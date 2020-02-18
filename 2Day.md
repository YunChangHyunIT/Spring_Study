# 2Day

## DI(Dependency injection)
- 의존 주입
- 어떠한 객체에 의존하지 않고 분리해서 여기저기서 사용하게 할 수 있는것
  - 내장배터리를 사용하는 자동차장난감, 따로 배터리를 사용할 수 있는 로봇 장난감이 있다고 가정
  - 자동차장난감은 배터리가 다 달면 새로 사야하지만 로봇은 배터리만 바꿔 껴주면 된다.

### DI 예제
- 성적표 프로그램이 있다고 가정
  - 입력, 출력, 삭제, 업데이트 등의 기능이 있다.
- 모든 기능에는 StudentDao(Data Access Ojbect)라는 데이터베이스와 통신을 위한 객체가 필요하다.
- 이 객체를 각각의 기능안에서 만들어주는 것이아니고 하나를 만들어서 각각의 객체들에게 주입해준다.

```java
// applicationContext.xml 파일안에서 객체를 생성할 때 Dao객체를 주입
<bean id="studentDao" class="ems.member.dao.StudentDao"></bean>
<bean id="registerService" class="ems.member.service.StudentRegister">
  <constructor-arg ref="studentDao"></constructor-arg>  // StudentRegister를 만들때 StudentDao를 참조한다. (생성자와 비슷한 역할)
</bean>
```
