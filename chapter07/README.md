Spring Data JPA Auditing 에 대해서 좀 더 알아보기
================================================
   
   
   
예시를 가정하고서 들어가기
-------------------------
   
   

1. 게시글을 작성할 때는 작성 일자가 필요하고, 수정을 할 때는 수정 일자가 필요하다.
2. 회원가입을 할경우 회원가입 일자가 필요하고, 회원 정보 수정을 하게 될 경우 수정 일자가 필요하다.

#### 즉, 최초 생성된 날짜와, 수정된 날짜 모두 필요하다는 것!

```java
@Table(name = members)
@Entity
public class Member {
	...
    
    @Column
    private Datetime createdAt;
    
    @Column
    private Datetime modifiedAt;
    
    @Column
    private String createdBy;
    
    @Column
    private String modifiedBy;
    
	public Member(..., String createdBy, String modifiedBy) {
		...
		this.createdAt = Datetime.now();
        this.modifiedAt = Datetime.now();
        this.createdBy = createdBy;
        this.modifiedBy = modifiedBy;
    }
    
    public modify(..., String modifiedBy) {
    	...
        this.modifiedAt = Datetime.now();
        this.modifiedBy = modifiedBy;
    }
}
```
   
위의 코드의 예처럼 생성일자와 수정일자를 setter 또는 생성자를 통해서 값을 설정이 가능!   그러나 이와 같이 지속적으로 하다보면 귀찮게 되고, 누군가가 알아서 딱딱 지정해줬으면 좋겠다는 생각이 들게 된다!
   
### 그 역할을 해줄 수 있는 것이 바로 => EnableJpaAuditing
   
EnableJpaAuditing 을 사용하려면 다음과 같이 의존성 주입이 필요하다!   
```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}
```

그 후 Application 클래스에 @EnableJpaAuditing 어노테이션을 추가!   
```java
@EnableJpaAuditing // 추가
@SpringBootApplication
public class ch07Application {
    ...
}
```

이제 Entity에 생성날짜에 해당하는 컬럼필드와 수정날짜에 해당하는 컬럼필드에 @CreateDate 어노테이션과, @LastModifiedDate 어노테이션을 붙여야 한다!   
그 전에 먼저 @CreateDate 어노테이션과, @LastModifiedDate 어노테이션부터 알아보자!!!   

#### @CreateDate 어노테이션?   
* 엔티티의 생성 날짜를 저장하기 위해 사용
* 엔티티 클래스의 필드에 @CreateDate 어노테이션을 추가하면, 해당 필드는 엔티티가 생성될 때 자동으로 현재 날짜 및 시간을 설정!   

### @LastModifiedDate 어노테이션?
* 엔티티의 마지막 수정 날짜를 저장하기 위해 사용
* 엔티티 클래스의 필드에 @LastModifiedDate 어노테이션을 추가하면, 해당 필드는 엔티티가 수정될 때 자동으로 현재 날짜 및 시간으로 업데이트!




* * *
### 이제 어떤 식으로 작성할지에 대해서 알아보기   

대부분의 엔티티는 생성날짜와 수정날짜는 필수로 사용되는 필드일 것이다!   
앞서 처음에 가정을 했던 예처럼 말이다.   
따라서 생성날짜와 수정날짜를 별도의 Entity 클래스로 분리를 하고, 다른 Entity 클래스에서 이를 상속받아서 사용을 하면 중복 코드를 없앨 수 있다!!   

별도의 Entity 클래스는 다음과 같이 작성할 수 있다!   
```java
@Getter
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
public class BaseEntity {

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```
