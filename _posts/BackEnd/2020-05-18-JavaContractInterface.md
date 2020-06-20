---
layout: post
title: "Contract Test 없이 MSA 도전 : Contract Interface"
date: 2020-05-18 00:00:00
lastmod: 2020-06-20 00:00:00
categories: BackEnd
tags: BackEnd Spring MSA
---

![API_contract_15](/images/post_img/APIcontract/API_contract_15.png)  

API 제공자(Provider), 소비자(Consumer) 모두 컴파일 단계에서 계약(Contract)의 API spec을 검증할 방법을 제안합니다.  
기존에 component, contract 테스트에서 받을 수 있었던 피드백을 컴파일 단계에서 받게되어 개발속도가 크게 향상됩니다.  

<!--more--> 

이 글에서 사용되는 샘플코드는 [https://github.com/dveamer/contract](https://github.com/dveamer/contract)에서 확인하실 수 있습니다.  
빌드 툴을 Maven 과 Gradle 두가지 준비했습니다. 원하시는 것을 사용하시면 됩니다. 제가 테스트시 사용한 빌드 툴 버전은 다음과 같습니다.  

  * Gradle : 5.6.4
  * Apache Maven : 3.6.0


# 기존의 Provider와 Consumer

여러 서비스들이 있는 가운데 댓글을 관리하는 comment 서비스가 제공자(provider)인 경우를 살펴보겠습니다.  
소비자(Consumer)인 다른 서비스들은 comment 서비스의 API를 HTTP 호출을 합니다. 아래 이미지의 점섬으로 표시된 화살표는 HTTP 호출을 의미합니다.  

![API_contract_10](/images/post_img/APIcontract/API_contract_10.png)  


### Provider의 어려움 

API spec이 바뀌면 다수의 소비자가 영향을 받기 때문에 제공자는 특정 개발 건 완료 후 contract test를 통해 모든 API에 대해서 API spec 변경여부 체크 필요합니다. 이 때 문제가 발견된다면 다행이지만 이미 개발이 많이 진행된 단계이기 때문에 시간 손실은 피할 수 없습니다.  

간혹 상태가 좋지 않은 프로젝트(?)에서는 제공자는 무턱대고 API spec을 바꾸고 배포해서 모든 소비자들이 변경사항에 대해 맞춰야하는 상황도 발생합니다. 이것은 프로젝트 전체적으로 봤을 때 굉장히 큰 손실을 발생시킵니다.  

### Consumer의 어려움 

API spec은 보통 문서를 통해 공유됩니다. 소비자는 문서를 보고 API spec에 맞춰 HTTP call하는 모듈을 만들어야 합니다. 숙련자라면 그나마 낫겠지만 이 과정에서 꽤나 많은 불필요한 시간이 소요됩니다. 또한 이 과정은 해당 API를 사용하는 소비자가 늘어날 때마다 반복됩니다.  

# Contract Interface 계층 추가

기존에는 API spec의 실물을 제공자가 들고 있었다면 (예: Spring MVC의 Controller)  
지금부터는 소비자와 제공자 사이에 ```Contract Interface``` 계층을 추가하고 그곳에 API spec의 실물을 위치 시킬 것입니다.  

소비자는 contract interface를 HTTP call이 아닌 Java call을 하게 되고  
제공자는 contract에 의존성을 갖게 되어 API spec 변경에 컴파일 단계에서 제약이 생깁니다.  
아래 이미지에서는 실선 화살표는 Java call을 의미합니다. 그리고 실선 흰색 화살표는 interface를 구현함을 의미합니다.  

![API_contract_11](/images/post_img/APIcontract/API_contract_11.png)  

HTTP call을 의미하는 점선의 화살표는 없지만 마이크로 서비스간의 통신이니 당연히 HTTP call은 존재합니다.  

아래의 이미지가 좀 더 구체적이고 정확한 표현입니다.  
소비자는 Feign Client라는 contract interface의 구현체를 Java call 합니다. 그러면 Feign Client가 제공자의 Controller를 HTTP call하게 됩니다.  

![API_contract_12](/images/post_img/APIcontract/API_contract_12.png)  

두가지 재미있는 점이 있습니다.  

  * 소비자의 Feign Client 그리고 제공자의 controller 둘다 contract interface의 구현체입니다.  
  * HTTP call을 수행하는 Feign Client는 컴파일시 자동으로 작성됩니다.  

같은 contract interface의 구현체이기 때문에 Feign Client와 controller의 spec은 동일할 수밖에 없습니다. 그 동일한 spec에 대해서 Feign Cleint가 HTTP call 모듈을 구현해주기 때문에 오류없이 정확하게 호출됩니다. 즉, 소비자측의 개발 양도 줄어들뿐더러 오류도 없어집니다.  

그리고 그보다 더 큰 장점은 소비자측 개발자는 Java call만으로도 contract에 대한 보장을 받기 때문에 매번 HTTP call을 하며 테스트를 해볼 필요가 없어집니다.  

제공자측 개발자는 실수로 API spec에 변경하는 실수를 하게 되면 대부분 ```Unimplemented Methods```가 존재한다는 컴파일 오류를 받게 됩니다. IDE를 사용하면 실수하는 순간 소스코드에 빨간 줄이 뜨기 시작하는 이야기가 됩니다.  

![API_contract_15](/images/post_img/APIcontract/API_contract_15.png)  

Spring Cloud Contract라는 솔루션과 같은 기존 방식에서는  
component, contract 테스트 단계에서 HTTP call을 통해 점검할 수 있었던 사항들을  
이제는 contract interface 계층을 추가함으로써 제공자, 소비자 모두 컴파일 단계에서 피드백을 받게되고 덕분에 개발 속도가 굉장히 빨라집니다.  

# 단점..?

아키텍트 관점에서 특정 언어, 특정 프레임워크에 종속되는 설계는 좋지 않습니다.  

근데 이글에서 설명하는 내용의 장점을 최대한 취하려면 Java, Spring을 사용해야 합니다. 이 점이 단점입니다.  
하지만 장점을 최대한 취하지 못한다는 것이지 polyglot을 못한다거나 다른 프레임워크를 사용하지 못하는 것은 아닙니다.  

아래에서 보여드리겠지만 stub server와 Swagger-UI 기능을 제공합니다.  
Swagger-UI 페이지를 통해 문서를 제공하고 수동으로 API를 호출해볼 수 있으며  
소비자 서비스는 stub server를 호출하며 테스트가 가능합니다. 기존 방식의 테스트를 수행할 수 있습니다.  

만약 MSA를 구성하고 있는 마이크로 서비스들 중에 Java, Spring 구성이 적지 않다면 충분히 장점을 취할 수 있는 방법입니다.  

# Contract 생성 과정 

Contract interface는 코드로 작성될 것이며 관리되어야 합니다. 예를들어, Git으로 형상관리가되고 Neuxs를 통해 jar 관리 및 배포가 됩니다.  
먼저 contract와 contract stub을 위한 git repository 두개가 필요합니다. 이 repository는 소비자, 제공자가 아닌 다른 maintainer가 관리합니다.  

그리고 이 글에서는 Consumer-Driven-Testing / Provider-Driven-Testing 와 같은 누가 contract를 작성해야하는지에 대한 이야기하지 않을 예정이지만 설명을 쉽게하기 위해서 소비자가 신규 API 추가 요청을 하고 제공자와 함께 contract를 만들어가는 과정을 생각해보겠습니다. 기술적으로는 두 방법 모두 수용 가능합니다.  

먼저 소비자가 contract, contract stub 코드를 작성해서 pull resquest를 보냅니다.  
그러면 code review 하듯이 소비자와 제공자가 contract, contract stub 코드를 다듬어 나갈 수도 있고 제공자가 주도적으로 코드를 다듬어 나갈 수 있습니다.  

아래 그림의 1, 2 과정을 소비자, 제공자가 함께 진행하는 것입니다.  

![API_contract_14](/images/post_img/APIcontract/API_contract_14.png)  

1, 2 과정이 끝났으면 contract stub을 이용해서 생성한 contract를 검증합니다.  
만약에 검증이 실패하면 다시 처음부터 contract, contract stub을 수정합니다.  

3번 과정인 contract 확인 작업이 완료되면 maintainer는 아래 사항들을 확인 후 pull request를 merge 해줍니다.  

  * API 변경 건 존재 여부 체크 (추가만 허용, 삭제는 더 엄밀히 검토 후 허용)
  * Contract test의 정상 수행 여부
  * Contract, contract stub 의 버전 증가여부, 버전 일치여부

그 뒤 maintainer는 contract, contact stub에 대한 jar를 각각 만들어서 원격 Maven repository에 배포 합니다.  
참고로 maintainer가 체크, 진행하는 과정은 대부분 CI/CD 에서 자동화처리가 가능한 영역입니다.  

## Contract Interface 추가 

드디어 contract interface를 실제 코드로 보실 수 있습니다. 해당 코드는 contract-comment jar로 배포됩니다.  
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

    String PV_articleId = "articleId";

    String PATH_loadCommentsByArticleId = "/articles/{articleId}/comments";
    @GetMapping(path= PATH_loadCommentsByArticleId)
    List<CommentDto> loadCommentsByArticleId(@PathVariable(PV_articleId) String articleId);

    String PATH_loadArticleIdHavingNumerousComments = "/articles";
    @GetMapping(path= PATH_loadArticleIdHavingNumerousComments)
    List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(@SpringQueryMap ConditionDto conditionDto);

}
~~~

