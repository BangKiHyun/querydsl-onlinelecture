# Querydsl

## Querydsl vs JPQL

- Querydsl은 JPQL 빌더
- JPQL: 문자(실행 시점 오류), Querydsl: 코드(컴파일 시점 오류)
- JPQL: 파라미터 바인딩 직접, Querydsl: 파라미터 바인딩 자동 처리

</br >

## 기본 Q-Type 활용

### Q클래스 인스턴스 사용하는 2가지 방법

~~~java
QMember member = new QMember("m"); //별칭 직접 지정
QMember member = QMember.member; //기본 인스턴스 사용
~~~

</br >

## 검색 조건 쿼리

### 검색 조건은 `.and()`,`. or()`를 메서드 체인으로 연결할 수 있음

~~~java
public void search() throws Exception {
    final Member findMember = queryFactory
            .selectFrom(member) //select, from 합칠 수 있음
            .where(member.username.eq("member1")
                    .and(member.age.eq(10)))
            .fetchOne();

    assertThat(findMember.getUsername()).isEqualTo("member1");
}
~~~

</br >

### JPQL이 제공하는 검색 조건 예제

~~~java
member.username.eq("member1") // username = 'member1'
member.username.ne("member1") //username != 'member1'
member.username.eq("member1").not() // username != 'member1'

member.username.isNotNull() //이름이 is not null

member.age.in(10, 20) // age in (10,20)
member.age.notIn(10, 20) // age not in (10, 20)
member.age.between(10,30) //between 10, 30

member.age.goe(30) // age >= 30
member.age.gt(30) // age > 30
member.age.loe(30) // age <= 30
member.age.lt(30) // age < 30

member.username.like("member%") //like 검색
member.username.contains("member") // like ‘%member%’ 검색
member.username.startsWith("member") //like ‘member%’ 검색
~~~

</br >

## 결과 조회

- fetch(): 리스트 조회, 데이터 없으면 빈 리스트 반환
- fetchOne(): 단 건 조회
  - 결과가 없으면: `null`
  - 둘 이상이면: `com.querydsl.core.NonUniqueResultException`
- fetchFirst(): `limit(1).fetchOne()`
- fetchResults(): 페이징 정보 포함, total count 쿼리 추가 실행
- fetchCount(): count 쿼리로 변경해서 count 수 조회

