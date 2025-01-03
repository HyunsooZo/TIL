---
description: 이 포스팅에서는 에그리거트가 무엇인지 이해하고, 이를 실제 코드에 어떻게 적용하는지 살펴볼 것이다.
---

# 🪨 DDD : Aggregate

## Aggregate ?

**에그리거트**는 도메인 주도 설계(DDD)에서 데이터 변경을 위해 \
단일 단위로 처리되는 도메인 객체들의 클러스터를 의미한다. \
에그리거트는 경계 내에서 일관성을 보장하며, 종종 여러 엔티티와 값 객체를 캡슐화한다.

## Importance of Aggregates

* **일관성:** 경계 내의 모든 불변성을 보장한다.
* **트랜잭션 경계:** 단일 트랜잭션에서 함께 수정될 수 있는 것을 정의한다.
* **의존성 감소:** 도메인 모델의 다른 부분 간의 의존성을 최소화한다.

## Design Principles for Aggregates

1. **단일 책임:** 각 에그리거트는 명확한 책임을 가져야 한다.
2. **루트 엔티티:** 에그리거트는 나머지 구성요소에 대한 접근을 제어하는 루트 엔티티를 가진다.
3. **캡슐화:** 루트만을 통해 내부 상태에 접근하도록 제한한다.
4. **경계 문맥 정렬:** 에그리거트는 자신이 속한 경계 문맥과 일치해야 한다.

## Example Code

아래 코드는 실제 내 개인프로젝트에서 사용중인 Post라는 도메인 에그리거트를 정의한 예제이다. \
이 코드는 **Post** 객체가 에그리거트 루트로서 다른 구성요소를 캡슐화하고 있음을 보여준다.

### Post Class

```java
@Getter
public class Post {
    private final Long seq;
    private final PostUserInfo postUserInfo;
    private final PostInfo postInfo;
    private final Categories category;
    private final LocalDateTime createdAt;
    private final LocalDateTime updatedAt;
    private String isPublic;
    private Badge badge;
    private Long commentCount;

    @Builder
    public Post(
            Long seq,
            PostUserInfo postUserInfo,
            PostInfo postInfo,
            Categories category,
            String isPublic,
            Long commentCount,
            Badge badge,
            LocalDateTime createdAt,
            LocalDateTime updatedAt
    ) {
        this.seq = seq;
        this.postUserInfo = postUserInfo;
        this.postInfo = postInfo;
        this.category = category;
        this.isPublic = isPublic;
        this.commentCount = commentCount;
        this.badge = badge;
        this.createdAt = createdAt;
        this.updatedAt = updatedAt;
    }

    public static Post of(
            PostUploadCommand postUploadCommand,
            User user
    ) {
        PostInfo postInfo = PostInfo.of(
                postUploadCommand.title(),
                postUploadCommand.content(),
                user.getCountry(),
                user.getRegion()
        );

        PostUserInfo postUserInfo = PostUserInfo.builder()
                .userSeq(user.getSeq())
                .profileImage(user.getImageUrl())
                .nickname(user.getNickname())
                .build();

        return Post.builder()
                .postUserInfo(postUserInfo)
                .postInfo(postInfo)
                .category(postUploadCommand.category())
                .isPublic(postUploadCommand.isPublic() ? "Y" : "N")
                .commentCount(0L)
                .build();
    }

    public void updateCommentCount() {
        this.commentCount++;
    }

    public void updateIsPublic(String isPublic) {
        this.isPublic = isPublic;
    }

    public void updateContent(String content) {
        this.postInfo.updateContent(content);
    }

    public void updateTitle(String title) {
        this.postInfo.updateTitle(title);
    }

    public void delete() {
        this.getPostInfo().delete();
    }

    public Long getUserSeq() {
        return this.postUserInfo.getUserSeq();
    }

    public void increaseViewCount() {
        this.postInfo.increaseViewCount();
    }

    public String getTitle() {
        return this.postInfo.getTitle();
    }

    public String getContent() {
        return this.postInfo.getContent();
    }

    public String getUserProfileImage() {
        return this.postUserInfo.getProfileImage();
    }

    public String getUserNickname() {
        return this.postUserInfo.getNickname();
    }

    public String getCountryName() {
        return this.postInfo.getCountry().name();
    }

    public String getRegion() {
        return this.postInfo.getRegion();
    }

    public Long getViewCount() {
        return this.postInfo.getViewCount();
    }

    public Long getLikeCount() {
        return this.postInfo.getLikeCount();
    }

    public void updateBadge(Badge badge) {
        if (badge == null) {
            throw new PostException(PostErrorCode.BADGE_NOT_FOUND);
        }
        this.badge = badge;
    }

    public PostStatus getStatus() {
        return this.postInfo.getStatus();
    }
}
```

## Role of Post as an Aggregate Root

* **캡슐화:** \
  `Post`는 `PostInfo`와 `PostUserInfo`를 포함하며, \
  이들을 외부에 노출하지 않고 루트를 통해서만 접근 가능하게 한다.
* **트랜잭션 경계:** \
  `Post`의 모든 변경사항은 하나의 트랜잭션 내에서 처리된다.
* **일관성:** \
  `updateBadge()` 메서드에서는 `Badge`의 유효성을 확인하여 일관성을 유지한다.

## Key Components of Aggregates

#### Value Objects: PostInfo, PostUserInfo

* `PostInfo`와 `PostUserInfo`는 값을 보유하는 객체로, \
  `equals()`와 `hashCode()`를 통해 동등성을 보장한다.
* `Post`는 고유 식별자인 `seq`를 가진 도메인 모델로, 에그리거트 루트 역할을 한다.

## Considerations

* &#x20;너무 큰 에그리거트는 성능 저하를 유발할 수 있다.
* 에그리거트 외부에서 내부 데이터를 직접 접근하지 않도록 주의해야 한다.
* 중요한 비즈니스 규칙은 에그리거트 내부에서 처리되어야 한다.