소비자가 Java call을 통해 호출 할수 있는 순수한 Java interface입니다.  
CommentDto, ArticleCommentCountDto 같은 DTO(Data Transfer Object)도 보입니다. DTO도 contract-comment jar에 포함되기 때문에 소비자, 제공자 모두 DTO를 새로 만들 필요 없으며 둘 사이의 DTO 불일치는 발생할 수 없습니다.  


OpenFeign annotation들이 어떻게 활용 되는지는 추후에 다시 다루게 됩니다. OpenFeign에 대해 처음들어보셨다면 [부록 - OpenFeign 간략설명](# OpenFeign 간략설명)을 참고해주시기 바랍니다.  
특이한 점으로 ```@SpringQueryMap``` 이라는 OpenFeign에서 제공하는 annotation이 loadArticleIdHavingNumerousComments의 conditionDto 파라미터에 적용됐습니다.  
HTTP spec을 정의하는 annotation은 모두 Spring annotaion을 사용하지만 유일하게 예외인 ```@SpringQueryMap```가 사용됐습니다. ```@SpringQueryMap```은 query-string 으로 전달될 파라미터들을 의미합니다.  

### Contract 패키징 

다음으로 만들 contract stub은 contract에 의존성을 갖습니다.  
먼저 contract를 jar로 묶어서 로컬 Maven repository 에 배포해야 합니다.  

~~~terminal
contract-comment$ gradle install 

OR

contract-comment$ mvn install
~~~

## Contract Stub 추가

Contract stub 코드는 다양한 목적으로 사용됩니다.  

그 중 첫번째 목적은 테스트를 통해 최초 contract를 검증하기 위함입니다.  
그리고 그 테스트는 추후에 CI/CD 과정에서 매번 contract 검증에도 사용할 수 있습니다.  

<br>

그리고 두번째 목적은 소비자, 제공자의 unit 테스트 데이터를 일치 시키기 위함입니다.  
소비자 unit test에서는 contract의 stub으로 사용되고  
제공자의 unit test에서는 contract의 예상응답(expeted value)로 사용함으로써  
각각 다른 시간에 다른 장소에서 unit test를 진행하지만 동일한 테스트 데이터를 가지고 테스트가 진행될 수 있습니다.  


<br>

그리고 contract stub은 server로 기동도 가능하며 아래 기능을 제공할 수 있습니다.  

  * Stub server
  * Swagger-UI 제공 

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

CommentContract를 구현체이자 stub 기능을 제공하는 CommentContractStub 입니다. 

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
    @GetMapping(path=PATH_loadCommentsByArticleId)
    public List<CommentDto> loadCommentsByArticleId(@PathVariable(PV_articleId) String articleId) {
        if(articleId.equals(ArticleFixture.articleId1())) {
            return CommentFixture.commentList1();
        }

        return CommentFixture.commentList2();
    }

    @Override
    @GetMapping(path=PATH_loadArticleIdHavingNumerousComments)
    public List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(ConditionDto conditionDto) {
        return ArticleCommentCountFixture.articleCommentCountList(conditionDto.getBiggerThan(), conditionDto.getBeforeDays());
    }

}
~~~

