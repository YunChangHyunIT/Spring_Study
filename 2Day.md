# 2Day

## DI(Dependency injection)
- 의존 주입
- 어떠한 객체에 의존하지 않고 분리해서 여기저기서 사용하게 할 수 있는것
  - 내장배터리를 사용하는 자동차장난감, 따로 배터리를 사용할 수 있는 로봇 장난감이 있다고 가정
  - 자동차장난감은 배터리가 다 달면 새로 사야하지만 로봇은 배터리만 바꿔 껴주면 된다.

### DI 예제
- 성적표 프로그램이 있다고 가정
  - 입력, 출력, 삭제, 업데이트 등의 기능을 가지는 인터페이스 객체가 있다.
- 모든 기능에는 StudentDao(Data Access Ojbect)라는 데이터베이스와 통신을 위한 객체가 필요하다.
- 이 객체를 각각의 기능안에서 만들어주는 것이아니고 하나를 만들어서 각각의 객체들에게 주입해준다.
- 생성자, setter, list, map을 통한 주입이 있다.

```java
// applicationContext.xml 파일안에서 객체를 생성할 때 Dao객체를 주입
<bean id="studentDao" class="ems.member.dao.StudentDao"></bean>

<bean id="registerService" class="ems.member.service.StudentRegister">
  <constructor-arg ref="studentDao"></constructor-arg>  
  // StudentRegister를 만들때 StudentDao를 참조한다. (생성자를 통한 주입)
  
  // <property name="userId" value="scott" /> // setUserId라는 setter가 있다고 가정할 때,
  // setUserId의 set을 빼고 U를 소문자로 바꾼 값을 property name으로 그 인자에 들어갈 값을 value에 적는다 (setter를 이용한 주입)
  
  // <property name="userId">
  //    <list>
  //       <value>cat</value>
  //       <value>dog</value>
  //       <value>bird</value>
  //    </ist>
  //  </property> // setUserId가 list를 인자로 갖는 setter일 때, 
  // 마찬가지로 setUserId의 set을 빼고 U를 소문자로 바꾼 값을 property name에 넣은후 리스트의 값을든 <list> <value> 로 넣는다.
    
  // <property name="userId"> 
  //    <map>
  //      <entry>
  //        <key>
  //           <value>Yun</value>
  //        </key>
  //           <value>ChangHyun</value>
  //      </entry>
  //    </map>
  //  </property>  // setUserId가 map을 인자로 갖는 setter일 때,
  // map 키와 값을 <entry>로 묶고 키는 <key>로 묶고 <value>에 키 값을 입력, 값은 <value>에 입력해준다.            
</bean>
```
## 빈(Bean)의 범위
- 싱글톤(Singleton)
  - 스프링 컨테이너에서 생성된 빈(Bean)객체의 경우 기본적으로 한 개만 생성이 된다.
  - getBean() 메소드로 호출될 때 동일한 객체가 반환된다.
  ![싱글톤](https://user-images.githubusercontent.com/58713853/74734779-9a8f3200-5292-11ea-9177-6bcb5def62f2.PNG)
- 프로토타입(Prototype)
  - 싱글톤 범위와 반대의 개념
  - 스프링 설정 파일에서 빈(Bean)객체를 정의할 때 scope속성을 명시해 주어야한다.

```java
<bean id="dependencyBean" class="scope.ex.DependencyBean" scope="prototype">
```
