---
layout: default
title: DynamicSQL
parent: Spring
nav_order: 8
---

# Spring

## 동적 SQL

## 동적 SQL 개념

- 프로그램 실행 중에 SQL 쿼리를 생성하거나 변경할 수 있는 기법
- 프로그램의 상태나 사용자의 요청에 따라 쿼리를 유연하게 변경할 수 있다.
- 마이바티스는 <if>, <choose>, <foreach>와 같은 다양한 XML 태그를 제공한다.

### 동적 SQL 장점

- 유사한 작업을 수행하는 여러 SQL 쿼리를 하나의 동적 SQL 쿼리로 통합할 수 있다.
- 코드의 가독성과 유지 관리성을 높이고, 실수를 줄인다.
- 사용자의 입력에 따라 필요한 조건만 쿼리에 포함시키므로 불필요한 데이터를 조회하지 않고, 데이터베이스 서버의 부하를 줄일 수 있다.

```sql
<select id="selectUsers" parameterType="com.example.UserSearch" resultMap="UserResult">
    SELECT * FROM Users
    <if test="name != null">
        WHERE name = #{name}
    </if>
    <if test="email != null">
        AND email = #{email}
    </if>
    <if test="age != null">
        AND age = #{age}
    </if>
</select>
```

- 위 코드는 사용자의 이름, 이메일, 나이에 따라 검색 쿼리가 동적으로 변경된다. 사용자가 입력한 조건만 쿼리에 포함되므로 불필요한 데이터 조회를 줄이고 성능을 최적화 할 수 있다.

## 동적 SQL 기본 구조

### 동적 SQL 작성 규칙

- SQL 명령어와 MyBatis 태그 사이에는 공백을 포함해야 한다.
- MyBatis 태그는 항상 닫아야 한다.
- 동적 SQL은 복잡성을 증가시킬 수 있으므로 쿼리를 간결하게 유지한다.

### 동적 SQL 활용 예제

### if 태그

- 특정 조건이 참일 때만 SQL 구문을 포함시키는 역할
- if 태그 안에 작성된 SQL 구문은 해당 태그의 조건이 만족할 때만 실행된다.

### if 태그 활용 예시

```sql
<select id="findUser" parameterType="map" resultType="User">
    SELECT * FROM User
    WHERE 1 = 1
    <if test="name != null">
        AND name = #{name}
    </if>
    <if test="email != null">
        AND email = #{email}
    </if>
</select>
```

- 사용자의 이름과 이메일 주소를 기반으로 사용자를 검색하는 SQL 쿼리
- 사용자는 이름, 이메일 주소, 또는 둘 다를 제공할 수 있다.
- test 속성은 조건을 정의하며, 해당 조건이 참일 경우에만 안에 있는 SQL 구문이 실행된다.
- 사용자가 이름만 제공한 경우, 첫 번째 if 태그의 조건이 참이 되어 'AND name = #{name}' 구문이 포함된다. 반면, 이메일 주소는 제공되지 않았으므로 두 번째 if 태그의 조건은 거짓이 되어 AND email = #{email} 구문은 포함되지 않는다.

### choose 태그

- 여러 개의 선택지 중에서 하나를 선택하는 역할
- 자바의 switch 문이나 if-else if-else 구조와 비슷하다.
- when, otherwise 태그와 함께 사용된다.
- when: 조건을 검사하고, 그 조건이 참인 경우에 해당하는 SQL 코드를 실행한다.
- otherwise: when 태그의 조건이 모두 거짓이라면 otherwise 태그의 SQL 코드가 실행된다.

### choose 태그 활용 예시

```sql
<select id="selectUser" parameterType="map" resultType="User">
    SELECT * FROM User
    <where>
        <choose>
            <when test="name != null">
                name = #{name}
            </when>
            <when test="email != null">
                email = #{email}
            </when>
        </choose>
    </where>
</select>
```

- 해당 예시는 사용자 이름과 이메일 중 하나를 선택하여 검색하는 경우를 나타낸다.
- when 태그의 test 속성에는 조건식을 작성하며, 이 조건이 참일 경우 해당 when 태그 안의 SQL 코드가 실행된다.
- 하지만 실제 상황에서는 조건이 더 복잡할 수 있기 때문에 아래와 같이 otherwise 태그를 활용하여 코드를 작성할 수 있다.

```sql
<select id="findUser" parameterType="UserSearch" resultType="User">
    SELECT * FROM User
    <where>
        <choose>
            <when test="name != null and email == null and phone == null">
                name = #{name}
            </when>
            <when test="eamil != null and name == null and phone == null">
                email = #{email}
            </when>
            <when test="phone != null and name == null and email == null">
                phone = #{phone}
            </when>
            <otherwise>
                id = #{id}
            </otherwise>
        </choose>
    </where>
</select>
```