구현된 메소드들의 내용을 살펴보면 소비자와 제공자의 계약과정에서 협의된 간단한 케이스들에 해당되는 테스트 데이터들이 담겨있는 stub입니다. 저는 간단하게 작성했지만 성공, 실패, 경계(Boundary) 테스트 케이스에 대해서 조금만 더 다양한 케이스를 작성하시면 됩니다.  

그리고 이것은 stub이지만 제공자의 controller와 유사하게 생겼습니다. 자세히 보시면 ```@RestController```, ```@GetMapping```, ```@PathVariable``` annotation들이 선언되어있습니다.  
이것을 Spring Boot로 기동시 contract를 만족시키는 stub server로 활용됩니다.  



### Contract Test Codes

Contract-comment-stub jar는 contract test 코드를 가지고 있습니다.  

이 contract test는 HTTP call을 보내게 됩니다. 예상(expected) 결과는 stub이 되고 실제(actual) 결과는 HTTP call에 대한 응답입니다.  
CommentContract interface의 구현체를 OpenFeign을 이용해서 생성하고 HTTP call 모듈로 활용합니다. 이 것은 소비자가 사용하게 될 HTTP call 모듈과 동일합니다.  

~~~java
package com.dveamer.contract.comment;

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
        ...(생략)

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
        List<ArticleCommentCountDto> expectedList = new CommentContractStub().loadArticleIdHavingNumerousComments(condition);
        List<ArticleCommentCountDto> actualList = commentContract.loadArticleIdHavingNumerousComments(condition);
        assertThat(actualList, is(samePropertyValuesAs(expectedList)));
    }

}


