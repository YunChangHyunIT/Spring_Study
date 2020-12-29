# Summary9



## Service & DAO 객체 구현

- 기능을 구현하는 Service, 데이터베이스와 연동되는 DAO 객체를 구현해 볼 것이다.

- 회원 관리, 회원 등록, 로그인, 회원 가입 웹어플리케이션을 간단하게 구현할 것이다.

  

### 웹 어플리케이션의 일반적으로 프로그램 구조

- STS를 이용해서 프로젝트를 생성한다.

![1](https://user-images.githubusercontent.com/58713853/102459299-c59c0b00-4088-11eb-8b56-d299c93a9b68.PNG)

---



![2](https://user-images.githubusercontent.com/58713853/102459305-c634a180-4088-11eb-99f2-3ff2ab44f9f2.PNG)

---

![3](https://user-images.githubusercontent.com/58713853/102459306-c6cd3800-4088-11eb-951f-43a1e8deeb54.PNG)



### 한글 처리

- 서버를 실행해보면 글자가 깨지는 것을 알 수 있는데, web.xml에 인코딩필터 코드를 가져와서 해결할 수 있다.

![4](https://user-images.githubusercontent.com/58713853/102459311-c765ce80-4088-11eb-971a-283cfaa47c7e.PNG)



### 화면에 보일 html 만들기

- 메인에 보일 index.html, 로그인 화면인 login.html, 회원가입 화면인 memjoin.html을 간단히 만들어준다.

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>Main</h1>
	<a href="/lec17/resources/html/memJoin.html">JOIN</a> &nbsp;&nbsp; <a href="/lec17/resources/html/login.html">LOGIN</a>
</body>
</html>
```

```html
<!-- login.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>Login</h1>
	<form action="/lec17/memLogin" method="post">
		ID : <input type="text" name="memId" ><br>
		PW : <input type="password" name="memPw" ><br>
		<input type="submit" value="Login" >
	</form>
	<a href="/lec17/resources/html/memJoin.html">JOIN</a> &nbsp;&nbsp; <a href="/lec17/resources/html/index.html">MAIN</a>
</body>
</html>
```

```html
<!-- memJoin.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>Member Join</h1>
	<form action="/lec17/memJoin" method="post">
		ID : <input type="text" name="memId" ><br />
		PW : <input type="password" name="memPw" ><br />
		MAIL : <input type="text" name="memMail" ><br />
		PHONE : <input type="text" name="memPhone1" size="5"> -
				<input type="text" name="memPhone2" size="5"> -
				<input type="text" name="memPhone3" size="5"><br />
		<input type="submit" value="Join" >
		<input type="reset" value="Cancel" >
	</form>
	<a href="/lec17/resources/html/login.html">LOGIN</a> &nbsp;&nbsp; <a href="/lec17/resources/html/index.html">MAIN</a>
</body>
</html>
```



### JSP 파일 만들기

- 회원가입, 로그인이 정상적으로 이루어졌을 때 실행되는 memJoinOk.jsp, memLoginOk.jsp 만들기

```jsp
<!-- memJoinOk.jsp -->
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page session="false" %>
<html>
<head>
	<title>Home</title>
</head>
<body>
	<h1> memJoinOk </h1>
	ID : ${memId}<br />
	PW : ${memPw}<br />
	Mail : ${memMail} <br />
	Phone : ${memPhone} <br />
	
	<a href="/lec17/resources/html/memJoin.html"> Go MemberJoin </a>
</body>
</html>

```

```jsp
<!-- memLoginOk.jsp -->
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page session="false" %>
<html>
<head>
	<title>Home</title>
</head>
<body>
	<h1> memLoginOk </h1>
	ID : ${memId}<br />
	PW : ${memPw}<br />
	
	<a href="/lec17/resources/html/index.html"> Go Main </a>
</body>
</html>

```



### member 객체를 구현

- member에 해당하는 속성값들이 있다.

```java
// Member.java
package com.bs.lec17.member;

public class Member {
	private String memId;
	private String memPw;
	private String memMail;
	private String memPhone1;
	private String memPhone2;
	private String memPhone3;
	
	public String getMemId() {
		return memId;
	}
	public void setMemId(String memId) {
		this.memId = memId;
	}
	public String getMemPw() {
		return memPw;
	}
	public void setMemPw(String memPw) {
		this.memPw = memPw;
	}
	public String getMemMail() {
		return memMail;
	}
	public void setMemMail(String memMail) {
		this.memMail = memMail;
	}
	public String getMemPhone1() {
		return memPhone1;
	}
	public void setMemPhone1(String memPhone1) {
		this.memPhone1 = memPhone1;
	}
	public String getMemPhone2() {
		return memPhone2;
	}
	public void setMemPhone2(String memPhone2) {
		this.memPhone2 = memPhone2;
	}
	public String getMemPhone3() {
		return memPhone3;
	}
	public void setMemPhone3(String memPhone3) {
		this.memPhone3 = memPhone3;
	}
}

```



### 컨트롤러 객체 구현

- 기능을 하게 만들어주는 컨트롤러를 구현해 준다.

```java
// MemberController.java
package com.bs.lec17.member.controller;

import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.bs.lec17.member.Member;
import com.bs.lec17.member.service.MemberService;

@Controller
public class MemberController {

	// MemberService를 사용하는 방법
	
	// MemberService service = new MemberService();	// 1. 스프링을 사용하지 않을 때 사용하는 방법
	
	// 2. servlet-context에 <beans:bean id="service" class="com.bs.lec17.member.service.MemberService"></beans:bean>
	// 를 작성한 후 @Autowired를 사용하는 방법
	
	// 3. servlet-context에 작성한 beans를 삭제해 주고 MemberSerivce에 @Service를 작성한 후 @Autowired를 사용하는 방법 (실무에서 가장 많이 사용)
	
	@Resource(name="memService")	// @Service("memService") 처럼 이름을 지정한 서비스를 호출할 수 있다.
	MemberService service;
	
	// memjoin이라는 요청이오면 HTML FORM태그로 부터 받은 속성들을 받아서 서비스를 이용해서 등록을 하는 절차
	@RequestMapping(value="/memJoin", method=RequestMethod.POST)
	public String memJoin(Model model, HttpServletRequest request) {
		String memId = request.getParameter("memId");	
		String memPw = request.getParameter("memPw");
		String memMail = request.getParameter("memMail");
		String memPhone1 = request.getParameter("memPhone1");
		String memPhone2 = request.getParameter("memPhone2");
		String memPhone3 = request.getParameter("memPhone3");
		
		service.memberRegister(memId, memPw, memMail, memPhone1, memPhone2, memPhone3);	
		// MemberService.java에 있는 memberRegister 함수에 속성값을 넣어 실행 준다.
		
		model.addAttribute("memId", memId);
		model.addAttribute("memPw", memPw);
		model.addAttribute("memMail", memMail);
		model.addAttribute("memPhone", memPhone1 + " - " + memPhone2 + " - " + memPhone3);
		
		return "memJoinOk";
	}
	
	@RequestMapping(value="/memLogin", method=RequestMethod.POST)
	public String memLogin(Model model, HttpServletRequest request) {
		
		String memId = request.getParameter("memId");
		String memPw = request.getParameter("memPw");
		
		Member member = service.memberSearch(memId, memPw);
		
		try {
			model.addAttribute("memId", member.getMemId());
			model.addAttribute("memPw", member.getMemPw());
		} catch (Exception e) {
			e.printStackTrace();
		}
		
		
		return "memLoginOk";
	}
	
}
```



### 서비스 객체 구현

```java
// IMemberSerive.java (MemberService의 인터페이스)
package com.bs.lec17.member.service;

import com.bs.lec17.member.Member;

public interface IMemberService {
	void memberRegister(String memId, String memPw, String memMail, String memPhone1, String memPhone2, String memPhone3);
	// 회원등록
	Member memberSearch(String memId, String memPw); // 회원검색
	void memberModify();	// 회원 수정
	void memberRemove();	// 회원 삭제
}
```

```java
// MemberSerive.java
package com.bs.lec17.member.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;
import org.springframework.stereotype.Service;

import com.bs.lec17.member.Member;
import com.bs.lec17.member.dao.MemberDao;

//@Service	// 내가 만드는 Service객체마다 이 어노테이션을 붙여주면 자동적으로 스프링 컨테이너에 등록 된다. 서비스에서는 @Service가 가독성이 좋다
//@Service("memService")	// 이름을 지정해줄 수 있지만 일반적으로 이름을 지정하지 않고 사용한다.
//@Component	// Service와 같은 기능
//@Component("memService")
//@Repository	// Service와 같은 기능
@Repository("memService")
public class MemberService implements IMemberService {

	@Autowired
	MemberDao dao;
	
	// 매개변수로 회원정보에 필요한 속성들을 받아 출력해준다.
	@Override
	public void memberRegister(String memId, String memPw, String memMail,
			String memPhone1, String memPhone2, String memPhone3) {
		System.out.println("memberRegister()");
		System.out.println("memId : " + memId);
		System.out.println("memPw : " + memPw);
		System.out.println("memMail : " + memMail);
		System.out.println("memPhone : " + memPhone1 + " - " + memPhone2 + " - " + memPhone3);
		
		dao.memberInsert(memId, memPw, memMail, memPhone1, memPhone2, memPhone3);
	}

	@Override
	public Member memberSearch(String memId, String memPw) {
		System.out.println("memberSearch()");
		System.out.println("memId : " + memId);
		System.out.println("memPw : " + memPw);
		
		Member member = dao.memberSelect(memId, memPw);
		
		return member;
	}

	@Override
	public void memberModify() {
		
	}

	@Override
	public void memberRemove() {
		
	}

}
```



### DAO 객체 구현

```java
package com.bs.lec17.member.dao;

import com.bs.lec17.member.Member;

public interface IMemberDao {
	void memberInsert(String memId, String memPw, String memMail, String memPhone1, String memPhone2, String memPhone3);
	// 멤버정보를 데이터베이스에 넣는 기능
	Member memberSelect(String memId, String memPw);	// 멤버정보를 데이터베이스에서 찾는 기능
	void memberUpdate();	// 멤버정보를 데이터베이스에 수정 하는 기능
	void memberDelete();	// 멤버정보를 데이터베이스에서 삭제하는 기능
}
```

```java
package com.bs.lec17.member.dao;

import java.util.HashMap;
import java.util.Iterator;
import java.util.Set;

import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

import com.bs.lec17.member.Member;

//@Component	//	@Service와 같은 기능을 한다.
@Repository
public class MemberDao implements IMemberDao {

	private HashMap<String, Member> dbMap;
	
	public MemberDao() {
		dbMap = new HashMap<String, Member>();
	}
	
	@Override
	public void memberInsert(String memId, String memPw, String memMail, String memPhone1, String memPhone2, String memPhone3) {
		System.out.println("memberInsert()");
		System.out.println("memId : " + memId);
		System.out.println("memPw : " + memPw);
		System.out.println("memMail : " + memMail);
		System.out.println("memPhone : " + memPhone1 + " - " + memPhone2 + " - " + memPhone3);
		
		Member member = new Member();
		member.setMemId(memId);
		member.setMemPw(memPw);
		member.setMemMail(memMail);
		member.setMemPhone1(memPhone1);
		member.setMemPhone2(memPhone2);
		member.setMemPhone3(memPhone3);
		
		dbMap.put(memId, member);	// Map을 이용해 Key에 memId, value에 member 객체를 넣어준다.
		 
		Set<String> keys = dbMap.keySet();
		Iterator<String> iterator = keys.iterator();
		
		while (iterator.hasNext()) {
			String key = iterator.next();
			Member mem = dbMap.get(key);
			System.out.print("memberId:" + mem.getMemId() + "\t");
			System.out.print("|memberPw:" + mem.getMemPw() + "\t");
			System.out.print("|memberMail:" + mem.getMemMail() + "\t");
			System.out.print("|memberPhone:" + mem.getMemPhone1() + " - " + 
											   mem.getMemPhone2() + " - " + 
											   mem.getMemPhone3() + "\n");
		}
		
	}

	@Override
	public Member memberSelect(String memId, String memPw) {
		Member member = dbMap.get(memId);
		
		return member;
	}

	@Override
	public void memberUpdate() {

	}

	@Override
	public void memberDelete() {

	}

}
```





### 서비스 객체 구현 방법

![5](https://user-images.githubusercontent.com/58713853/102459313-c896fb80-4088-11eb-9caf-2a0e5beb16e0.PNG)



### DAO 객체 구현 방법

![6](https://user-images.githubusercontent.com/58713853/102459314-c896fb80-4088-11eb-8b07-1886133dc028.PNG)

