---
description: >-
  스프링에서 @Transactional은 선언만 하면 트랜잭션이 알아서 잘 동작한다고 생각하기 쉽다. 하지만 실제로는 메서드 접근 제어자,
  호출 방식, 프록시 메커니즘 등에 따라 트랜잭션이 동작하지 않을 수 있다.  그 대표적인 사례가 바로 private 메서드에
  @Transactional을 선언한 경우이다.
---

# ❌ AOP : @Transactional on private = ❌

## Why it doesn't work

`@Transactional`은 **Spring AOP 기반**으로 동작하며, \
이 AOP는 **프록시 객체를 생성**하여 해당 메서드 호출을 감싼다.\
즉, **프록시 객체를 통해 메서드를 호출해야만 트랜잭션이 적용**된다.

> 같은 클래스 내부에서 private 메서드를 호출하면?\
> 프록시를 거치지 않음 → 트랜잭션 어드바이스가 적용되지 않음

***

## Proxy type and scope

| 프록시 타입            | 설명                                              | AOP 적용 범위                                          |
| ----------------- | ----------------------------------------------- | -------------------------------------------------- |
| JDK Dynamic Proxy | 인터페이스 기반 프록시. 해당 인터페이스를 구현한 **public 메서드만** 적용됨 | `public` 메서드만                                      |
| CGLIB             | 클래스 상속 기반 프록시. 인터페이스 없어도 가능                     | `public`, `protected`, default 접근 가능❌ `private` 불가 |

즉, CGLIB이라 해도 `private`은 절대 안 됨.

***

## Practice

```java
@Slf4j
@RequiredArgsConstructor
@Service
public class SelfInvocation {

    private final MemberRepository memberRepository;

    public void outerSaveWithPrivate(Member member) {
        saveWithPrivate(member); // 👈 직접 호출이므로 프록시를 거치지 않음
    }

    @Transactional
    private void saveWithPrivate(Member member) {
        memberRepository.save(member);
        throw new RuntimeException("rollback test");
    }
}
```

```java
@SpringBootTest
class SelfInvocationTest {

    @Autowired
    private SelfInvocation selfInvocation;

    @Autowired
    private MemberRepository memberRepository;

    @Test
    void outerSaveWithPrivate() {
        try {
            selfInvocation.outerSaveWithPrivate(new Member("test"));
        } catch (RuntimeException ignored) {}

        var members = memberRepository.findAll();
        assertThat(members).hasSize(1); // ❌ 롤백 안됨
    }
}
```

***

## Solution 1: Split into separate class

```java
@Service
public class OuterService {
    private final InnerService innerService;

    public void run() {
        innerService.innerTransactionalLogic();
    }
}

@Service
public class InnerService {
    @Transactional
    public void innerTransactionalLogic() {
        // 트랜잭션 적용됨
    }
}
```

프록시 객체 간 호출이므로 트랜잭션 적용됨

***

## Solution 2: Inject self proxy

```java
@Service
public class SelfCallingService {

    private final SelfCallingService selfProxy;

    public SelfCallingService(@Lazy SelfCallingService selfProxy) {
        this.selfProxy = selfProxy;
    }

    public void outer() {
        selfProxy.transactionalInner(); // 프록시를 통한 호출
    }

    @Transactional
    public void transactionalInner() {
        // 트랜잭션 적용됨
    }
}
```

**순환 의존성 문제**를 야기할 수 있어 실무에선 권장되지 않음.

***

## Solution 3: Use AspectJ

Spring AOP가 아닌 **AspectJ**를 사용하면 같은 클래스 내 메서드 호출도 AOP 적용 가능함. \
하지만 복잡도가 올라가고, build config (예: weaving) 손봐야 해서 실무에선 잘 안 씀.

***

## Conclusion

* `@Transactional`은 프록시 기반으로 동작하므로, 반드시 **public 메서드**이고 **프록시를 통해 호출**되어야 적용된다.
* `private`, `self-invocation`(자기 자신 호출)은 적용되지 않으며, \
  이를 피하려면 구조적 분리 혹은 프록시 주입 등의 대안이 필요하다.