~~~


테스트를 실행시켜보면 HTTP 커넥션이 안되서 ```java.net.ConnectException``` 에러가나는 것을 확인할 수 있습니다.  

~~~terminal

contract-comment-stub$ gradle contractTest

> Task :test

com.dveamer.contract.comment.CommentContractTests > loadCommentsByArticleIdTest() FAILED
    feign.RetryableException at CommentContractTests.java:47
        Caused by: java.net.ConnectException at CommentContractTests.java:47

com.dveamer.contract.comment.CommentContractTests > loadArticleIdHavingNumerousCommentsTest() FAILED
    feign.RetryableException at CommentContractTests.java:55
        Caused by: java.net.ConnectException at CommentContractTests.java:55

~~~

테스트 케이스가 HTTP call을 보내게 됨으로써 contract test를 하기 위한 준비가 모두 끝났습니다.  


여기서 주의깊게 봐야할 점은, 여전히 테스트의 예상결과는 stub이지만 실행결과는 HTTP call의 응답이라는 점입니다.  

만약에 해당 테스트가 성공하고  
소비자가 unit test에서 contract의 mock으로 stub을 사용하고  
제공자가 unit test에서 contract의 예상응답(expeted value)로 stub을 사용한다면  

```stub 데이터``` == ```소비자 unit test의 stub``` == ```contract test의 예상응답``` == ```HTTP call에 의한 실제응답``` == ```제공자 unit test의 예상응답``` 이 성립하게 됩니다.  
이를 통해 소비자와 제공자가 동일한 테스트 데이터로 개발해 나감을 확신할 수 있습니다.  

컴파일 단계에서 HTTP spec에 대한 검증가능해졌다면 unit test 단계에서 테스트 데이터에 대한 검증 가능해진다고 보시면 됩니다.  

### Contract Test 수행

최초 contact 검증을 위한 contract 테스트를 수행합니다.  

먼저 stub server를 기동시키고 contract test 코드를 실행시켜서 stub server의 API를 HTTP call할 것입니다.  
Contract test 코드와 stub server의 API가 http spec, 테스트 케이스를 동일하게 갖췄다는 것을 확인하는 것입니다.  

stub server를 기동시킵니다.  

~~~terminal
contract-comment-stub$ gradle bootRun

