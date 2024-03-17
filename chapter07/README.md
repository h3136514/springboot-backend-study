Spring Data JPA Auditing 에 대해서 좀 더 알아보기
================================================

예시를 가정하고서 들어가기
-------------------------

1. 게시글을 작성할 때는 작성 일자가 필요하고, 수정을 할 때는 수정 일자가 필요하다.
2. 회원가입을 할경우 회원가입 일자가 필요하고, 회원 정보 수정을 하게 될 경우 수정 일자가 필요하다.

## 즉, 최초 생성된 날짜와, 수정된 날짜 모두 필요하다는 것!

```
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
