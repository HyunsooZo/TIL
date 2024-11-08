---
description: 도메인과 엔티티를 분리하는 것은 소프트웨어 설계에서 중요한 결정 중 하나이다.
---

# 🍕 Arch : Domain-Entity Split

## Domain & Entity

### **Domain**&#x20;

시스템의 비즈니스 로직을 포함하고 있는 계층이다. \
도메인은 비즈니스 규칙을 표현하며, 애플리케이션의 핵심 가치를 담당한다. \
도메인은 문제 공간과 그에 대한 해결책을 포괄하며, 애플리케이션의 주요 비즈니스 프로세스를 포함한다.

### **Entity**

데이터베이스 테이블과 직접적으로 매핑되는 객체이다. \
데이터의 저장, 조회, 삭제 등 영속성 관련 작업에 주로 사용된다. \
엔티티는 주로 식별자를 가지며, 데이터베이스의 특정 레코드와 일대일로 대응된다.

## The reason for separating domain and entity

### **비즈니스 로직의 독립성 유지**&#x20;

도메인 계층이 영속성 프레임워크나 데이터베이스 구현에 종속되ㄷ지 않도록 한다. \
도메인이 엔티티에 종속되면 데이터베이스 스키마의 변경이 도메인 로직에 영향을 미칠 수 있다. \
이를 방지하면 비즈니스 로직을 더 쉽게 테스트하고 유지보수할 수 있게 된다.

### **모듈성 향상**

엔티티가 데이터베이스 변경이나 영속성 기술의 변경에 종속적일 때, \
도메인 로직이 영향을 받지 않도록 보장한다. \
이를 통해 데이터베이스와 도메인 계층이 서로 독립적으로 발전할 수 있으며, \
데이터베이스 변경으로 인한 영향을 최소화할 수 있다.

### **코드 가독성과 유지보수성**&#x20;

도메인 로직과 데이터베이스 로직을 분리함으로써 코드를 더 명확하게 이해할 수 있다. \
도메인 로직은 비즈니스 로직에만 집중할 수 있고, \
엔티티는 데이터베이스의 영속성 관리에만 집중할 수 있어 서로의 역할이 명확해진다.

### **테스트 용이성**&#x20;

도메인 계층이 엔티티와 분리되어 있으면 테스트 시 엔티티에 의존할 필요가 없다. \
이는 단위 테스트에서 데이터베이스 설정 없이 순수한 도메인 로직을 테스트할 수 있음을 의미한다.

## Benefits

그렇다면 도메인과 엔티티를 분리하면(결합도를 낮추면) 어떤 장점이 있을까?\
먼저 결합도란 두 개체 간의 상호 의존성을 나타낸다. \
도메인과 엔티티의 결합도를 낮추면 다음과 같은 이점을 얻을 수 있다:

### **변경 용이성**

도메인 로직이 엔티티에 덜 의존할수록, 데이터베이스 구조나 영속성 기술이 변경되더라도 \
비즈니스 로직에 미치는 영향이 줄어든다. \
이는 시스템 변경에 유연하게 대응할 수 있게 해준다.

### **테스트와 유지보수성 향상**

결합도를 낮추면 테스트할 때 데이터베이스 설정 없이도 도메인 로직을 독립적으로 \
테스트할 수 있어 테스트가 간편해진다. \
이로 인해 유지보수 시 코드의 의존성을 줄이고 리팩토링이 더 쉬워진다.

### **확장성**

결합도를 낮추면 애플리케이션 구조를 더 쉽게 확장할 수 있다. \
도메인 로직이 엔티티에 독립적일 때, 새로운 기능을 추가하거나 기존 기능을 확장할 때 더 적은 영향을 받는다.

### **재사용성**&#x20;

낮은 결합도를 유지하면 도메인 로직이 다른 프로젝트나 컨텍스트에서도 재사용될 수 있다. \
특정 영속성 기술에 의존하지 않기 때문에, 도메인 로직은 다양한 환경에서 활용될 수 있다.

## Considerations

### **불필요한 변환 비용**&#x20;

도메인과 엔티티를 분리할 때 DTO 변환 작업이 추가되어 성능 저하를 초래할 수 있다. \
하지만 이러한 비용은 유지보수성과 코드의 명확성에서 얻는 이점으로 _**충분히 상쇄될 수 있다.**_

### **일관성 유지**

도메인 객체와 엔티티 간의 일관성을 유지하기 위해 변환 로직이 명확해야 한다. \
이를 위해 변환 메서드를 일관성 있게 관리하는 것이 중요하다. (예 :`toDomain()` ..)&#x20;

## **Implementation**

### **DTO(Data Transfer Object) 사용**&#x20;

엔티티와 도메인 간의 데이터를 전송할 때 DTO를 활용하여 분리한다. \
DTO는 외부에 데이터를 전달하거나 데이터를 받을 때 사용되며, 도메인 로직에는 영향을 미치지 않는다.

### **도메인 모델을 VO로 구성**&#x20;

도메인에서 영속성과 상관없는 VO를 사용하여 비즈니스 로직을 처리한다. \
VO는 불변 객체로 도메인에서 특정 개념을 나타내며, 비즈니스 로직을 더욱 명확하게 표현할 수 있게 해준다.

### **서비스 계층**&#x20;

도메인 로직을 서비스 계층에 위치시켜 엔티티와의 상호작용을 간접적으로 처리한다. \
서비스 계층은 도메인 계층과 인프라 계층(엔티티) 사이에서 데이터를 변환하고 관리하는 중재 역할을 한다.

```java
// 도메인 
public record Order(
    String orderId, 
    List<Item> items, 
    OrderStatus status
) {
    public boolean isOrderValid() {
        return !items.isEmpty() && status != OrderStatus.CANCELLED;
    }
}

// 엔티티
@Entity
public class OrderEntity {
    @Id
    private String id;
    @OneToMany
    private List<ItemEntity> items;
    private String status;
}


public Order toDomain(OrderEntity entity) {
    return new Order(
        entity.getId(),
        entity.getItems().stream().map(ItemEntity::toDomain).toList(),
        OrderStatus.valueOf(entity.getStatus())
    );
}

public OrderEntity from(Order domain) {
    OrderEntity entity = new OrderEntity();
    entity.setId(domain.orderId());
    entity.setItems(domain.items().stream().map(Item::toEntity).toList());
    entity.setStatus(domain.status().name());
    return entity;
}
```

## 결론

도메인과 엔티티의 분리는 초기에는 더 많은 작업처럼 보일 수 있지만, \
장기적으로는 시스템의 유지보수성과 확장성을 크게 향상시킨다. \
도메인을 독립적으로 관리함으로써, 코드의 재사용성과 테스트 가능성을 높일 수 있다.&#x20;

궁극적으로 비즈니스 로직의 독립성은 애플리케이션의 품질을 높이고, \
코드의 가독성과 확장성을 확보할 수 있는 전략적 선택이다. \
결합도를 낮춤으로써 변화에 유연하게 대응할 수 있는 아키텍처를 구축할 수 있으며, \
이는 안정적이고 유지보수하기 쉬운 시스템을 만드는 데 필수적이다.

즉, 많은 책에서 DDD를 강조하고 도메인과 엔티티의 분리를 강조하는데에는 다 이유가 있다!