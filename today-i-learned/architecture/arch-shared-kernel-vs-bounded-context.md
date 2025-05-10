---
description: >-
  회사에서 모빌리티(고속 + 시외 (2개 사)) 통합 프로젝트를 진행하며 서비스가 커지고 도메인이 많아질수록, '이 개념은 누구 거지?',
  '같은 user인데 도메인마다 다르게 쓰이는데?' 같은 문제가 발생했다. DDD(를 지향하는) 구조를 가지고 있기 때문에  DDD에서
  마주하는 가장 흔한 도메인 고민 중 하나일 것이라 생각한다.
---

# 🤝 Arch : Shared Kernel vs Bounded Context

## Shared Kernel?

> 둘 이상의 서브도메인이 동일한 모델을 공유해야 할 때, 그 공통 모델을 별도로 추출해서 공유하는 것.

* 서로 밀접하게 협업하는 팀이나 모듈 사이에서 사용
* 모델을 변경할 땐 양쪽 팀/모듈 간의 **명확한 커뮤니케이션**이 필요
* 공유 영역이기 때문에 **테스트, 문서화, 안정성 확보**가 중요



* `User`, `Coupon`, `Terminal`, `BusCompany` 같은 **여러 도메인에서 참조되는 개념**
* 단일 DB를 쓰는 모놀리식 시스템에서는 **`shared-domain` 모듈**로 패키지 분리해서 관리

```java
// shared-kernel
package com.shared.model;

public class Terminal {
    private final String terminalId;
    private final String terminalName;
    private final String regionCode;

    public Terminal(String terminalId, String terminalName, String regionCode) {
        this.terminalId = terminalId;
        this.terminalName = terminalName;
        this.regionCode = regionCode;
    }

    public String terminalId() { return terminalId; }
    public String terminalName() { return terminalName; }
    public String regionCode() { return regionCode; }
}
```

***

## Bounded Context?

> 도메인의 의미와 용법을 "문맥(Context)" 단위로 나누어 서로 독립적으로 모델링하는 설계 경계

* 도메인마다 용어가 달라지거나, 같은 용어라도 의미가 달라질 때 유용
* 컨텍스트마다 `User`, `Coupon`이 다르게 정의되어도 무방함
* 컨텍스트 간 통신은 보통 **이벤트**, **API**, **중간 매핑 객체** 등을 통해 진행

```java
// ticketing : User
package com.ticketing.domain;

public class TicketingUser {
    private final Long userSeq;
    private final boolean blacklisted;

    public TicketingUser(Long userSeq, boolean blacklisted) {
        this.userSeq = userSeq;
        this.blacklisted = blacklisted;
    }

    public boolean isBlacklisted() {
        return blacklisted;
    }
}

// coupon : User
package com.coupon.domain;

public class CouponUser {
    private final Long userSeq;
    private final String membershipGrade;

    public CouponUser(Long userSeq, String membershipGrade) {
        this.userSeq = userSeq;
        this.membershipGrade = membershipGrade;
    }

    public boolean isPremium() {
        return "VIP".equals(membershipGrade);
    }
}
```

***

## Design Criteria

<table><thead><tr><th width="262.67578125">상황</th><th width="143.48828125">Shared Kernel</th><th>Bounded Context</th></tr></thead><tbody><tr><td>공통 개념이 자주 같이 바뀐다</td><td> 적합</td><td>분리할수록 번거로움</td></tr><tr><td>도메인마다 개념이 다르게 쓰인다</td><td>위험</td><td>컨텍스트 분리 추천</td></tr><tr><td>팀이 명확히 나뉘고 협업 부담이 있다</td><td>위험</td><td>각자 책임질 수 있음</td></tr><tr><td>시스템이 MSA 또는 분산 구조다</td><td>공유 어려움</td><td>독립 배포 구조에 적합</td></tr></tbody></table>

***

## Tips For Design

* Shared Kernel을 도입할 땐 **정말 변경이 거의 없고, 팀 간 조율이 가능한 경우에만**
* 그 외에는 **Bounded Context로 분리하고, 통신을 명확하게 정의**하는 게 훨씬 안전함
* 특히 DTO, Entity, DomainModel을 혼용할 때는 **의미 충돌을 반드시 피해야 함**

***

## Summary

| 개념              | 설명                          | 특징              |
| --------------- | --------------------------- | --------------- |
| Shared Kernel   | 여러 도메인 간에 공통으로 사용하는 모델      | 조율 필요, 안정화 필수   |
| Bounded Context | 각 도메인이 자신의 문맥에 맞게 독립적으로 모델링 | 의미 충돌 방지, 통신 필요 |

모듈을 잘 나누는 것도 중요하지만, \
그보다 더 중요한 건 **경계를 어떻게 정하고 책임을 어디까지 줄 것인지**다.\
DDD의 핵심은 모델링이 아니라 _의도를 명확히 나누는 &#xAC83;_&#xC774;다.

***

## Conclusion

처음엔 도메인마다 모델을 따로 두는 게 중복처럼 느껴졌는데, \
오히려 분리하고 나니 각 도메인에 집중할 수 있고 변경 부담도 확 줄었음. \
특히 실무에서 API에 따라 사용자 구조가 다를 땐 Bounded Context 분리가 진짜 체감되는 설계 전략임.

공유는 신중하게, 분리는 명확하게. 결국 이게 실무에서 살아남는 모델링의 기준 아닐까 생각된다.
