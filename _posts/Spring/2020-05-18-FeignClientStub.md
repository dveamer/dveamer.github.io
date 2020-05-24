---
layout: post
title: "Java Interface Contract : 컴파일, Unit Test 단계에서 Contract 체크 / OpenFeign을 이용한 개발속도 향상"
date: 2020-05-18 00:00:00
lastmod: 2020-05-24 00:00:00
categories: BackEnd
tags: BackEnd Spring MSA
---

![API_contract_06](/images/post_img/APIcontract/API_contract_06.png)

API contract를 실제 사용할 Java interface 코드로 작성합니다.  

기존에 제공자(Provider)는 unit test단계에서 HTTP spec에 대한 feedback을 받을 수 있었지만 이제는 컴파일 단계에서 HTTP spec에 대한 feedback을 받게되기 때문에 개발 속도가 크게 향상 됩니다.  

기존에 소비자(Consumer)가 테스트할 때 제공자(Provider)와 합의된 HTTP spec과 데이터(stub)에 대해 정상적으로 처리되는지 검증을 하는 단계가 component test이고 HTTP call을 이용해야만 했습니다. 이제는 HTTP spec에 대해서는 컴파일 단계에서 feedback을 받게되고 데이터(stub)에 대해서는 unit test에서 feedback을 받게 되기 때문에 개발 속도가 크게 향상 됩니다.  

OpenFeign을 이용하기 때문에 작성된 API contract interface의 구현체가 컴파일시 자동생성되고 소비자(Consumer)는 HTTP call을 위한 구현을 하지 않아도 됩니다.  


<!--more-->

참고로 contract, component 테스트를 위해 제가 직접 고민해서 설계한 방법입니다.  

