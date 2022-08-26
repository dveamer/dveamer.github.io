---
layout: post
title: "코드 의존성 Visualization Tools"
date: 2022-12-18 00:00:00
lastmod: 2022-12-18 00:00:00
categories: BackEnd
tags: BackEnd Visualization 
hidden: true
published: true
---

코드간 의존성이 얼마나 있는지 파악하기 위해 사용할 수 있는 visualization tools 를 기록합니다.  

<!--more-->

이전에 다니던 회사에서는 코드간 의존성 파악 등을 해주는 팀이 있고 자체적으로 만든 솔루션 툴이 있어서 비용을 내고 요청을 하면 분석을 해줬었는데 직접 해볼 생각을 하니 툴이 필요하다고 느껴져셔 검색을 해봤습니다.  

아직 사용해본 것은 아니고 추후를 위해 기록만 해둡니다.  


## 목적

1차적으로 코드가 체계가 있다면 체계를 얼마나 잘 지키고 있는지를 확인할 수 있습니다.  

만약 잘 지켜지고 있다면 파악한 코드간 의존성을 가지고 시스템을 분해할 수 있을지 혹은 어떻게 분해할지를 고민해볼 수 있습니다.  

예를들어, 컴포넌트 별로 구분지을 수 있는 코드 체계라면 컴포넌트 별로 의존성을 파악해서 의존성이 낮은 컴포넌트는 쉽게 분해할 수 있는 후보대상으로 생각할 수 있을 겁니다.  

## Tools

  * [IntelliJ Scopes Feature](https://www.jetbrains.com/help/idea/dependencies-analysis.html) : visualization 기능까지는 없는 것 같지만 [validate dependencies](https://www.jetbrains.com/help/idea/dependencies-analysis.html#validate-dependencies) 을 활용 가능
    * 공식 문서보다 [synyx.de 블로그](https://synyx.de/blog/validating-internal-structure-dependencies-using-intellij-idea/) 의 설명이 이해하기 좋음 

  * [JDepend](https://github.com/clarkware/jdepend)
    * Eclipse IDE plugin 존재 / Intellij IDE plugin은 확인 안됨
    * 테스트 코드에도 활용 가능
    * 배포 파이프라인에서도 사용 가능할 것으로 보임

  * [Degraph](https://github.com/riy/degraph)  
    * 테스트 코드에도 활용 가능
    * 배포 파이프라인에서도 사용 가능할 것으로 보임

  * [Sonargraph](https://www.hello2morrow.com/products/sonargraph/architect9)