OR 

contract-comment-stub$ mvn spring-boot:run
~~~

다른 터미널에서 contract test를 실행합니다.  

~~~terminal
contract-comment-stub$ gradle contractTest

OR

contract-comment-stub$ mvn -Dapi.contract.comment.url=http://localhost:8080 test
~~~

성공여부를 확인합니다. 만약에 실패한다면 contract, contract stub 중 잘못된 곳을 찾아서 수정해줘야 합니다.  
성공하게 되면 contract, contract stub에 대한 Pull Reqeust가 merge가 진행될 수 있도록 처리합니다.  

그러면 이제 contract, contract stub에 대한 jar 배포만 진행되면 소비자와 제공자가 각자 개발을 진행할 수 있습니다.  
그리고 contract maintainer는 CI(Continuous Integration) 과정의 contract test 를 통해 제공자가 HTTP spec 및 테스트 케이스를 만족시키는지 매번 체크할 수 있습니다.  


![API_contract_08](/images/post_img/APIcontract/API_contract_08.png)  


### Contract Stub Jar 패키징

아래 명령어를 수행하면 ```build/libs``` 디렉토리와 ```~/.m2/repository/com/dveamer/contract-comment-stub/0.0.1``` 디렉토리에 ```contract-comment-stub-0.0.1.jar``` 파일이 생깁니다.  

~~~terminal
contract-comment-stub$ gradle install


OR

contract-comment$ mvn -Dspring-boot.repackage.skip=true install
~~~


### Stub Server Jar 패키징 

 
Gradle 명령어를 수행하면 ```build/libs``` 디렉토리에 ```contract-comment-stub-server-0.0.1.jar``` 파일이 생깁니다.  

~~~terminal
contract-comment-stub$ gradle bootJar
~~~

Maven 명령어를 수행하면 ```target``` 디렉토리에 ```contract-comment-stub-server-0.0.1.jar``` 파일이 생깁니다.  

~~~terminal
contract-comment-stub$ mvn -DfinalName=contract-comment-stub-server package 
~~~

### Swagger 기능 제공

Swagger 설정을 해뒀기 때문에 stub server를 기동 후 ```localhost:8080/swagger-ui.html``` 에 접속하시면 아래와 같은 API document를 공유할 수 있고 수동으로 테스트도 진행할 수 있습니다.  

![API_contract_16](/images/post_img/APIcontract/API_contract_16.png)  


# Consumer 개발 진행

아래의 다이어그램처럼 소비자 unit 테스트 코드는 contract 의 구현체인 stub을 호출하게 됩니다.  

![API_contract_07](/images/post_img/APIcontract/API_contract_07.png)  


소비자측의 개발속도는 아래의 이유로 크게 향상 됩니다.  

  * 제공되는 stub 활용  
  * Stub server 기동유무, 제공자의 개발완료 등과 같은 외부요인과 무관하게 테스트 가능  
  * Contract interface를 Java call하는 것만으로도 contract의 HTTP spec 준수됨 (컴파일 단계 체크)  
  * Unit test만으로도 contract의 테스트 데이터 준수됨 (unit test 단계 체크)   
  * 시간이 오래 걸리고 변수가 많은 HTTP call 수행 없이 개발 진행 가능  
  * HTTP call 모듈 작성 없이 OpenFeign을 이용한 HTTP call 호출  

이와 같은 소비자측 개발속도 향상은 특정 API에 대해 제공자 서비스는 1개지만 소비자 서비스는 다수라는 점에서 더 빛을 발합니다.  

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

소비자는 CommentContract를 Java call하기만 하면 됩니다. OpenFeign가 CommentContract의 구현체를 자동으로 생성하고 HTTP call 수행을 대신해 줍니다.  

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

그래도 뭔가 확인 작업이 필요할 것 같으신가요? 개발하시다가 뭔가 오타를 내서 IDE에서 빨간줄로 오류를 표시해주고 있는 상황이 아니라면 더 이상 확인할 것도 확인할 수 있는 것도 없습니다.  

## Unit Test In Consumer

Service 구현에 대한 unit test 작성시에는 contract stub을 contract의 mock으로 사용하기만 하면 됩니다.  

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