이 글에서 사용되는 샘플코드는 [https://github.com/dveamer/contract](https://github.com/dveamer/contract)에서 확인하실 수 있습니다.  

# Weakness

단점부터 이야기 하겠습니다.  

MSA의 장점인 polyglot에 악영향을 줍니다.  
앞으로 설명드리는 방법을 활용하려면 Java, Spring이 필요합니다.  
다른 언어, 프레임워크를 사용하는 마이크로 서비스는 혜택을 받을 수 없습니다.  


두번째 단점은, 아키텍트 관점에서 보면 굉장히 좋지 않은 설계라는 점입니다.  
설명드릴 방법의 편리함을 최대한 활용하다보면 OpenFeign이라는 API client 모듈을 사용하게 됩니다.  
언어는 봐줄 수 있다고 쳐도 특정 프레임워크에 의존하는 설계는 이미 좋지 않은 설계입니다.  
그런데 API client 모듈까지 의존성이 생기는 설계는 확실하게 좋지 않은 설계입니다.  

OpenFeign이 반드시 필요한 것은 아니고 그 역할을 자체적으로 개발하거나 다른 솔루션을 사용해서 대체할 수도 있습니다.  

그래도 우연찮게 현재 운영중인 대부분의 마이크로 서비스가 Java, Spring을 사용한다면  
또한 OpenFeign을 사용해도 상관없다면 충분히 유용하게 활용할 수 있는 방안이기 때문에 공유합니다.  


# Contract Interface 계층 추가

![API_contract_04](/images/post_img/APIcontract/API_contract_04.png)  

댓글을 관리하는 comment 서비스가 있습니다.  
그리고 comment 서비스는 댓글 정보만이 아니라 댓글이 어떤 글(article)에 달렸는지에 대한 정보도 함께 관리합니다.  

Comment 서비스가 제공하는 API는 다른 여러 서비스들이 아래처럼 HTTP 호출을 할 수 있습니다.  
위 이미지의 점선으로 표시된 화살표는 HTTP call을 의미합니다.  

![API_contract_05](/images/post_img/APIcontract/API_contract_05.png)  

그런데 소비자와 제공자 사이에 contract-comment라는 인터페이스를 만들어 보겠습니다.  
새롭게 소개된 contract-comment는 실제 Java interface 로써 jar 형태로 제공되며 소비자, 제공자 모두 사용합니다.  

소비자 측에서는 interface를 java call을 하게 됩니다. 물론 interface의 구현체가 HTTP call을 진행해야하긴 합니다.  
하지만 HTTP call을 담당할 interface 구현체에 대해서는 소비자가 신경쓰지 않도록 만들 것이기 때문에 아래 그림에서는 HTTP call을 의미하는 점선의 화살표가 없고 java call을 의미하는 실선의 화살표만 존재합니다.  

제공자 측에서는 HTTP call을 받아들이는 interface를 구현합니다. 소비자 측에 제공된 interface와 동일한 interface이기 때문에 API spec에 대한 불일치 확률이 굉장히 낮습니다.  

위 이미지의 검은색으로 채워진 실선 화살표는 java call을 의미합니다.  
그리고 희색으로 비워진 실선 화살표는 interface를 구현함을 의미합니다.  


## Contract Interface 계층의 장점

소비자, 제공자 모두 CommentContract를 활용하게 만들어서 모두 contract-comment jar에 대한 의존성을 가지도록 만들 것입니다.  
소비자, 제공자 사이에 contract라는 계층이 추가되고 contract가 소비자, 제공자 보다 더 상위계층으로 구성되게 됩니다.  

제공자 입장에서는 쓸데 없는 의존성이 추가된 것으로 보이지만  
전체 서비스의 입장에서는 서비스간의 계약관계가 제공자의 기능보다 더 중요합니다. Contract interface 계층이 더 상위계층으로 설계되는 것이 맞다고 생각합니다.  

상위 계층의 contract가 실물로 존재하게 됨으로써 계약관계에 대한 안전성이 추가 됩니다.  

예를들어, 제공자 측의 개발자 실수로 인해 API가 수정 되었을 때 기존에는 contract test 과정에서 제한될 수 있었습니다. (만약 unit test를 작성해뒀다면 다행히 좀 더 일찍 발견할 수 있습니다.)  
그런데 API contract interface가 실물로 존재하게 됨으로써 컴파일 과정에서 API 수정에 대해 알 수 있게 됩니다.  

기존에 contract test에서 체크하는 API spec 변경여부에 대한 체크가 이제는 컴파일 단계에서 이뤄지며 개발 속도를 향상시킵니다.  



# 협업 작업 과정

![API_contract_09](/images/post_img/APIcontract/API_contract_09.png)  

Contract 계층은 개념적인 존재가 아니라 코드로 존재하는 실제 계층입니다.  
즉, 그 코드는 생성되어야하고 보관되어야하고 관리되어야 합니다.  

일단 contract, contract stub을 위한 형상관리 repository가 필요합니다.  
그리고 소비자, 제공자가 아닌 contract repository에 대한 maintainer가 필요합니다.  
물론 현실적으로 어렵다면 제공자가 그 maintainer가 될 가능성이 높습니다.  

아래와 같은 작업이 진행되고 만약에 contract test 과정에서 문제가 있다면 contract 변경 작업부터 다시 하게 됩니다.  

  * Contract 추가 요청 ( 변경은 불가 - 추가/삭제 만으로 처리 )
  * Contract test, stub 추가 요청
  * 제공자측 API 생성 (HTTP spec 체크를 위함)
  * Contract test 진행

최종적으로 contract test가 완료 후 contract 와 contract stub 소스가 각 형상관리 respository에 merge 되도록 해야합니다.  

해당 maintainer는 최종 Pull Request에 대해 체크해야할 사항들이 있습니다.  

  * API 변경 건 존재 여부 체크 (추가/삭제만 허용)
  * Contract test의 정상 수행 여부
  * Contract, contract stub 의 버전 증가여부, 버전 일치여부

모든 체크 사항이 만족되면 contract, contract stub을 jar로 패키징해서 Maven Repository 혹은 Nexus에 배포합니다.  

참고로 앞서 maintainer가 체크, 진행하는 과정은 CI/CD 에서 자동화처리가 가능한 영역입니다.  

## API Contract 추가/변경 요청

소비자가 새로운 API가 필요하다면 직접 contract interface을 작성해서 Pull Request를 보낼 수 있습니다.  
하지만 소비자는 제공자 서비스에 대해서 정확하게 알고 있지 않기 때문에 제공자의 검토 및 수정이 필요할 것입니다.  
그 과정은 code review하듯이 진행할 수도 있고 혹은 소비자의 요청사항에 맞춰서 제공자가 직접 진행할 수도 있습니다.  

이 때 생성되야하는 contract 코드의 내용은 아래와 같이 단순한 Java interface를 작성하는 내용입니다.  


### Java Contract Interface

아래 interface 코드는 contract-comment jar에 포함될 ```CommentContract.java``` 입니다.  
OpenFeign 관련 설정들을 함께 포함하고 있습니다.  
OpenFeign에 의해서 CommentContract의 구현체는 자동으로 생성되며 소비자는 CommentContract을 Java call하게 되면 OpenFeign이 구현해준 구현체가 HTTP call을 대신해줍니다. 여기서 사람의 개입이 없기 때문에 오류가 생성될 가능성이 없습니다.  
제공자 측에서는 CommentContract의 구현체를 만들면 됩니다. 만들 때 ```@FeignClient``` annotation만 ```@RestController```로 변경해주고 다른 annotation은 거의 그대로 사용하면 됩니다. 사람의 개입이 있지만 거의 복사만 하면 되는 수준이기 때문에 오류를 생성할 가능성은 매우 낮습니다.  

또한 DTO(Data Tracnfer Object)도 contract-comment jar가 포함하고 있기 때문에 소비자, 제공자 모두 DTO를 새로 만들 필요 없으며 둘 사이의 DTO 불일치는 발생할 수 없습니다.  

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

@FeignClient 라는 OpenFeign 관련 annotation이 사용됐습니다. OpenFeign에 대해 처음들어보셨다면 [부록 - OpenFeign 간략설명](# OpenFeign 간략설명)을 참고해주시기 바랍니다.  

## Contract Stub 추가 요청

필요하다면 contract stub도 소비자가 직접 작성해서 Pull Request를 보낼 수 있습니다.  
역시나 소비자는 제공자 서비스에 대해서 정확하게 알고 있지 않기 때문에 제공자의 검토 및 수정이 필요할 것입니다.  
이 과정도 code review하듯이 진행할 수도 있고 혹은 소비자의 요청사항에 맞춰서 제공자가 직접 진행할 수도 있습니다.  

기존에는 stub을 만들면 stub server를 구성하고 소비자가 직접 HTTP call을 보내봄으로써 구현이 제대로 됐는지 확인하는 목적으로 사용했습니다.  
즉, 기껏 만들어둔 stub은 소비자의 component test에만 활용이 됐습니다.  

앞으로 만들어질 contract Interface 에 대한 stub은 더 다양하게 활용됩니다.  
소비자는 unit test, component test에 활용가능하고 제공자는 unit test, contract test에 활용할 수 있습니다.  
Unit test를 작성 시 stub을 활용하기 때문에 테스트 코드 작성시간을 아낄 수 있습니다.  
또한 unit test, component test 시에 HTTP call 없이도 HTTP spec에 대한 체크가 이뤄지기 때문에 테스트 시간도 단축되어 소비자측 개발 속도를 크게 향상할 수 있습니다.  

그리고 이 단계에서는 contract stub만이 아니라 contract test 코드도 함께 작성이 됩니다.  
contract stub을 이용해서 contract test를 생성하기 때문에 간단하게 진행되고 stub과 contract test를 일치시킴으로써 소비자와 제공자가 동일한 테스트 데이터로 개발해나감을 확신할 수 있습니다.  


해당 샘플 코드는 [https://github.com/dveamer/contract/tree/master/contract-comment-stub](https://github.com/dveamer/contract/tree/master/contract-comment-stub)에서 확인 가능합니다.  

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

단순히 CommentContract에 대한 stub 구현체 입니다.  
contract-comment-stub이라는 jar파일로 배포될 것이며 소비자와 제공자가 테스트 코드에서 사용하게 됩니다.  

~~~java
package com.dveamer.contract.comment.stub;

import com.dveamer.contract.comment.ArticleCommentCountDto;
import com.dveamer.contract.comment.CommentContract;
import com.dveamer.contract.comment.CommentDto;
import com.dveamer.contract.comment.ConditionDto;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
public class CommentContractStub implements CommentContract {

    @Override
    public List<CommentDto> loadCommentsByArticleId(String articleId) {
        if(articleId.equals(ArticleFixture.articleId1())) {
            return CommentFixture.commentList1();
        }

        return CommentFixture.commentList2();
    }

    @Override
    public List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(ConditionDto conditionDto) {
        return ArticleCommentCountFixture.articleCommentCountList(conditionDto.getBiggerThan(), conditionDto.getBeforeDays());
    }

}
~~~


### Contract Test Codes

Contract-comment-stub jar는 contract test 코드를 가지고 있습니다.  
Contract test의 예상(expected) 결과는 stub이 되고 실제(actual) 결과는 제공자 서비스의 응답 값이 됩니다.  
그리고 HTTP call을 호출하는 commentContract의 구현체는 OpenFeign이 생성해준 구현체로 소비자가 사용한 구현체와 동일합니다.  

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
class CommentTests {

	Logger logger = LoggerFactory.getLogger(this.getClass());

	private CommentContract commentContract;

	@BeforeAll
	void setup() {
		Properties properties = System.getProperties();
		String profiles = properties.getProperty("testType");
		if(profiles==null||!profiles.contains("contractTest")) {
			commentContract = new CommentContractStub();
			return;
		}

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

OpenFeign에 의존성을 갖고 있는 테스트 코드입니다. OpenFeign에 대해 처음들어보셨다면 [부록 - OpenFeign 간략설명](# OpenFeign 간략설명)을 참고해주시기 바랍니다.  

```build.gradle``` 파일을 확인해보면 아래와 같이 test, contractTest로 2개의 테스트 task가 존재합니다.  

~~~gradle
...(생략)

test {
	useJUnitPlatform()
}

task contractTest(type: Test, group: 'verification') {
	systemProperty "testType", "contractTest"
	systemProperty "api.contract.comment.url", "http://localhost:8080"
	useJUnitPlatform()
}

...(생략)

~~~

그 중 contractTest를 실행하면 HTTP call을 통한 contract test가 진행됩니다.  

~~~terminal
$ gradle contractTest
~~~

일반 test를 실행하면 HTTP call이 아닌 CommentContractStub.java에 대한 Java call을 하게 됩니다.  
재미있게도 예상결과와 실제결과 모두 stub을 통한 결과이기 때문에 항상 참일 수 밖에 없습니다.  
이러한 코드가 존재하는 이유는 contract test 케이스가 제대로 작성됐는지 체크하기 위해서 사용합니다.  
주로 contract test 케이스 작성시 사용하게 된다고 보시면 됩니다.  

~~~terminal
$ gradle test
~~~

## Provider API 작업

Contract interface에 대한 구현체를 작성합니다. 아직 Service 구현까지는 불필요 합니다.  


### build.gradle

Stub을 활용하기 위해서 contract-comment-stub jar 의존성 추가를 testImplementation이 아닌 implementation으로 설정합니다.  
이것은 추후에 service 개발이 진행되고 나면 testImplementation으로 변경하면 됩니다.  

~~~gradle

dependencies {
	
  ...(skipped)

	implementation 'org.springframework.cloud:spring-cloud-starter-openfeign'
	implementation 'com.dveamer:contract-comment:0.0.1'
	implementation 'com.dveamer:contract-comment-stub:0.0.1'
}

~~~

### Implementation Of Contract In Provider

단순히 CommentContract 를 구현하는 CommentController를 만들고 Stub을 이용해서 결과 처리 합니다. Service에 대한 구현은 추후에 다시 진행합니다.  

Contract와 동일한 public method를 갖게되기 때문에 파라미터 타입과, 응답 타입 등이 모두 소비자와 동일할 수 밖에 없습니다. 만들 때 ```@FeignClient``` annotation만 ```@RestController```로 변경해주고 다른 annotation은 거의 그대로 사용하면 됩니다. 사람의 실수로 annotation이 잘못 설정될 수는 있습니다. 하지만 거의 복사만 하면 되는 수준이기 때문에 오류를 생성할 가능성은 매우 낮습니다.  



~~~java
package com.dveamer.comment.web;

import com.dveamer.contract.comment.ArticleCommentCountDto;
import com.dveamer.contract.comment.CommentContract;
import com.dveamer.contract.comment.CommentDto;
import com.dveamer.contract.comment.ConditionDto;

...(생략)

@RestController
public class CommentController implements CommentContract {

    @Override
    @GetMapping("/articles/{articleId}/comments")
    public List<CommentDto> loadCommentsByArticleId(@PathVariable("articleId") String articleId) {
        return new CommentClientStub().loadCommentsByArticleId(articleId);
    }

    @Override
    @GetMapping("/articles")
    public List<ArticleCommentCountDto> loadArticleIdHavingNumerousComments(@QueryMap ConditionDto conditionDto) {
        return new CommentClientStub().loadArticleIdHavingNumerousComments(conditionDto);

    }

}

~~~


### Contract Test 수행

이 단계에서 contract test를 최초 수행합니다. 만약에 실패한다면 contract, contract stub, provider 중 잘못된 곳을 찾아서 수정해줘야 합니다.  
Contract test가 성공하게 되면 contract, contract stub에 대한 Pull Reqeust가 merge가 진행될 수 있도록 처리합니다.  

그러면 이제 소비자가 해당 API에 대한 개발을 진행할 수 있게 됩니다.  
그리고 제공자는 controller와 service에 대한 추가적인 개발을 진행하면 됩니다.  


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
배포가 완료되고 나면 아래의 다이어그램처럼 소비자 service가 HTTP call을 제공자에게 보낼 수 있게 됩니다. 이 때는 OpenFeign이 제공한 Contract interface의 구현체가 사용됩니다.  

![API_contract_06](/images/post_img/APIcontract/API_contract_06.png)  

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

## Implementation Of Contract

제공자는 CommentContract의 구현체를 만들면 됩니다. Contract와 동일한 public method를 갖게되기 때문에 파라미터 타입과, 응답 타입 등이 모두 소비자와 동일할 수 밖에 없습니다. 만들 때 ```@FeignClient``` annotation만 ```@RestController```로 변경해주고 다른 annotation은 거의 그대로 사용하면 됩니다. 사람의 실수로 annotation이 잘못 설정될 수는 있습니다. 하지만 거의 복사만 하면 되는 수준이기 때문에 오류를 생성할 가능성은 매우 낮습니다.  


~~~java
package com.dveamer.comment.web;

import com.dveamer.contract.comment.ArticleCommentCountDto;
import com.dveamer.contract.comment.CommentContract;
import com.dveamer.contract.comment.CommentDto;
import com.dveamer.contract.comment.ConditionDto;

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

제공자의 배포시에는 항상 contract test가 함께 진행되도록 CI/CD를 구성해야 합니다. 그리고 그 이후에 contract, contract stub도 REALEASE 버전으로 배포 될 수 있도록 해야 합니다.  

배포가 완료되고 나면 아래 다이어그램처럼 소비자가 직접 HTTP call을 제공자에게 보낼 수 있게 됩니다.  

![API_contract_06](/images/post_img/APIcontract/API_contract_06.png)  



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


