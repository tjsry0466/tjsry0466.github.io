---
layout: post
title: Spring Boot 구조 정리
category: spring&spring boot
tags: [spring, spring boot, 구조]
comments: true
---
<!----------------- 탬플릿
>안내말
## forEach
### 설명
[MDN]()
### 문법
```javascript

```
### 예시
```javascript

```

<center>
 <figure>
 <img src="/assets/post-img/git/git_diff.png" alt="views">
 <figcaption>cat을 통해서 git diff 결과를 표시</figcaption>
 </figure>
 </center>
------------------->
> 스프링 부트와 AWS로 혼자 구현하는 웹서비스를 보고 정리했습니다.

# 들어가기 앞서
- 해당 글은 제가 스프링 부트를 처음 공부하면서 스프링 부트 프로젝트에서 소스를 작성할때에
스프링 프레임워크가 어떤 데이터 흐름을 가지고 데이터가 전달되고 어떤 순서로 소스를 작성해야되는지
각각의 패키지들이 어떤 역할을 하는지 정리하는 글입니다.
- Spring boot로 jpa 및 spring-data-jpa를 사용하는 상황에서 작성한 게시글입니다.

# 소스 작성을 위한 프로젝트 구조

스프링 부트는 목적에 따라 여러 패키지를 생성하여 소스코드를 관리하고 있습니다.
크게 몇가지로 나누어 보면 다음과 같습니다.

- domain(entity)
- repository
- dto
- service
- controller

한가지씩 살펴보겠습니다.

## domain
- db의 테이블과 직접적으로 맵핑되는 클래스 입니다.
- jpa 사용시 여러 어노테이션을 사용하여 테이블, 필드, 필드 옵션 등을 설정하게 됩니다.
- domain 클래스에서 자주 사용되는 몇가지 어노테이션을 설명하도록 하겠습니다.

- @Entity - 테이블과 1:1로 맵핑되는 어노테이션으로 해당 어노테이션을 클래스에 붙이면 jpa가 해당 클래스를 관리하게 됩니다.  
- @Column - 필드에 붙이는 어노테이션으로 해당 어노테이션을 붙이면 컬럼으로 인식하게 됩니다.
- @Id - PK를 지정하는 어노테이션 입니다.  
- @GeneratedValue - pk가 중복되는것을 방지하기 위해 작성하는 어노테이션 입니다. strategy 파라미터로 GenerationType.IDENTITY를 주게되면 PK로 인식하여 Auto Increament로 설정됩니다.  
- @Builder - Builder 패턴을 사용하기 위한 어노테이션 입니다. @Builder 어노테이션을 사용하게되면 객체를 생성할때 생성자를 통해서 객체를 생성하는게 아니라 도메인.builder().필드(값).필드(값).build(); 를 통해 필드에 대한 값을 좀더 명시적으로 넣어 줄 수 있습니다.

## Repository
- repository는 DB에 접근하는 소스코드를 모아둔 Interface 입니다.
- JPA를 사용하게되면 JpaRepository 인터페이스를 상속받아 제네릭을 통해 관리하고자 하는 클래스, ID필드 타입을 <Posts, Long>와 같이 넣어주게되면 자동으로 DB와 CRUD 연결을 할 수 있는 메소드를 생성해 줍니다.
- 다만 Create, Update, Delete와 같은 경우에는 다른 테이블간의 조인 을 잘 수행하지 않기 때문에 그대로 사용 하지만 Read와 같은 경우엔 여러 테이블과 조인 연산이 필요한 경우가 많이 때문에 @Query 어노테이션으로 직접 쿼리를 작성하여 사용하거나 querydsl과 같은 동적쿼리를 생성해주는 라이브러리를 같이 사용하게 됩니다.

> 동적 쿼리란 ? 미리 작성해둔 SQL이 아니라 코드 실행 시점에 동적으로 구성되고 실행되는 쿼리입니다.

## DTO
- DTO는 Data Transfer Object로 직역하면 데이터 전송 객체이다.
- Service나 Controller에서 DB에 접근할때 사용하는 클래스 입니다.
- 위에서 설명한 Domain 클래스와 다른게 무엇인지 의문을 품을수 있는데 Domain은 DB 테이블에 대한 정보를 가지고 있는 클래스이고 DTO는 해당 테이블에서 실제로 CRUD를 할 필드를 정의해둔것이라고 보면 된다.
- 이렇게 Domain과 DTO를 나누어서 사용하는 이유는 소스코드 작성중에 디비에 접근할 필드의 변경이 생겼을 경우 Domain을 변경하여 디비에 접근 하는 경우 디비 테이블 설정을 접근하는 경우가 되어 큰 문제를 일으킬수 있습니다.
- 그로인해 테이블에 대한 정보를 작성하는 Domain 클래스와 디비에 접근하는 필드에 관한 클래스를 작성하는 DTO 클래스를 사용하게 됩니다.
- Domain과 마찬가지로 Builder 패턴을 사용 할 수 있습니다.

## Service
- Repository와 DTO를 통해 DB에 접근하여 CRUD의 각각의 프로세스 관리와 에러처리 등을 담당하는 역할을 합니다.
- @Service 어노테이션을 붙여주게 되면 스프링 빈에 등록되고 스프링에서 관리하는 객체가 됩니다.
- DB와 실제적인 접근을 명령하는 소스코드를 작성하는 클래스입니다.
- DTO에 작성된 메소드를 기반으로 소스코드를 작성하게 됩니다.


## Controller
- 제일 앞단에서 HTTP 요청과 응답을 위한 클래스 입니다.
- @Controller 어노테이션을 붙여주면 스프링 빈에 등록되고 스프링에서 관리하는 객체가 됩니다.
- @GetMapping("주소") 와같이 http 메소드 명과 함께 주소를 작성해주게 되면 해당 주소로 요청을 받을수 있게 됩니다.
- Service를 사용하여 디비에 접근하게 되고 model을 사용하여 파라미터를 view단으로 넘길수 있고 template 이름만 문자열로 반환하게 되면 View Resolver가 자동으로 해당 template를 찾아 사용자에게 페이지를 보여주게됩니다.

# 자주 사용하는 lombok의 Annotation

- @Getter - 클래스 필드의 getter 메소드를 자동으로 생성해줍니다.
- @NoArgsConstructor - 클래스에 존재하는 필드의 생성자를 자동으로 생성해줍니다.

# 총 정리
- 스프링은 Domain-Repository-DTO-Service-Controller구조로 되어있으며 소스를 작성하는 순서도 이와 같습니다.
- Domain에서 디비와 관련된 클래스를 작성하고 Repository로 Doamin에 작성된 필드 접근을 위한 CRUD를 spring-data-jpa를 통해 자동으로 생성하고 DTO로 어떤 테이블에 어떤 필드로 접근할지 정의합니다.
- 그후에 Service에서 외부에서 해당 기능을 사용할 수 있도록 메소드를 정의하고 외부에서 요청이 오면 Controller를 통해서 Service로 디비에 접근하여 요청을 처리하게 됩니다.





