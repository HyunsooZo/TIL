---
description: Spring Data JPA는 어떻게 새로운 Entity를 구별할까?
---

# 🆕 JPA : isNew

## isNew

Spring Data JPA에서 엔티티가 새로운 것인지 판단하는 것은 `isNew()` 메서드를 통해 이루어진다. \
이는 `persist()`와 `merge()` 중 어떤 연산을 수행할지 결정하는 중요한 기준이 된다.

### Default Behavior of `isNew()`

`JpaEntityInformation` 인터페이스의 `isNew(T entity)` 메서드가 엔티티의 신규 여부를 판단한다. \
일반적으로 `JpaMetamodelEntityInformation` 클래스가 이를 구현하며, 다음과 같은 기준을 따른다.

### When There Is No `@Version`

* `@Id` 필드의 값을 확인한다.
* 기본 타입(primitive)이라면 `0`인지 확인하고, 그렇지 않다면 `null` 여부를 확인한다.

### When There Is `@Version`

* `@Version` 필드가 기본 타입(primitive)이라면 기존 방식 그대로 `@Id` 값을 확인한다.
* `@Version` 필드가 `Wrapper Class`라면 해당 필드의 값이 `null`인지 확인한다.

```java
@Override
public boolean isNew(T entity) {
    if (versionAttribute.isEmpty()
        || versionAttribute.map(Attribute::getJavaType).map(Class::isPrimitive).orElse(false)) {
        return super.isNew(entity);
    }

    BeanWrapper wrapper = new DirectFieldAccessFallbackBeanWrapper(entity);

    return versionAttribute.map(it -> wrapper.getPropertyValue(it.getName()) == null).orElse(true);
}
```

***

## When Assigning ID Manually

JPA에서는 보통 `@GeneratedValue`를 사용하여 ID를 자동 생성한다. \
그러나 직접 ID를 할당하는 경우, `JpaMetamodelEntityInformation`의 기본 동작으로는 \
새로운 엔티티로 인식되지 않는다.

```java
@Override
public boolean isNew(T entity) {
    ID id = getId(entity);
    Class<ID> idType = getIdType();

    if (!idType.isPrimitive()) {
        return id == null;
    }

    if (id instanceof Number) {
        return ((Number) id).longValue() == 0L;
    }

    throw new IllegalArgumentException(String.format("Unsupported primitive id type %s", idType));
}
```

이처럼 **ID가 직접 할당되면 `isNew()`가 false로 판별되므로,** \
&#xNAN;**`persist()`가 아닌 `merge()`가 호출될 가능성이 있다.**

#### **해결 방법** 이 문제를 해결하려면 엔티티에서 `Persistable<T>` 인터페이스를 구현하여 `JpaPersistableEntityInformation`이 동작하도록 해야 한다.

```java
import org.springframework.data.domain.Persistable;
import jakarta.persistence.*;

@MappedSuperclass
public abstract class BaseEntity<ID> implements Persistable<ID> {
    @Id
    private ID id;

    @Transient
    private boolean isNew = true;

    @PostLoad
    void markNotNew() {
        this.isNew = false;
    }

    @Override
    public boolean isNew() {
        return isNew;
    }

    @Override
    public ID getId() {
        return id;
    }
}
```

이제 `isNew()`를 직접 구현하여 새로운 엔티티 여부를 올바르게 판별할 수 있다.

***

## Why `isNew()` Matters ?&#x20;

Spring Data JPA의 `SimpleJpaRepository`는 `save()` 메서드에서 `isNew()`를 사용하여 `persist()` 또는 `merge()`를 결정한다.

```java
@Override
@Transactional
public <S extends T> S save(S entity) {

    Assert.notNull(entity, "Entity must not be null");

    if (entityInformation.isNew(entity)) {
        entityManager.persist(entity);
        return entity;
    } else {
        return entityManager.merge(entity);
    }
}
```

### **What Happens If ID Is Manually Assigned?**

* `isNew()`가 `false`로 판별되므로 `merge()`가 호출된다.
* 이 경우, 새로운 엔티티임에도 불구하고 먼저 **DB를 조회**하는 불필요한 과정이 발생한다.
