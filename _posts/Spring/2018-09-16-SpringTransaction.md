---
layout: post
title:  "Spring @Transactional DB 트렌젝션 처리"
date:   2038-09-16 00:00:00
categories: BackEnd
tags: Spring Transaction
---

![https://spring.io/](https://spring.io/img/spring-by-pivotal.png){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

이 글은 Spring을 사용하면 DB transaction 관리가 얼마나 편해지는지를 설명합니다.  

<!--more-->

SpringBoot 를 사용했으며 java based configuration 방식을 이용했습니다.  

최종 결과물에 대한 [샘플](https://github.com/dveamer/SpringBootSample/tree/master/Transaction)을 Github에 올려뒀으니 참고하시기 바랍니다.  


# JTA(Java Transaction API)

Spring과의 비교를 위해 먼저 순수 JTA를 이용한 transaction 처리를 살펴봅니다.  

실패시에는 수행한 모든 쿼리를 rollback하고 성공시에는 모든 쿼리를 commit하는 가장 단순한 예제입니다.  

~~~java


public void updateCoffeeSales(HashMap<String, Integer> salesForWeek)
    throws SQLException {

    PreparedStatement updateSales = null;
    PreparedStatement updateTotal = null;

    String updateString =
        "update " + dbName + ".COFFEES " +
        "set SALES = ? where COF_NAME = ?";

    String updateStatement =
        "update " + dbName + ".COFFEES " +
        "set TOTAL = TOTAL + ? " +
        "where COF_NAME = ?";

    try {
        con.setAutoCommit(false);
        updateSales = con.prepareStatement(updateString);
        updateTotal = con.prepareStatement(updateStatement);

        for (Map.Entry<String, Integer> e : salesForWeek.entrySet()) {
            updateSales.setInt(1, e.getValue().intValue());
            updateSales.setString(2, e.getKey());
            updateSales.executeUpdate();
            updateTotal.setInt(1, e.getValue().intValue());
            updateTotal.setString(2, e.getKey());
            updateTotal.executeUpdate();
            con.commit();
        }
    } catch (SQLException e ) {
        JDBCTutorialUtilities.printSQLException(e);
        if (con != null) {
            try {
                System.err.print("Transaction is being rolled back");
                con.rollback();
            } catch(SQLException excep) {
                JDBCTutorialUtilities.printSQLException(excep);
            }
        }
    } finally {
        if (updateSales != null) {
            updateSales.close();
        }
        if (updateTotal != null) {
            updateTotal.close();
        }
        con.setAutoCommit(true);
    }
}

~~~


## DTO(Data Transfer Object) & DAO(Data Access Object)

#### References 

https://docs.oracle.com/javase/tutorial/jdbc/basics/transactions.html
http://blog.naver.com/PostView.nhn?blogId=0131v&logNo=110105753252&parentCategoryNo=35&viewDate=&currentPage=1&listtype=0

https://www.javatpoint.com/example-of-PreparedStatement-in-Spring-JdbcTemplate
https://jungwoon.github.io/common%20sense/2017/11/16/DAO-VO-DTO/

# Spring @Transactional

## 사용법


## 제약사항