- 해당 예시는 이름, 이메일, 전화번호 중 하나만 선택하여 사용자를 검색하며, 그 어떤 조건도 충족되지 않을 때는 id로 검색을 수행한다.

### foreach 태그

- SQL 구문에 반복적인 요소를 적용할 때 사용된다.
- 여러 개의 항목에 대한 조회 쿼리를 작성하거나, 여러 개의 항목을 한 번에 삽입하거나 업데이트하려는 경우에 사용할 수 있다.
- 여러 값을 SQL 구문의 IN 절에 넣을 때 유용하게 사용된다.

### foreach 태그 활용 예시

```sql
<select id="selectUserIn" resultType="User">
    SELECT * FROM User
    WHERE id IN
    <foreach item="userId" index="index" collection="userIds" open="("close=")" separator=",">
        #{userId}
    </foreach>
</select>
```

- 해당 예시는 'userIds'라는 이름의 컬렉션을 통해 여러 'userId'를 IN 절에 넣는다. 'userIds' 컬렉션은 자바 코드에서 다음과 같이 매개변수로 넘겨줄 수 있다.

```java
List<Integer> userIds = Arrays.asList(1, 2, 3);
Map<String, Object> params = new HashMap<>();
params.put("userIds", userIds);
List<User> users = sqlSession.selectList("selectUserIn", params);
```

- 다수의 레코드를 삽입하는 경우: 'foreach' 태그는 여러 레코드를 한 번에 삽입하는 데에도 사용될 수 있다.

```sql
<insert id="insertUsers">
    INSERT INTO user (id, name, email)
    VALUES
    <foreach item="user" index="index" collection="users" open="" separator="," close="">
        (#{user.id}, #{user.name}, #{user.email})
    </foreach>
</insert>
```

- 해당 예시는 'users'라는 이름의 컬렉션을 사용하여 여러 사용자 정보를 한 번에 삽입한다.
- 각 'user' 항목은 'id', 'name', 'email' 필드를 가진다.

### trim 태그

- SQL 문의 앞뒤에 특정 문자열을 추가하거나 제거할 수 있다.
- 특정 조건에 따라 쿼리의 일부가 생략될 경우, 그로 인해 발생하는 구문 오류를 방지하는 데 도움이 된다.
- prefix, prefixOverrieds, suffix, suffixOverrides 네 가지 속성을 가지고 있다.

### trim 태그 활용 예시

```sql
<update id="updateUser">
    UPDATE user
    <trim prefix="SET" suffixOverrieds=",">
        <if test="name != null">name = #{name},</if>
        <if test="email != null">email = #{email},</if>
    </trim>
    WHERE id = #{id}
</update>
```

- 해당 예시는 trim 태그를 이용해 SET 절을 동적으로 생성한다.
- prefix 속성에 SET을 설정함으로써, trim 태그 내부의 내용이 존재할 경우 SET이라는 접두사를 추가한다.
- suffixOverrieds=","는 마지막에 붙는 불필요한 쉼표를 제거한다.

### where 태그

- SQL의 WHERE 절을 동적으로 생성하는 역할
- where 태그 내부에 있는 조건들 중 true인 조건들만 WHERE 절에 포함된다.
- where 태그는 자동으로 첫 번째 Boolean 연산자 (e.g. AND, OR)를 제거하여 SQL 구문 오류를 방지한다.

### where 태그 활용 예시

```sql
<select id="selectUser" resultType="User">
    SELECT * FROM user
    <where>
        <if test="name != null">AND name = #{name}</if>
        <if test="eamil != null">AND email = #{email}</if>
    </where>
</select>
```

- 해당 예시는 where 태그를 사용하여 이름과 이메일이 null이 아닌 경우에만 WHERE 절에 조건을 추가하고 있다.
- 첫 번째 조건 앞의 'AND'는 where 태그가 자동으로 제거한다.

### set 태그

- UPDATE 쿼리에서 SET 절을 동적으로 작성하는 데 사용된다.
- set 태그를 사용하면 조건에 따라 여러개의 컬럼을 동적으로 업데이트할 수 있다.
- set 태그는 내부에 있는 조건들 중 ture인 조건들만 SET 절에 포함된다.

### set 태그 활용 예시

```sql
<update id="updateUser">
    UPDATE user
    <set>
        <if test="name != null">name = #{name},</if>
        <if test="email != null">email = #{email},</if>
    </set>
    WHERE id = #{id}
</update>
```

- 해당 예시는 set 태그를 사용하여 name과 email 필드가 null이 아닌 경우에만 해당 필드를 업데이트 한다.
- 불필요한 업데이트를 방지하고 SQL 구문을 최적화 할 수 있다.
