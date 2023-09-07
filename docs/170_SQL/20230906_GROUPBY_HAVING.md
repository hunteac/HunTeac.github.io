---
layout: default
title: GROUP BY, HAVING
parent: SQL
nav_order: 1
---

# SQL

## GROUP BY, HAVING 절

### 집계 함수(Aggregate Function)
- 여러 행들의 그룹이 모여서 그룹당 단 하나의 결과를 돌려주는 함수이다.
- GROUP BY 절은 행들을 소그룹화 한다.
- SELECT 절, HAVING 절, ORDER BY 절에 사용할 수 있다.

### 집계함수 특징
- SQL문 - 집계함수명([DISTINCT \| ALL] 컬럼 \| 표현식)
- 설명 - DISTINCT : 같은 값을 하나의 데이터로 간주하여 하나만 조회
- ALL : Default 옵션이므로 생략 가능

### 집계함수 종류
- COUNT(*) – NULL 값을 포함한 행의 수를 출력
- COUNT(컬럼 \| 표현식) – 컬럼이나 표현식의 값이 NULL 값인 것을 제외한 행의 수를 출력
- SUM([DISTINCT \| ALL] 컬럼 \| 표현식) – 컬럼이나 표현식의 NULL 값을 제외한 합계 출력
- AVG([DISTINCT \| ALL] 컬럼 \| 표현식) – 컬럼이나 표현식의 NULL 값을 제외한 평균 출력
- MAX([DISTINCT \| ALL] 컬럼 \| 표현식) – 컬럼이나 표현식의 최대값 출력 / 문자, 날짜 데이터 타입도 가능
- MIN([DISTINCT \| ALL] 컬럼 \| 표현식) – 컬럼이나 표현식의 최소값 출력 / 문자, 날짜 데이터 타입도 가능
- STDDEV([DISTINCT \| ALL] 컬럼 \| 표현식) – 컬럼이나 표현식의 표준편차 출력
- VARIAN([DISTINCT \| ALL] 컬럼 \| 표현식) – 컬럼이나 표현식의 분산 출력
기타 통계 함수 – 벤더별로 다양한 통계식 제공 

### GROUP BY 절
- GROUP BY 절은 SQL 문에서 FROM 절과 WHERE 절 뒤에 위치한다.
- 데이터들을 작은 그룹으로 분류하여 소그룹에 대한 항목별로 통계 정보를 얻을 때 추가로 사용된다. 

### HAVING 절
- HAVING 절은 GROUP BY 절의 조건절이다.
- HAVING 절은 GROUP BY 절 뒤에 위치한다.
- WHERE 절은 집계함수를 사용할 수 없지만 HAVING 절은 사용할 수 있다.

### GROUP BY 절과 HAVING 절의 특성
- GROUP BY 절을 통해 소그룹별 기준을 정한 후, SELECT 절에 집계 함수를 사용한다.
- 집계 함수의 통계 정보는 NULL 값을 가진 행을 제외하고 수행한다.
- GROUP BY 절에서는 SELECT 절과는 달리 ALIAS 명을 사용할 수 없다.
- 집계 함수는 WHERE 절에는 올 수 없다. (집계 함수를 사용할 수 있는 GROUP BY 절보다 WHERE 절이 먼저 수행된다)
- WHERE 절은 전체 데이터를 GROUP으로 나누기 전에 행들을 미리 제거시킨다.
- HAVING 절은 GROUP BY 절의 기준 항목이나 소그룹의 집계 함수를 이용한 조건을 표시할 수 있다.
- GROUP BY 절에 의한 소그룹별로 만들어진 집계 데이터 중, HAVING 절에서 제한 조건을 두어 조건을 만족하는 내용만 출력한다.
- HAVING 절은 일반적으로 GROUP BY 절 뒤에 위치한다.

### 집계 함수와 NULL
- 테이블의 빈칸을 NULL이 아닌 0으로 표현하기 위해 NVL(Oracle)/ISNULL(SQL Server) 함수를 사용하는 경우가 많은데, 다중 행 함수를 사용하는 경우는 오히려 불필요한 부하가 발생하므로 굳이 NVL 함수를 다중 행 함수 안에 사용할 필요가 없다.
- 다중 행 함수는 입력 값으로 전체 건수가 NULL 값인 경우만 함수의 결과가 NULL이 나오고 전체 건수 중에서 일부만 NULL인 경우는 NULL인 행을 다중 행 함수의 대상에서 제외한다.