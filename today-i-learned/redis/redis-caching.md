---
description: >-
  Redis는 빠른 속도, 다양한 데이터 구조 지원, 확장성, 간단한 설정, 그리고 강력한 커뮤니티를 통해 캐싱 시스템에 최적화된 솔루션이다.
  특히 대규모 트래픽 처리와 실시간 데이터 요구사항이 있는 애플리케이션에서 매우 효과적이다.
---

# 🧠 Redis : Caching

## Concept

Redis는 오픈 소스, 인메모리 데이터 스토어로, 주로 \
캐싱, 메시지 브로커, 세션 저장소 등의 목적으로 사용된다. \
캐싱은 데이터베이스나 API의 응답 속도를 높이고, \
서버 부하를 줄이기 위해 자주 사용되는 데이터를 메모리에 저장하는 방식이다.

Redis를 캐시로 사용할 때는 데이터 읽기와 쓰기 속도가 빠르고, \
다양한 데이터 구조를 지원하며, 스케일링도 가능하다.

## Pros and Cons

### **Pros**

* **고속 처리**: 인메모리 기반으로 데이터 처리 속도가 매우 빠르다.
* **다양한 데이터 구조**: 문자열, 리스트, 셋, 해시, 정렬된 셋 등의 자료형을 지원한다.
* **확장성**: Redis 클러스터링 및 복제를 통해 확장성을 제공한다.
* **유연성**: TTL(Time-To-Live)을 설정해 데이터를 자동으로 만료시킬 수 있다.
* **비용 효율성**: 자주 조회되는 데이터를 캐싱해 DB나 API 호출을 줄인다.

### **Cons**

* **데이터 영속성 부족**: 메모리 기반이므로, 서버가 종료되면 데이터가 유실될 수 있다.
* **메모리 의존**: 메모리를 많이 사용하는 데이터는 비용이 증가할 수 있다.
* **복잡한 관리**: 클러스터링 구성이나 데이터 정합성 유지가 어려울 수 있다.

## Useful Information

* **TTL 설정**: 각 데이터에 만료 시간을 설정해 불필요한 메모리 사용을 방지한다.
* **LRU 알고리즘**: 캐시 공간 초과 시, 오래된 데이터를 자동으로 제거하는 Least Recently Used 알고리즘 활용.
* **Redis 버전**: 특정 기능은 최신 버전에서만 제공되므로, 프로젝트에 맞는 Redis 버전을 선택한다.
* **보안**: Redis는 기본적으로 인증과 암호화가 활성화되어 있지 않으므로, ACL 설정과 TLS를 적극적으로 활용한다.

## Annotation-Based Caching

```kotlin
import org.springframework.cache.annotation.Cacheable
import org.springframework.cache.annotation.CacheEvict
import org.springframework.stereotype.Service

@Service
class UserService(private val userRepository: UserRepository) {

    // 캐싱: userId를 키로 메서드 결과를 저장
    @Cacheable("users")
    fun getUserById(userId: Long): User {
        println("Fetching user from database...")
        return userRepository.findById(userId).orElseThrow { RuntimeException("User not found") }
    }

    // 캐시 삭제: 사용자 업데이트 후 해당 키와 연관된 캐시 제거
    @CacheEvict("users")
    fun updateUser(user: User) {
        println("Updating user in database...")
        userRepository.save(user)
    }
}
```

## Manual Caching

```kotlin
import org.springframework.stereotype.Service
import redis.clients.jedis.Jedis

@Service
class ManualCacheService {

    private val jedis = Jedis("localhost", 6379)

    // 캐시에서 키를 사용해 값을 가져오기
    fun getCachedValue(key: String): String? {
        return jedis.get(key)
    }

    // 캐시에 값을 저장하고 TTL(유효 기간) 설정
    fun setCachedValue(key: String, value: String, ttl: Int) {
        jedis.set(key, value)
        jedis.expire(key, ttl)
    }

    // 캐시에서 키를 사용해 값 삭제
    fun deleteCachedValue(key: String) {
        jedis.del(key)
    }
}
```

## Redis Configuration in Kotlin

```kotlin
import org.springframework.cache.annotation.EnableCaching
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory
import org.springframework.data.redis.core.RedisTemplate

@Configuration
@EnableCaching
class RedisConfig {

    // Redis 연결 팩토리 설정
    @Bean
    fun redisConnectionFactory(): LettuceConnectionFactory {
        return LettuceConnectionFactory("localhost", 6379)
    }

    // RedisTemplate 설정: Redis와 상호작용을 위한 템플릿
    @Bean
    fun redisTemplate(): RedisTemplate<String, Any> {
        val template = RedisTemplate<String, Any>()
        template.setConnectionFactory(redisConnectionFactory())
        return template
    }
}
```

## Conclusion

* `@Cacheable`과 `@CacheEvict`를 사용해 선언적으로 캐싱을 간단하게 구현할 수 있다.
* `Jedis`를 이용한 수동 캐싱은 더 세밀한 제어가 필요할 때 유용하긴 하지만, \
  특별한 경우가 아니라면 어노테이션 방식을 사용하는 것이 유지보수에 더 유리하다고 생각한다.
* 캐싱은 성능개선에 유리한 만큼 사용되는 솔루션을 잘 알고 쓰면 더 좋은 것 같다.