Unit test 과정에서 wasCalled 체크를 위해 CommentContractStub를 직접 사용하지 않고 CommentContractStubWrapper로 감싸서 사용했습니다.  
응답에 대한 내용은 CommentContractStub이 모두 제공하기 때문에 CommentContractStubWrapper는 wasCalled에 대한 내용만 담고 있으면 됩니다.  

~~~java
package com.dveamer.article.component;

import com.dveamer.contract.comment.ArticleCommentCountDto;
import com.dveamer.contract.comment.CommentContract;
import com.dveamer.contract.comment.CommentDto;
import com.dveamer.contract.comment.ConditionDto;
import com.dveamer.contract.comment.stub.CommentContractStub;
import com.dveamer.contract.comment.stub.ConditionFixture;

import java.util.List;

public class CommentContractStubWrapper implements CommentContract  {

    private final CommentContract commentContract;

    private boolean loadCommentsByArticleId_wasCalled = false;
    private boolean loadArticleIdHavingNumerousComments_wasCalled = false;

    CommentContractStubWrapper() {
        commentContract = new CommentContractStub();
    }

    public boolean loadCommentsByArticleId_wasCalled() {
        return loadCommentsByArticleId_wasCalled;
    }

    public boolean loadArticleIdHavingNumerousComments_wasCalled() {
        return loadArticleIdHavingNumerousComments_wasCalled;
    }

    @Override
    public List<CommentDto> loadCommentsByArticleId(String articleId) {
        loadCommentsByArticleId_wasCalled = true;
        return commentContract.loadCommentsByArticleId(articleId);
    }

    @Override
    public List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(ConditionDto conditionDto) {
        loadArticleIdHavingNumerousComments_wasCalled = true;
        return commentContract.loadArticleIdHavingNumerousComments(ConditionFixture.conditionDto());
    }
}
~~~


# Provider 개발 진행 

제공자의 controller 작성과 그에 대한 unit test를 작성에 대해서 살펴 볼 것입니다.  

제공자측 개발속도는 아래의 이유로 크게 향상 됩니다.  

  * 제공되는 stub 활용  
  * Contract interface를 구현하는 것만으로도 contract의 HTTP spec이 대부분 준수됨 (컴파일 단계 체크)  
  * Unit test만으로도 contract의 테스트 데이터 준수됨 (unit test 단계 체크)  
  * 시간이 오래 걸리고 변수가 많은 HTTP call 수행 없이 개발 진행 가능  

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

제공자의 controller는 contract interface의 구현체이기 때문에 controller의 public method는 contract 의 method와 동일한 파라미터 타입, 응답 타입을 갖을 수 밖에 없습니다.  

다만 annotation과 PATH, PV 상수들은 강제성도 없고 사람의 개입이 들어갈 가능성이 있습니다. 가장 좋은 방법은 이미 검증 된 stub의 CommentContractStub의 메소드들을 그대로 복사해오면 검증된 annotation들을 그대로 가져올 수 있습니다. 거의 복사만 하면 되는 수준이기 때문에 오류를 생성할 가능성은 매우 낮습니다. 여기서 만약에 실수가 발생하더라도 추후 CI/CD의 contract test에서 문제점은 바로 발견됩니다. 앞서 최초 contract 테스트를 하며 contract에 대한 검증은 완료된 상태입니다. 여기서 문제점이 발견된다면 그것은 제공자가 실수가 명백하고 제공자 측에서 수정을 해야합니다. 하지만 이미 많은 것들이 제약되어있기 때문에 발견된 문제점으로인해 큰 변경이 있을리 없습니다. 이로 인해 소비자, 제공자 모두 구현로직이 바뀔일은 없습니다.  

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
    @GetMapping(PATH_loadCommentsByArticleId)
    public List<CommentDto> loadCommentsByArticleId(@PathVariable(PV_articleId) String articleId) {
        ...(생략)
    }

    @Override
    @GetMapping(PATH_loadArticleIdHavingNumerousComments)
    public List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(ConditionDto conditionDto) {
        ...(생략)
    }

    ...(생략)

}
~~~


## Unit Test For Contract In Provider

