---
layout: post
title: "Java Interface Contract : 컴파일, Unit Test 단계에서 Contract 체크 / OpenFeign을 이용한 개발속도 향상"
date: 2020-05-18 00:00:00
lastmod: 2020-05-24 00:00:00
categories: BackEnd
tags: BackEnd Spring MSA
---

![API_contract_15](/images/post_img/APIcontract/API_contract_15.png)

API contract를 실제 사용할 Java interface 코드로 작성합니다.  

기존에 제공자(Provider)는 unit test단계에서 HTTP spec에 대한 feedback을 받을 수 있었지만 이제는 컴파일 단계에서 HTTP spec에 대한 feedback을 받게되기 때문에 개발 속도가 크게 향상 됩니다.  

기존에 소비자(Consumer)가 테스트할 때 제공자(Provider)와 합의된 HTTP spec과 데이터(stub)에 대해 정상적으로 처리되는지 검증을 하는 단계가 component test이고 HTTP call을 이용해야만 했습니다. 이제는 HTTP spec에 대해서는 컴파일 단계에서 feedback을 받게되고 데이터(stub)에 대해서는 unit test에서 feedback을 받게 되기 때문에 개발 속도가 크게 향상 됩니다.  

OpenFeign을 이용하기 때문에 작성된 API contract interface의 구현체가 컴파일시 자동생성되고 소비자(Consumer)는 HTTP call을 위한 구현을 하지 않아도 됩니다.  


<!--more-->

서비스간의 contract라는 개념을 충족시키는 contract interface라는 코드 형태의 실물 계층을 도입할 것입니다.  

Contract interface의 장점을 먼저 소개하고  
샘플코드와 함께 contract interface를 설계, 검증하고 테스트하는 내용들을 살펴보겠습니다.  

참고로 contract, component 테스트를 위해 제가 직접 고민해서 설계한 방법입니다.  
일반적으로 사용되는 용어들과 약간의 충돌이 있을 수 있습니다. 헷갈리게 표현된 용어가 있다면 양해 부탁 드리고 의견 주시기 바랍니다.  

