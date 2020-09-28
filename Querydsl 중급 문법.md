# Querydsl 중급 문법

## 프로젝션과 결과 반환

- 프로젝션: select 대상 지정

</br >

### 프로젝션 대상이 하나면 타입을 명확하게 지정 가능

~~~java
final List<String> result = queryFactory
        .select(member.username)
        .from(member)
        .fetch();
~~~

</br >

### 프로젝션 대상이 둘 이상이면 튜플이나 DTO로 조회

### 튜플 조회

~~~java
    final List<Tuple> result = queryFactory
            .select(member.username, member.age)
            .from(member)
            .fetch();
~~~

</br >

### DTO 조회(권장)

### 결과를 DTO로 반환하는 3가지 방법

1. ### 프로퍼티 접근 - Setter

   ~~~java
       final List<MemberDto> result = queryFactory
               .select(Projections.bean(MemberDto.class,
                       member.username,
                       member.age))
               .from(member)
               .fetch();
   ~~~

   

2. ### 필드 직접 접근

   ~~~java
       final List<MemberDto> result = queryFactory
              .select(Projections.fields(MemberDto.class,
                       member.username,
                       member.age))
              .from(member)
              .fetch();
   ~~~

   

3. ### 생성자 접근

   ~~~java
       final List<MemberDto> result = queryFactory
              .select(Projections.constructor(MemberDto.class,
                       member.username,
                       member.age))
              .from(member)
              .fetch();
   ~~~

</br >

### 별칭이 다르다면?

- **프로퍼티나, 필드 접근 생성** 방식에서 **필드 명이 다르면 null값**이 들어감
- 생성자 방식은 type으로 확인하기 때문에 이름이 달라도 타입이 같으면 값이 들어감

### 해결방법

~~~java
    final List<UserDto> result = queryFactory
            .select(Projections.fields(UserDto.class,
                    member.username.as("name"), //필드명이 같아야 값이 들어감, 필드에 별칭 적용

                    ExpressionUtils.as(JPAExpressions //필드나 서브 쿼리에 별칭 적용
                            .select(memberSub.age.max())
                            .from(memberSub), "age")
            ))
            .from(member)
            .fetch();
~~~

- `필드명.as("적용할 별칭")`: 필드에 별칭 적용
- `ExpresstionUtils.as(source, alias)`: 필드나, 서브 쿼리에 별칭 적용