Contract 구현에 대한 unit test 작성시에는 contract-comment-stub에서 제공하는 Stub을 예상(expected) 결과로 사용하면 됩니다.  

~~~java
package com.dveamer.comment.web;


import com.dveamer.contract.comment.CommentContract;
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
        mockMvc.perform(get(CommentContract.PATH_loadCommentsByArticleId, ArticleFixture.articleId1()))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(content().string(JsonConverterUtil.toJson(expectedCommentDtoList)));

        assertThat(commentService.loadCommentsByArticleId_wasCalled(), equalTo(true));
    }

    @Test
    public void loadArticleIdHavingNumerousComments_success() throws Exception {
        ConditionDto condition = ConditionFixture.conditionDto();
        List<ArticleCommentCountDto> expectedArticleCommentCountDtoList = new CommentContractStub().loadArticleIdHavingNumerousComments(condition);
        mockMvc.perform(get(CommentContract.PATH_loadArticleIdHavingNumerousComments)
                            .queryParam("beforeDays", Integer.toString(condition.getBeforeDays()))
                            .queryParam("biggerThan", Integer.toString(condition.getBiggerThan())))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(content().string(JsonConverterUtil.toJson(expectedArticleCommentCountDtoList)));

        assertThat(commentService.loadArticleIdHavingNumerousComments_wasCalled(), equalTo(true));
    }


}
~~~


## Consumer, Provider 배포 

제공자, 소비자의 배포가 모두 이뤄지고 나면 아래 다이어그램처럼 소비자가 직접 HTTP call을 제공자에게 보낼 수 있게 됩니다.  

![API_contract_12](/images/post_img/APIcontract/API_contract_12.png)  

# 결론

HTTP spec에 대한 피드백을 컴파일 단계에서 받고 테스트 데이터에 대한 피드백을 유닛 테스트 단계에서 받을 수 있게 되어 빨라진 피드백만큼 개발속도가 크게 향상 되었습니다.  

![API_contract_15](/images/post_img/APIcontract/API_contract_15.png)  


그리고 Contract interface 계층이 실물로 존재하게 됨으로써 contract에 대한 관리를 제공자가 아닌 제 3자가 진행할 수 있는 구조가 됐습니다.  
Contract 계층은 MSA에서 굉장히 중요한 영역입니다. "소비자와 제공자 서비스 담당자들끼리 알아서 하세요." 라고 방관할 수도 있지만 많은 우여곡절이 있을 것이 쉽게 예상이 됩니다.  
모든 서비스들의 contract의 형상관리를 담당하는 관리자를 둠으로써 다수가 겪을 어려움을 피해갈 수 있을 것입니다. 
기존에 지켜지면 좋고 지켜지지 않으면 어쩔도리 없는 "개발자 행동강령"과 같은 구두 정책으로 관리가 되었다면  
관리자가 contract 실제 코드에 대한 형상관리를 하기 때문에 코드에 정책으로 관리가 되어 더욱 확실하게 관리가 될 수 있습니다.  

잘 관리된다면 아래 이미지 처럼   왼쪽의 그 복잡하던 마이크로 서비스간의 연결관계를 오른쪽 처럼 단순하게 그려볼 수 있지 않을까 생각해봅니다.  

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
  List<Contributor> contributors(@PathVariable("owner") String owner, @PathVariable("repo") String repo);

  @PostMapping("/repos/{owner}/{repo}/issues")
  void createIssue(Issue issue, @PathVariable("owner") String owner, @PathVariable("repo") String repo);
}
~~~

굉장히 익숙한 모양입니다. Spring MVC의 controller 문법과 거의 동일합니다.  
실제 호출 될 controller의 모양은 아래처럼 작성할 수 있습니다.  

~~~java
@RestController
public class GitHubController {

  @GetMapping("/repos/{owner}/{repo}/contributors")
  List<Contributor> contributors(@PathVariable("owner") String owner, @PathVariable("repo") String repo) {

      // do something...

      return contributors;
  }

  @PostMapping("/repos/{owner}/{repo}/issues")
  void createIssue(Issue issue, @PathVariable("owner") String owner, @PathVariable("repo") String repo) {

      // do something...

  }
}
~~~