이 글에서 사용되는 샘플코드는 [https://github.com/dveamer/contract](https://github.com/dveamer/contract)에서 확인하실 수 있습니다.  

# Contract Interface 계층 추가

여러 서비스들이 있는 가운데 댓글을 관리하는 comment 서비스가 제공자(provider)인 경우를 살펴보겠습니다.  
소비자(Consumer)인 다른 서비스들은 comment 서비스의 API를 HTTP 호출을 합니다. 아래 이미지의 점섬으로 표시된 화살표는 HTTP 호출을 의미합니다.  

![API_contract_10](/images/post_img/APIcontract/API_contract_10.png)  

소비자와 제공자 사이에는 API에 대한 계약(Contract)이 필요합니다.  
특히 서비스간 API 호출이 많은 MSA에서는 더 빠르고 강력한 계약이 필요합니다.  

기존에 contract는 개념적인 존재였습니다. 혹은 contract test를 통해 contract라는 것이 실제 존재하는 것 처럼 해보려고 했습니다.  

이 글에서 설명하는 내용은 Java contract interface라는 코드 형태의 실물을 contract로 사용합니다.  
소비자, 제공자 모두 이 contract interface에 의존성을 갖게 됩니다.  

소비자는 contract interface를 Java call을 하게 할 것이고  
제공자는 contrat interface의 구현체인 controller를 만들어서 수신할 것입니다.  
소비자가 가진 contract interface가 제공자가 가진 contract interface를 HTTP call 하겠지만  
그 HTTP call은 contract interface가 제공된 시점에 API 계약에 대한 HTTP spec은 이미 검증되어있기 때문에  
소비자는 마치 Java call을 구현하면 제공자의 controller에 안전하게 메시지가 전달됩니다.  
그래서 아래 이미지에서는 HTTP call을 의미하는 점선의 화살표가 없고 Java call을 의미하는 실선의 화살표를 그려넣었습니다.  

이 글에서 계속해서 HTTP call은 점선으로 Java call은 실선으로 표시가 될 것이니 기억해주시기 바랍니다.  
그리고 흰색으로 비워진 실선 화살표는 interface를 구현함을 의미합니다.  

![API_contract_11](/images/post_img/APIcontract/API_contract_11.png)  

아래의 이미지가 좀 더 구체적인 표현입니다.  
소비자는 FeignClient라는 HTTP client를 이용해서 HTTP call을 하고 있습니다.  
그리고 제공자는 Controller로 그 HTTP call을 호출 받고 있습니다.  

![API_contract_12](/images/post_img/APIcontract/API_contract_12.png)  

첫번째 재미있는 점은 FeignClient, controller 모두 contract의 구현체라는 점입니다.  
두번째 재미있는 점은 FeignClient라는 구현체는 소비자가 작성하지 않아도 자동으로 구현된다는 점입니다.  
즉, contract를 Java call하기만하면 개발자의 별다른 구현없이 HTTP call을 정확하게 보낼 수 있습니다.  

## Contract Interface 계층의 장점

소비자, 제공자 사이에 contract라는 계층이 추가되어 contract가 소비자, 제공자 보다 더 상위계층으로 구성되게 됩니다.  

제공자 입장에서는 쓸데 없는 의존성이 추가된 것으로 보이지만  
전체 서비스의 입장에서는 서비스간의 계약관계가 제공자의 기능보다 더 중요합니다.  
사용자에게 가치를 제공해주는 것은 단일 서비스가 아닌 서비스간 협력에 의해서 이뤄집니다.  
일반적인 MSA라면 contract interface 계층이 더 상위계층으로 설계되는 것이 맞습니다.  

Contract interface의 하위 계층인 제공자는 API를 임의로 수정시 컴파일 단계에서 에러가 나게 됩니다.  
기존에 contract tetst 단계에서 체크가 가능했던 API spec 변경여부를 컴파일 단계에서 feedback을 받게 되므로 개발 속도가 향상되고 가장 중요한 계약관계의 안정성이 높아집니다.  

![API_contract_15](/images/post_img/APIcontract/API_contract_15.png)  

"지금도 개발하기 힘든데 더 힘들어 지고 개발속도가 너무 느려지는 것 아니냐?" 라고 생각할 수 있습니다.  
하지만 현재 unit test, component test, contract test 등을 하고 있다면  
작업량이 늘어나지 않습니다. 문서 작업도 없고 개발에 필요한 Java 소스 코드만 작성만 합니다. 오히려 작업량은 줄어듭니다.  
게다가 컴파일 단계에서 feedback을 받게 되기 떄문에 작업 속도도 빠르고 일의 양도 줄어듭니다.  

만약에 MSA를 하면서 unit test, component test, contract test 등의 테스트를 안하고 있다면  
문제가 생겨도 들어나지 않을 뿐이지 나중에 어떤 문제가 생길지 모르는 상황입니다.  
결과적으로는 개발속도가 엄청나게 떨어지는 상황이 생기게 됩니다.  

## 그 외의 장점

정확히는 contract interface 계층의 장점은 아니지만  
이 글의 가이드를 따라하면 아래와 같은 장점들이 더 취할 수 있습니다.  

  * 소비자 역시 컴파일 단계에서 feedback을 받음  
  * 1회 작성된 contract stub 코드가 다수의 소비자, 제공자의 unit 테스트 코드 작성시 재활용됨  
  * 소비자는 HTTP call에 대한 코드작성을 하지 않고서도 contract 에 대한 정확한 HTTP spec을 만족시킨 HTTP call을 보냄  
  * Stub server를 제공가능하여 Java 가 아닌 다른 언어의 소비자 시스템도 stub server를 HTTP call하며 테스트 가능  
  * Stub server는 Swagger-UI 기능을 제공하여 깔끔한 document 및 수동 테스트 가능  

## 단점 

이글에서 설명하는 내용의 장점을 최대한 취하려면 Java, Spring을 사용해야 합니다.  
결국 MSA의 장점인 polyglot에 악영향을 줍니다.  

또한 아키텍트 관점에서 특정 언어, 프레임워크에 의존하는 설계는 좋지 않습니다.  
Java, Spring 그리고 Java contract interface를 이용해서 장점을 취했다면  
다른 언어, 프레임워크로 바꾸기 위해서는 그 장점들을 일부분 포기해야할 수 있습니다.  

하지만 모든 마이크로 서비스가 Java, Spring을 사용해야만하는 것은 아닙니다.  
이 과정에서 생성되는 stub 서버, contract test는 소비자, 제공자가 Java, Spring 이 아니더라도 활용할 수 있기 때문에  
장점을 최대한 취할 수는 없지만 타 언어를 사용하는 서비스들도 부분적으로 장점을 취할 수 있습니다.  

현재 운영 중인 대부분의 마이크로 서비스가 Java, Spring을 사용한다면 충분히 유용하게 활용할 수 있는 방법입니다.  

# Contract 생성 과정 

Contract 계층은 개념적인 존재가 아니라 코드로 존재하는 실제 계층입니다.  
즉, 그 코드는 생성되어야하고 보관되어야하고 관리되어야 합니다.  

이 글에서는 Consumer-Driven-Test 와 Provider-Driven-Test 에 대한 개념은 다루지 않습니다. 기술적으로는 두 방법 모두 수용 가능합니다.  

일단 contract, contract stub을 위한 형상관리 repository가 필요합니다.  
소비자, 제공자 서비스와는 별도의 repository로 구성합니다. 그리고 소비자, 제공자가 아닌 maintainer가 필요합니다.  

예를들어, 소비자가 신규 API 추가 요청을 하고 제공자와 협의를 하는 과정을 생각해보겠습니다.  

먼저 소비자가 contract, contract stub 코드를 작성해서 pull resquest를 보냅니다.  
그러면 code review 하듯이 소비자와 제공자가 contract, contract stub 코드를 다듬어 나갈 수도 있고  
제공자가 주도적으로 코드를 다듬어 나갈 수 있습니다. 그리고 contract stub을 이용해서 생성한 contract를 검증합니다.  

Maintainer는 아래 사항들을 확인 후 pull request를 merge합니다.  

  * API 변경 건 존재 여부 체크 (추가/삭제만 허용)
  * Contract test의 정상 수행 여부
  * Contract, contract stub 의 버전 증가여부, 버전 일치여부

그 뒤 maintainer는 contract, contact stub에 대한 jar를 각각 만들어서 원격 Maven repository에 배포 합니다.  
참고로 앞서 maintainer가 체크, 진행하는 과정은 CI/CD 에서 자동화처리가 가능한 영역입니다.  

![API_contract_14](/images/post_img/APIcontract/API_contract_14.png)  

## Contract Interface 추가 

아래 interface 코드는 contract-comment jar에 포함될 ```CommentContract.java``` 입니다.  

OpenFeign 관련 설정들을 함께 포함하고 있습니다. 하지만 어쩃든 Java interface이기 때문에 소비자는 Java call로 해당 method를 호출 할 수 있습니다.  
OpenFeign이 어떻게 활용 되는지는 추후에 다시 다루게 됩니다.  

또한 CommentDto, ArticleCommentCountDto 같은 DTO(Data Tracnfer Object)도 보입니다. DTO 역시 contract-comment jar가 포함하고 있기 때문에 소비자, 제공자 모두 DTO를 새로 만들 필요 없으며 둘 사이의 DTO 불일치는 발생할 수 없습니다.  

해당 샘플은 [https://github.com/dveamer/contract/tree/master/contract-comment](https://github.com/dveamer/contract/tree/master/contract-comment)에서 확인 가능합니다.  

~~~java
package com.dveamer.contract.comment;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.cloud.openfeign.SpringQueryMap;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

import java.util.List;

@FeignClient(name = "${api.contract.commentClient.name}"
        , url = "${api.contract.commentClient.url}"
        , contextId = "${api.contract.commentClient.contextId}")
public interface CommentContract {

    @GetMapping(path="/articles/{articleId}/comments")
    List<CommentDto> loadCommentsByArticleId(@PathVariable("articleId") String articleId);

    @GetMapping(path="/articles")
    List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(@SpringQueryMap ConditionDto conditionDto);

}
~~~

특이한 점으로 ```@SpringQueryMap``` 이라는 OpenFeign에서 제공하는 annotation이 loadArticleIdHavingNumerousComments의 conditionDto 파라미터에 적용됐습니다.  
HTTP spec을 정의하는 annotation은 모두 spring annotaion을 사용하지만 유일하게 예외인 ```@SpringQueryMap```은 query-string 으로 전달될 파라미터들을 의미합니다.  

@FeignClient 라는 OpenFeign 관련 annotation이 사용됐습니다. OpenFeign에 대해 처음들어보셨다면 [부록 -OpenFeign 간략설명](# OpenFeign 간략설명)을 참고해주시기 바랍니다.  


## Contract 패키징 

앞으로 만들 contract stub은 contract에 의존성을 갖습니다.  
Contract stub을 만들기 위해 아래 명령어로 contract 를 패키징 합니다.  
빌드되고 jar로 묶여서 로컬환경의 Maven repository에 배포 됩니다.  

~~~terminal
contract-comment$ gradle install 
~~~

## Contract Stub 추가

해당 샘플 코드는 [https://github.com/dveamer/contract/tree/master/contract-comment-stub](https://github.com/dveamer/contract/tree/master/contract-comment-stub)에서 확인 가능합니다.  


### build.gradle

contract-comment-stub은 contract-comment에 대한 의존성을 갖고 있습니다.  

~~~build.gradle

...(생략)

dependencies {

  ...(생략)

    implementation 'com.dveamer:contract-comment:0.0.1'
}

...(생략)
~~~



### Stub Implementation Of Contract

contract-comment.jar에서 제공하는 CommentContract에 대한 구현체 stub를 생성합니다.  

~~~java
package com.dveamer.contract.comment.stub;

import com.dveamer.contract.comment.ArticleCommentCountDto;
import com.dveamer.contract.comment.CommentContract;
import com.dveamer.contract.comment.CommentDto;
import com.dveamer.contract.comment.ConditionDto;
import org.springframework.stereotype.Component;

import java.util.List;

@RestController
public class CommentContractStub implements CommentContract {

    @Override
    @GetMapping(path="/articles/{articleId}/comments")
    public List<CommentDto> loadCommentsByArticleId(@PathVariable("articleId") String articleId) {
        if(articleId.equals(ArticleFixture.articleId1())) {
            return CommentFixture.commentList1();
        }

        return CommentFixture.commentList2();
    }

    @Override
    @GetMapping(path="/articles")
    public List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(ConditionDto conditionDto) {
        return ArticleCommentCountFixture.articleCommentCountList(conditionDto.getBiggerThan(), conditionDto.getBeforeDays());
    }

}
~~~


CommentContractStub은 두가지 목적으로 사용됩니다.  

첫번째는 stub server 입니다.  
해당 stub은 contract와 동일한 RequestMapping annotation들과 URL, 파라미터 들이 선언되어있습니다. 또한 ```@RestController``` 가 선언되어있습니다.  
Spring Boot 기동시 contract를 만족시키는 stub 서버로 구성 가능합니다. 덕분에 Java가 아닌 타 언어로 작성된 소비자 서비스도 해당 stub server를 이용해서 compoent test가 가능합니다.  

아래 명령어로 Spring Boot를 기동시킬 수 있습니다.  

~~~terminal
contract-comment-stub$ gradle bootRun
~~~

아래 명령어로 stub server의 jar를 만들수 있습니다. ```build/libs``` 디렉토리에 ```contract-comment-stub-server-0.0.1.jar``` 파일이 생길 것입니다.  

~~~terminal
contract-comment-stub$ gradle bootJar
~~~

두번째 목적은 stub입니다.  
```@RestController```가 존재함에도 불구하고 CommentContractStub 은 Java call을 받을 수 있습니다.  

소비자는 unit test, component test 작성시 CommentContractStub을 java call해서 stub 데이터를 받아봅니다.  
제공자는도 CommentContractStub을 unit test, contract test에 활용합니다.  
Unit test를 작성 시 stub을 활용하기 때문에 테스트 코드 작성시간을 아낄 수 있습니다.  

아래 명령어로 stub jar를 만들수 있습니다. ```build/libs``` 디렉토리와 ```~/.m2/repository/com/dveamer/contract-comment-stub/0.0.1``` 디렉토리에 ```contract-comment-stub-0.0.1.jar``` 파일이 생길 것입니다.  

~~~terminal
contract-comment-stub$ gradle install
~~~

### Swagger 기능 제공

Swagger 설정을 할 수 있습니다.  

~~~java

/*
* This SpringBootApplication is for Swagger Page of Contract.
* Boot this application and then go to localhost:8080/swagger-ui.html in browser.
* */

@EnableSwagger2
@SpringBootApplication
public class CommentContractStubApplication {
    public static void main(String[] args) {
        SpringApplication.run(CommentContractStubApplication.class, args);
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Comment Contract")
                .description("Comment Contract - API Example")
                .build();
    }

    @Bean
    public Docket commonApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("example")
                .apiInfo(this.apiInfo())
                .select()
                .apis(RequestHandlerSelectors
                        .basePackage("com.dveamer.contract.comment.stub"))
                .paths(PathSelectors.ant("/**"))
                .build();
    }

}

~~~

Spring Boot 기동 후 ```localhost:8080/swagger-ui.html``` 에 접속하시면 아래와 같은 API documentation을 볼 수 있고 수동으로 테스트도 보내볼 수 있습니다.  

![API_contract_16](/images/post_img/APIcontract/API_contract_16.png)  



### Contract Test Codes

Contract-comment-stub jar는 contract test 코드를 가지고 있습니다.  
Contract test의 예상(expected) 결과는 stub이 되고 실제(actual) 결과는 제공자 서비스의 응답 값이 됩니다.  
그리고 HTTP call을 호출하는 commentContract의 구현체는 OpenFeign이 생성해준 구현체로 소비자가 사용한 구현체와 동일합니다.  

OpenFeign에 의존성을 갖고 있는 테스트 코드입니다. OpenFeign에 대해 처음들어보셨다면 [부록 - OpenFeign 간략설명](# OpenFeign 간략설명)을 참고해주시기 바랍니다.  

~~~java
package com.dveamer.contract.comment;

import com.dveamer.contract.comment.stub.ArticleCommentCountFixture;
import com.dveamer.contract.comment.stub.ArticleFixture;
import com.dveamer.contract.comment.stub.CommentContractStub;
import com.dveamer.contract.comment.stub.ConditionFixture;
import feign.Feign;
import feign.jackson.JacksonDecoder;
import feign.jackson.JacksonEncoder;

...(생략)

@TestInstance(TestInstance.Lifecycle.PER_CLASS)
class CommentContractTests {

    Logger logger = LoggerFactory.getLogger(this.getClass());

    private CommentContract commentContract;

    @BeforeAll
    void setup() {
        //commentContract = new CommentContractStub(); // 테스트 코드 작성시에만 사용

        Properties properties = System.getProperties();
        String url = properties.getProperty("api.contract.comment.url");
        commentContract = Feign.builder()
                .contract(new SpringMvcContract())
                .encoder(new JacksonEncoder())
                .decoder(new JacksonDecoder())
                .target(CommentContract.class, url);
    }

    @Test
    void loadCommentsByArticleIdTest() {
        List<CommentDto> expectedList = new CommentContractStub().loadCommentsByArticleId(ArticleFixture.articleId1());
        List<CommentDto> actualList = commentContract.loadCommentsByArticleId(ArticleFixture.articleId1());
        assertThat(actualList, is(samePropertyValuesAs(expectedList)));
    }

    @Test
    void loadArticleIdHavingNumerousCommentsTest() {
        ConditionDto condition = ConditionFixture.conditionDto();
        List<ArticleCommentCountDto> expectedList = ArticleCommentCountFixture.articleCommentCountList(condition.getBiggerThan(), condition.getBeforeDays());
        List<ArticleCommentCountDto> actualList = commentContract.loadArticleIdHavingNumerousComments(condition);
        assertThat(actualList, is(samePropertyValuesAs(expectedList)));
    }

}


~~~



```setup()``` 메소드에 주석으로 남겨둔 곳이 있습니다. 이 unit 테스트 작성을 시작할 때는 아래와 같이 ```setup()``` 메소드를 구성합니다.  

~~~java
    @BeforeAll
    void setup() {
        commentContract = new CommentContractStub(); // 테스트 코드 작성시에만 사용
    }
~~~

그리고 테스트를 실행시키면 재미있게도 예상결과와 실제결과 모두 stub을 통한 결과이기 때문에 항상 참일 수 밖에 없습니다.  
이 과정을 통해서 테스트 케이스를 정상적으로 작성했는지를 체크 합니다.  

그 다음에 contract test를 위해 HTTP call을 날리기 위해 OpenFeign 설정을 합니다.  

~~~java

    @BeforeAll
    void setup() {
        Properties properties = System.getProperties();
        String url = properties.getProperty("api.contract.comment.url");
        commentContract = Feign.builder()
                .contract(new SpringMvcContract())
                .encoder(new JacksonEncoder())
                .decoder(new JacksonDecoder())
                .target(CommentContract.class, url);
    }
~~~

그리고 테스트를 실행시켜보면 HTTP 커넥션이 안되서 에러가나는 것을 확인할 수 있습니다.  

~~~terminal

contract-comment-stub$ gradle test

> Task :test

com.dveamer.contract.comment.CommentContractTests > loadCommentsByArticleIdTest() FAILED
    feign.RetryableException at CommentContractTests.java:47
        Caused by: java.net.ConnectException at CommentContractTests.java:47

com.dveamer.contract.comment.CommentContractTests > loadArticleIdHavingNumerousCommentsTest() FAILED
    feign.RetryableException at CommentContractTests.java:55
        Caused by: java.net.ConnectException at CommentContractTests.java:55

~~~

여전히 테스트의 예상결과는 stub이지만 실행결과는 HTTP call을 통한 응답으로 바꼈습니다.  
Stub과 contract test를 일치시킴으로써 소비자와 제공자가 동일한 테스트 데이터로 개발해나감을 확신할 수 있습니다.  

이제 contract test 혹은 검증을 하기 위한 준비가 모두 끝났습니다.  


## Contract Test

### Contract Test 의 목적

기존에는 소비자가 제공자의 HTTP API에 대해서 의존성을 가지고 있는 반면에 제공자는 별다른 의존성을 가지고 있지 않았습니다.  
그로 인해 제공자가 HTTP API를 변경하게 되면 소비자는 영향을 받았습니다.  

그러한 일을 막기 위해서 contract test가 필요해졌고 contract test시에만 제공자는 변경사항을 배포할 수 있도록 제한해야합니다.  
Contract test 때 점검되는 사항은 2가지 입니다.  

첫번째는 약속해둔 API spec에 변경이 있는지 체크 합니다.  

  * HTTP URI, method 체크  
  * 특정 파라미터가 HTTP header, query stirng, path variable, HTTP body 중 어디로 전달되는지 체크  
  * 파라미터들의 데이터 타입 점검  

두번째는 데이터의 결과에 변경이 있는지 체크합니다. 미리 약속해둔 테스트 케이스를 가지고 체크가 됩니다.  


하지만 이조차 이론적인 이야기에 불과합니다. 만약에 제공자가 contract test를 무시하거나 contract test 자체를 변경하면서 HTTP API를 변경하게 되면 소비자 측에서는 unit test, component test를 정상적으로 통과했음에도 불구하고 HTTP call을 호출시 문제가 생길 수 있다는 부담감이 있습니다. 즉, 모든 테스트를 다 했음에도 불구하고 배포 후 HTTP call에 대한 추가적인 테스트를 진행해야만 하는 부담을 갖고 있었습니다.  

하지만 contract interface 라는 실물 계층이 생겼고 소비자가 최신버전의 contract, contract stub을 사용한다면 컴파일 단계에서 HTTP spec에 대한 feedback을 받고 unit test 단계에서 데이터에 대한 feedback을 받을 수 있습니다.  

### Contract Test 수행

Contract stub의 stub server를 기동시키고 contract stub의 contract test 코드를 실행시킬 것입니다.  
현재는 제공자의 API 가 아니라 stub server의 API를 호출할 것입니다.  
그 테스트 결과가 성공이라면 contract test 코드와 stub server의 API 가 http spec 그리고 테스트 케이스를 동일하게 갖췄다는 것이 확인됩니다.  

소비자는 stub server를 가지고 테스트를 진행하고  
제공자는 contract test를 가지고 테스트를 진행하게 됩니다.  
서로 교류없이 각자 테스트를 진행하더라도 stub server와 contract test 간의 불일치가 없음을 확인했기 때문에  
소비자와 제공자 사이에서도 불일치가 발생하지 않을 것이라는 확신을 할 수 있습니다.  

그 확신을 위해 제공자가 API를 만들지 않은 현 단계에서 stub server와 contract test를 수행합니다.  

일단 stub server를 기동시킵니다.  

~~~terminal
contract-comment-stub$ gradle bootRun
~~~

또 다른 터미널에서 contract test를 실행합니다.  

~~~terminal
contract-comment-stub$ gradle test
~~~

성공여부를 확인합니다. 만약에 실패한다면 contract, contract stub 중 잘못된 곳을 찾아서 수정해줘야 합니다.  
성공하게 되면 contract, contract stub에 대한 Pull Reqeust가 merge가 진행될 수 있도록 처리합니다.  

그러면 이제 소비자와 제공자가 각자 개발을 진행할 수 있습니다.  
그리고 제공자는 CI(Continuous Integration) 과정에서 contract test 통과여부를 체크 받아야 합니다.  


# 중간 점검

지금까지 생성한 내용을 아래와 같습니다.  

  * Contract Interface : contract-comment-0.0.1.jar
  * Contract Stub : contract-comment-stub-0.0.1.jar
  * Contract Tests : jar 공유가 아닌 CI 과정에서 unit test를 실행시켜서 수행
  * Contract Stub Server : contract-comment-stub-server-0.0.1.jar

이 중 contract test, contract stub server는 소비자, 제공자가 Java가 아닌 다른 언어를 사용하더라도 활용 가능합니다.  

만약 소비자, 제공자가 Java, Spring을 사용한다면 contract interface, contract stub을 이용해서 더욱 안정적이고 빠르게 개발을 진행할 수 있습니다.  

여기서부터는 소비자, 제공자가 모두 Java, Spring을 사용한다고 가정하고 내용을 이어 나가겠습니다.  


# Consumer 개발 진행

![API_contract_07](/images/post_img/APIcontract/API_contract_07.png)  

필요한 API에 대한 contract, contract stub이 모두 Maven Repository에 배포되어 있다고 하면 소비자는 큰 노력없이 빠르고 쉽게 개발이 진행 가능합니다.  
위의 다이어그램처럼 소비자 코드는 contract 의 구현체인 stub을 호출하게 됩니다. Java call만으로 테스트가 되기 때문에 stub server의 기동유무에 대해 신경쓸 필요도 없습니다.  
또한 contract 뒤의 제공자 서비스에 대해서도 전혀 신경 쓸 필요가 없습니다.  

Contract를 사용하는 service를 작성하는 내용과 service에 대한 unit test 작성에 대해서 살펴볼 것입니다.  
contract-comment jar에서 제공하는 CommentContract 와 contract-comment-stub jar에서 제공하는 stub을 사용하기 때문에 개발 속도가 향상됩니다.  

위와 같은 소비자 측의 개발 속도 향상은 해당 API에 대해 제공자 서비스는 1개지만 소비자 서비스는 다수라는 점에서 더 빛을 발합니다.  

해당 코드의 샘플은 [https://github.com/dveamer/contract/tree/master/article](https://github.com/dveamer/contract/tree/master/article)에서 확인 가능합니다.  

## build.gradle

contract-comment에 대한 의존성과 contract-comment-stub에 대한 의존성을 가지고 있습니다.  
다만 contract-comment-stub은 testImplementation으로 의존성을 걸어서 테스트 시에만 의존성을 갖습니다.  
실제 운영환경에서는 contract-comment-stub 코드를 사용되지 않습니다.  

그리고 OpenFeign을 사용하기 위해서 ```spring-cloud-starter-openfeign``` 에 대한 의존성을 갖습니다.  


~~~build.gradle

...(생략)

dependencies {

  ...(생략)

    implementation 'org.springframework.cloud:spring-cloud-starter-openfeign'
    implementation 'com.dveamer:contract-comment:0.0.1'
    testImplementation 'com.dveamer:contract-comment-stub:0.0.1'

}

...(생략)
~~~


## Service Implementation In Consumer

소비자는 CommentContract를 사용만 하면 됩니다. 원래는 CommentContract의 구현체를 직접 작성해야 되겠지만 OpenFeign을 사용하기 때문에 그 구현체가 컴파일시에 자동으로 생성됩니다.  
실제로 동작시켜보면 HTTP call을 제공자에게 보내는 것을 확인할 수 있습니다.  

기존에는 계약단계에서 결정된 API spec을 기준으로 HTTP call을 위한 구현체를 소비자가 직접 작성해야했습니다.  
하지만 이제는 탄탄하게 점검되어 제공되는 contract를 호출하는 것만으로도 완료가 됩니다.  


~~~java
package com.dveamer.article.component;

import com.dveamer.contract.comment.CommentContract;
import com.dveamer.contract.comment.ConditionDto;
import org.springframework.stereotype.Service;

@Service
public class ArticleServiceImpl implements ArticleService {

    private final CommentContract commentContract;

    ArticleServiceImpl(CommentContract commentContract) {
        this.commentContract = commentContract;
    }

    @Override
    public int loadCountOfComments(String articleId) {
        return commentContract.loadCommentsByArticleId(articleId).size();
    }

    @Override
    public int loadCountOfFamousArticle() {
        ConditionDto conditionDto = new ConditionDto();
        conditionDto.setBiggerThan(2);
        conditionDto.setBeforeDays(3);
        return commentContract.loadArticleIdHavingNumerousComments(conditionDto).size();
    }
}
~~~


## Unit Test In Consumer

Service 구현에 대한 unit test 작성시에는 Stub을 그대로 사용하면 되기 때문에 테스트 코드 작성 시간을 줄일 수 있습니다.  
그 뿐만 아니라 제공자의 unit test 그리고 소비자의 unit test에서 동일한 stub을 이용하게 됨으로써 각자 unit test만 잘 지켜도 API가 깨지는 일을 막을 수 있습니다.  
또한 이러한 장점은 component test 시에도 그대로 활용가능합니다.  

~~~java
package com.dveamer.article.component;

import com.dveamer.contract.comment.stub.ArticleFixture;
import com.dveamer.contract.comment.stub.CommentContractStub;
import com.dveamer.contract.comment.stub.ConditionFixture;
...(생략)

@TestInstance(TestInstance.Lifecycle.PER_CLASS)
class ArticleServiceTests {

    private CommentContractStubWrapper commentContract;
    private ArticleService articleService;

    @BeforeAll
    public void setup() {
        commentContract= new CommentContractStubWrapper();
        articleService = new ArticleServiceImpl(commentContract);
    }

    @Test
    public void loadCountOfComments_success() throws Exception {
        int expectedValue = new CommentContractStub().loadCommentsByArticleId(ArticleFixture.articleId1()).size();
        int actualValue = articleService.loadCountOfComments(ArticleFixture.articleId1());
        assertThat(actualValue, equalTo(expectedValue));

        assertThat(commentContract.loadCommentsByArticleId_wasCalled(), equalTo(true));
    }

    @Test
    public void loadArticleIdHavingNumerousComments_success() throws Exception {
        int expectedValue = new CommentContractStub().loadArticleIdHavingNumerousComments(ConditionFixture.conditionDto()).size();
        int actualValue = articleService.loadCountOfFamousArticle();
        assertThat(actualValue, equalTo(expectedValue));

        assertThat(commentContract.loadArticleIdHavingNumerousComments_wasCalled(), equalTo(true));
    }

}
~~~


~~~java
package com.dveamer.article.component;

import com.dveamer.contract.comment.ArticleCommentCountDto;
import com.dveamer.contract.comment.CommentDto;
import com.dveamer.contract.comment.ConditionDto;
import com.dveamer.contract.comment.stub.CommentContractStub;
import com.dveamer.contract.comment.stub.ConditionFixture;

import java.util.List;

public class CommentContractStubWrapper extends CommentContractStub {

    private boolean loadCommentsByArticleId_wasCalled = false;
    private boolean loadArticleIdHavingNumerousComments_wasCalled = false;

    public boolean loadCommentsByArticleId_wasCalled() {
        return loadCommentsByArticleId_wasCalled;
    }

    public boolean loadArticleIdHavingNumerousComments_wasCalled() {
        return loadArticleIdHavingNumerousComments_wasCalled;
    }

    @Override
    public List<CommentDto> loadCommentsByArticleId(String articleId) {
        loadCommentsByArticleId_wasCalled = true;
        return super.loadCommentsByArticleId(articleId);
    }

    @Override
    public List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(ConditionDto conditionDto) {
        loadArticleIdHavingNumerousComments_wasCalled = true;
        return super.loadArticleIdHavingNumerousComments(ConditionFixture.conditionDto());
    }
}
~~~

## Consumer 배포

소비자 측에서는 제공자 측에서 배포가 완료되었는지를 체크 후 배포를 진행해야 합니다. 이를 위한 어떤 다른 장치가 필요할 수도 있습니다.  
배포가 완료되고 나면 아래의 다이어그램처럼 소비자 service가 HTTP call을 제공자에게 보낼 수 있게 됩니다. 이 때도 OpenFeign이 제공한 Contract interface의 구현체가 사용됩니다.  

![API_contract_12](/images/post_img/APIcontract/API_contract_12.png)  


# Provider 개발 진행 

![API_contract_08](/images/post_img/APIcontract/API_contract_08.png)  


Controller 작성과 그에 대한 unit test를 작성에 대해서 살펴 볼 것입니다.  
contract-comment jar에서 제공하는 CommentContract 와 contract-comment-stub jar에서 제공하는 stub을 사용하기 때문에 개발 속도가 향상됩니다.  


해당 코드의 샘플은 [https://github.com/dveamer/contract/tree/master/comment](https://github.com/dveamer/contract/tree/master/comment)에서 확인 가능합니다.  

## build.gradle

contract-comment에 대한 의존성과 contract-comment-stub에 대한 의존성을 가지고 있습니다.  
다만 contract-comment-stub은 testImplementation으로 의존성을 걸어서 테스트 시에만 의존성을 갖습니다.  
실제 운영환경에서는 contract-comment-stub 코드를 사용되지 않습니다.  


~~~build.gradle

...(생략)

dependencies {

  ...(생략)

    implementation 'com.dveamer:contract-comment:0.0.1'
    testImplementation 'com.dveamer:contract-comment-stub:0.0.1'

}

...(생략)
~~~

## Controller Implementing Contract

제공자는 CommentContract의 구현체를 만들면 됩니다. Contract와 동일한 public method를 갖게되기 때문에 파라미터 타입과, 응답 타입 등이 모두 소비자와 동일할 수 밖에 없습니다.  

다만 annotation들은 사람의 개입이 들어가기 때문에 실수가 발생할 수 있습니다. 가장 좋은 방법은 이미 검증 된 stub의 CommentContractStub의 메소드들을 그대로 복사해오면 검증된 annotation들을 그대로 가져올 수 있습니다. 거의 복사만 하면 되는 수준이기 때문에 오류를 생성할 가능성은 매우 낮습니다. 여기서 만약에 실수가 발생하더라도 추후 contract test를 수행하면 문제점은 바로 발견됩니다.  


~~~java
package com.dveamer.comment.web;

import com.dveamer.comment.component.Article;
import com.dveamer.comment.component.Comment;
import com.dveamer.comment.component.CommentService;
import com.dveamer.comment.component.ConditionVo;

...(생략)

@RestController
public class CommentController implements CommentContract {

    ...(생략)

    private final CommentService commentService;

    CommentController(CommentService commentService) {
        this.commentService = commentService;
    }

    @Override
    @GetMapping("/articles/{articleId}/comments")
    public List<CommentDto> loadCommentsByArticleId(@PathVariable("articleId") String articleId) {
        ...(생략)
    }

    @Override
    @GetMapping("/articles")
    public List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(ConditionDto conditionDto) {
        ...(생략)
    }

    ...(생략)

}
~~~


## Unit Test For Contract In Provider

Contract 구현에 대한 unit test 작성시에는 contract-comment-stub에서 제공하는 Stub을 예상(expected) 결과로 사용하면 되기 때문에 테스트 코드 작성 시간을 줄일 수 있습니다.  
그 뿐만 아니라 제공자의 unit test 그리고 소비자의 unit test, component test에서 동일한 stub을 이용하게 됨으로써 각자 unit test만 잘 지켜도 API가 깨지는 일을 막을 수 있습니다.  

~~~java
package com.dveamer.comment.web;


import com.dveamer.contract.comment.ArticleCommentCountDto;
import com.dveamer.contract.comment.CommentDto;
import com.dveamer.contract.comment.ConditionDto;
import com.dveamer.contract.comment.stub.ArticleFixture;
import com.dveamer.contract.comment.stub.CommentContractStub;
import com.dveamer.contract.comment.stub.ConditionFixture;

...(생략)

@TestInstance(TestInstance.Lifecycle.PER_CLASS)
class CommentControllerTests {

    private MockMvc mockMvc;
    private CommentServiceStub commentService;

    @BeforeAll
    public void setup() {
        commentService= new CommentServiceStub();
        mockMvc = MockMvcBuilders.standaloneSetup(new CommentController(commentService)).build();
    }


    @Test
    public void loadCommentsByArticleId_success() throws Exception {
        List<CommentDto> expectedCommentDtoList = new CommentContractStub().loadCommentsByArticleId(ArticleFixture.articleId1());
        mockMvc.perform(get("/articles/{articleId}/comments", ArticleFixture.articleId1()))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(content().string(JsonConverterUtil.toJson(expectedCommentDtoList)));

        assertThat(commentService.loadCommentsByArticleId_wasCalled(), equalTo(true));
    }

    @Test
    public void loadArticleIdHavingNumerousComments_success() throws Exception {
        ConditionDto condition = ConditionFixture.conditionDto();
        List<ArticleCommentCountDto> expectedArticleCommentCountDtoList = new CommentContractStub().loadArticleIdHavingNumerousComments(condition);
        mockMvc.perform(get("/articles")
                            .queryParam("beforeDays", Integer.toString(condition.getBeforeDays()))
                            .queryParam("biggerThan", Integer.toString(condition.getBiggerThan())))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(content().string(JsonConverterUtil.toJson(expectedArticleCommentCountDtoList)));

        assertThat(commentService.loadArticleIdHavingNumerousComments_wasCalled(), equalTo(true));
    }


}
~~~


## Provider 배포 

제공자의 배포시에는 항상 contract test가 함께 진행되야 합니다.  

배포가 완료되고 나면 아래 다이어그램처럼 소비자가 직접 HTTP call을 제공자에게 보낼 수 있게 됩니다.  

![API_contract_08](/images/post_img/APIcontract/API_contract_08.png)  


# 결론

아키텍트 관점에서는 Java, Spring 에 구속된다는 단점이 있습니다.  
또한 소비자 서비스가 OpenFeign을 사용해야 장점을 최대한 활용할 수 있다는 단점이 있습니다.  

하지만 Java, Spring으로 구성된 마이크로 서비스들로 서비스를 구성 중이라면 많은 장점을 가져갈 수 있는 전략입니다.  

Contract interface 계층이 실물로 존재하게 됨으로써  
기존에는 contract test, component test 단계에서 HTTP call을 보내보며 체크하던 HTTP spec과 테스트 데이터에 대해서  
Java call만으로도 체크가 가능하게 되어 컴파일 단계에서 HTTP spec을 체크하고 unit test단계에서 테스트 데이터를 체크할 수 있게 되었습니다.  

개발 오류에 대한 feedback을 컴파일, unit test 단계에서 받을 수 있게 되기 때문에 개발 속도가 크게 향상됩니다.  

또한 contract에 대한 관리를 제공자가 아닌 제 3자가 진행할 수 있는 구조가 됐습니다.  
Contract 계층은 MSA에서 굉장히 중요한 영역입니다. "소비자와 제공자 서비스 담당자들끼리 알아서 하세요." 라고 방관할 수도 있지만 많은 우여곡절이 있을 것이 쉽게 예상이 됩니다.  
모든 서비스들의 contract의 형상관리를 담당하는 관리자를 둠으로써 다수가 겪을 어려움을 피해갈 수 있을 것입니다. 
기존에 지켜지면 좋고 지켜지지 않으면 어쩔도리 없는 "개발자 행동강령"과 같은 구두 정책으로 관리가 되었다면  
관리자가 contract 실제 코드에 대한 형상관리를 하기 때문에 코드에 정책으로 관리가 되어 더욱 확실하게 관리가 될 수 있습니다.  

잘 관리된다면 아래 이미지 처럼 왼쪽의 그 복잡하던 MSA간의 연결관계를 오른쪽 처럼 단순하게 그려볼 수 있지 않을까 생각해봅니다.  

![API_contract_17](/images/post_img/APIcontract/API_contract_17.png)  

# 부록

## OpenFeign 간략설명

[OpenFeign](https://github.com/OpenFeign/feign)에 대해서 간략하게 설명을 합니다.  

OpenFeign은 HTTP client binder 입니다. 예를들어, Apache HttpClient를 좀 더 쉽게 사용할 수 있도록 URI, HTTP header, HTTP body 등을 바인딩 해줍니다.  
그것만이 아니라 [Netflix Ribbon](https://github.com/Netflix/ribbon), [Netflix Hystrix](https://github.com/Netflix/hystrix) 등을 이용해서 load balancing, circuit breaker 기능도 제공합니다. 하지만 이 글에서는 바인딩에 대해서만 일부 다룹니다.  

아래처럼 Java interface에 간단하게 annotation 으로 HTTP method, URI, 파라미터 정보들을 선언하는 것만으로도 사용이 가능합니다.  

~~~java
public interface GitHub {

  @RequestLine("GET /repos/{owner}/{repo}/contributors")
  List<Contributor> contributors(@Param("owner") String owner, @Param("repo") String repo);

  @RequestLine("POST /repos/{owner}/{repo}/issues")
  void createIssue(Issue issue, @Param("owner") String owner, @Param("repo") String repo);
}
~~~

GitHub이라는 interface에 대한 구현체를 작성하지 않아도 OpenFeign이 자동으로 실제 HTTP call 을 날려줍니다.  
자세한 샘플은 [링크](https://github.com/OpenFeign/feign#basics)된 페이지를 통해서 확인 하시기 바랍니다.  

그리고 Sprign Cloud를 위한 [Spring Cloud OpenFeign](https://cloud.spring.io/spring-cloud-static/spring-cloud-openfeign/3.0.0.M1/reference/html/) 도 제공됩니다.  

아까 위의 코드를 Spring Cloud openFeign 문법으로 바꾸면 아래처럼 바뀝니다.  

~~~java
@FeignClient(name = "${feign.name}", url = "${feign.url}")
public interface GitHub {

  @GetMapping("/repos/{owner}/{repo}/contributors")
  List<Contributor> contributors(@Param("owner") String owner, @Param("repo") String repo);

  @PostMapping("/repos/{owner}/{repo}/issues")
  void createIssue(Issue issue, @Param("owner") String owner, @Param("repo") String repo);
}
~~~

굉장히 익숙한 모양입니다. Spring MVC의 controller 문법과 거의 동일합니다.  
실제 호출 될 controller의 모양은 아래처럼 작성할 수 있습니다.  

~~~java
@RestController
public class GitHubController {

  @GetMapping("/repos/{owner}/{repo}/contributors")
  List<Contributor> contributors(@Param("owner") String owner, @Param("repo") String repo) {

      // do something...

      return contributors;
  }

  @PostMapping("/repos/{owner}/{repo}/issues")
  void createIssue(Issue issue, @Param("owner") String owner, @Param("repo") String repo) {

      // do something...

  }
}
~~~

