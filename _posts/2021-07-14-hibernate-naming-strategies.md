---
title: "하이버네이트 명명 전략(Hibernate Naming Strategies)"
excerpt: "하이버네이트에서 제공하는 명명 전략을 정리한 내용입니다."
toc: true
toc_sticky: true
categories:
- JPA
tags:
- JAVA
- ORM
- JPA
last_modified_at:
---
# 하이버네이트 명명 전략(hibernate Naming Strategies)
- JPA와 하이버네이트는 엔티티 클래스를 동일한 이름을 가진 데이터베이스의 테이블로 매핑하는 기본 매핑을 제공한다. 또한 엔티티의 필드는 동일한 이름의 데이터베이스 컬럼에 매핑된다.
- 이러한 기본 매핑을 바꾸려면 어떻게 해야할까? 테이블 이름은 @Table 어노테이션, 컬럼 이름은 @Column 어노테이션을 이용하여 바꿀 수 있다. 이를 명시적 명명법 이라고 한다.
- 하지만 수많은 엔티티의 테이블과 컬럼의 매핑정보를 일일히 어노테이션을 이용하여 변경해야 한다면, 하이버네이트의 명명 전략을 이용하면 효과적으로 변경할 수 있다.

## 1. 2단계 프로세스
- 하이버네이트는 엔티티를 데이터베이스의 테이블, 컬럼으로 매핑할 때 2단계로 진행한다.
- 첫 번째는 논리적 이름을 결정하는 것이다.
  - 논리적 이름은 @Table, @Column 어노테이션을 통해 명시적으로 설정할 수 있다.
  - 명시적으로 설정하지 않으면, ImplicitNamingStrategy 인터페이스 구현체에 의해 암시적으로 설정된다.
- 두 번째 단계는 논리적 이름을 물리적 이름으로 매핑한다.
  - 기본적으로 하이버네이트에서 물리적 이름은 논리적 이름을 그대로 사용한다.
  - PhysicalNamingStrategy 인터페이스를 구현하여 논리적 이름을 내부 명명 규칙을 따르는 물리적 이름에 매핑할 수 있다.

- 2단계로 매핑하는 것은 좀 더 유연성을 제공한다. 예를 들어 모든 테이블에 “_TBL” 이라는 접미사를 붙여야 한다면 PhysicalNamingStrategy 를 이용하여 구현하면 된다.
  - @Table 어노테이션을 이용해 일일히 테이블 이름을 지정하거나 엔티티 이름을 직접 수정할 필요가 없다.
  - 하이버네이트가 물리적 이름 전략을 이용해 모든 테이블 이름에 “_TBL”을 추가해준다.

## 2. 논리적 명명 전략(Logical naming strategy)

- 논리적 이름은 암시적 또는 명시적으로 정의할 수 있다.
- 명시적 명명 전략(Explicit naming strategy)
  - @Table 또는 @Column 어노테이션을 통해 명시적으로 테이블 이름 또는 컬럼 이름을 지정해주는 방법이다.
- 암시적 명명 전략(Implicit naming strategy)
  - @Table 또는 @Column 어노테이션을 사용하지 않았을 때, 하이버네이트는 암시적 명명 전략을 사용한다.
  - ImplicitNamingStrategy 인터페이스 구현체에 의해 논리적 이름이 결정된다.
  - 하이버네이트에서 기본적으로 4개의 ImplicitNamingStrategy 인터페이스 구현체를 제공한다. 직접 구현해서 사용해도 된다.
    - default
      - org.hibernate.boot.model.naming.ImplicitNamingStrategyJpaCompliantImpl
      - jpa 가 default와 같다.
    - jpa
      - org.hibernate.boot.model.naming.ImplicitNamingStrategyJpaCompliantImpl
      - JPA 2.0 compliant naming strategy
    - legacy-hbm
      - org.hibernate.boot.model.naming.ImplicitNamingStrategyLegacyHbmImpl
      - compliant with the original Hibernate NamingStrategy
    - legacy-jpa
      - org.hibernate.boot.model.naming.ImplicitNamingStrategyLegacyJpaImpl
      - compliant with the legacy NamingStrategy developed for JPA 1.0
    - component-path
      - org.hibernate.boot.model.naming.ImplicitNamingStrategyComponentPathImpl
- 각 구현체별로 차이는 join column 과 join table 의 명명법이 조금씩 다른 것 같다.
- hibernate.implicit_naming_strategy 프로퍼티를 통해 암시적 명명 전략 구현 클래스를 설정할 수 있다.

```java
@Entity
@Table
public class Member {
    @Id @GeneratedValue
    private Long id;
    @Column(name = "name")
    private String username;
    private Integer age;
    private String emailAddress;
    @ManyToOne
    @JoinColumn
    private Team team;
}

@Entity
public class Team {
    @Id @GeneratedValue
    private Long id;
    private String name;
}
```
```xml
<!-- persistence.xml -->
<property name="hibernate.implicit_naming_strategy" value="org.hibernate.boot.model.naming.ImplicitNamingStrategyJpaCompliantImpl"/>
<property name="hibernate.implicit_naming_strategy" value="org.hibernate.boot.model.naming.ImplicitNamingStrategyLegacyHbmImpl"/>
```

```sql
-- ImplicitNamingStrategyJpaCompliantImpl 전략 사용시
create table Member (
    id bigint not null,
    age integer,
    emailAddress varchar(255),
    name varchar(255),
    team_id bigint,
    primary key (id)
)

create table Team (
    id bigint not null,
    name varchar(255),
    primary key (id)
)

-- ImplicitNamingStrategyLegacyHbmImpl 전략 사용시
create table Member (
    id bigint not null,
    age integer,
    emailAddress varchar(255),
    name varchar(255),
    team bigint,
    primary key (id)
)

create table Team (
    id bigint not null,
    name varchar(255),
    primary key (id)
)
```

## 3. 물리적 명명 전략(Physical naming strategy)
- 물리적 이름을 구현하는 것은 어렵지 않다. PhysicalNamingStrategy 인터페이스를 구현하거나 PhysicalNamingStrategyStandardImpl 클래스를 상속받으면 된다.
- hibernate.physical_naming_strategy 프로퍼티를 통해 물리적 명명 전략 구현 클래스를 설정할 수 있다.

### 3.1 테이블 이름에 접미사 적용
```java
public class TablePostfixPhysicalNamingStrategy extends PhysicalNamingStrategyStandardImpl {

    private final static String POSTFIX = "_TBL";

    @Override
    public Identifier toPhysicalTableName(Identifier name, JdbcEnvironment context) {
        if (name == null) {
            return null;
        }
        final String newName = name.getText() + POSTFIX;
        return Identifier.toIdentifier(newName);
    }
}
```
```xml
<property name="hibernate.physical_naming_strategy" value="jpabook.start.TablePostfixPhysicalNamingStrategy"/>
```
```sql
create table Member_TBL (
    id bigint not null,
    age integer,
    name varchar(255),
    team bigint,
    primary key (id)
)
    
create table Team_TBL (
    id bigint not null,
    name varchar(255),
    primary key (id)
)
```
### 3.2 카멜 케이스를 스네이크 케이스로 변경
```java
public class SnakeCasePhysicalNamingStrategy implements PhysicalNamingStrategy {
    @Override
    public Identifier toPhysicalCatalogName(Identifier name, JdbcEnvironment jdbcEnvironment) {
        return convertToSnakeCase(name);
    }

    @Override
    public Identifier toPhysicalSchemaName(Identifier name, JdbcEnvironment jdbcEnvironment) {
        return convertToSnakeCase(name);
    }

    @Override
    public Identifier toPhysicalTableName(Identifier name, JdbcEnvironment jdbcEnvironment) {
        return convertToSnakeCase(name);
    }

    @Override
    public Identifier toPhysicalSequenceName(Identifier name, JdbcEnvironment jdbcEnvironment) {
        return convertToSnakeCase(name);
    }

    @Override
    public Identifier toPhysicalColumnName(Identifier name, JdbcEnvironment jdbcEnvironment) {
        return convertToSnakeCase(name);
    }

    private Identifier convertToSnakeCase(Identifier identifier) {
        if (identifier == null) {
            return null;
        }
        String name = identifier.getText();
        String newName = name.replaceAll("([a-z])([A-Z])", "$1_$2").toLowerCase();
        return Identifier.toIdentifier(newName);
    }
}
```
```xml
<property name="hibernate.physical_naming_strategy" value="jpabook.start.SnakeCasePhysicalNamingStrategy"/>
```
```sql
create table member (
    id bigint not null,
    age integer,
    email_address varchar(255),
    name varchar(255),
    team bigint,
    primary key (id)
)

create table team (
    id bigint not null,
    name varchar(255),
    primary key (id)
)
```

## 4. 결론
- 하이버네이트 명명 전략은 논리적 이름과 물리적 이름으로 구성되어 있다.
- @Table, @Column 어노테이션을 이용하여 논리적 이름을 명시적으로 설정한다. 설정하지 않으면 하이버네이트는 암시적 명명 전략을 이용하여 논리적 이름을 설정한다.
- 논리적 이름이 결정되면 하이버네이트는 물리적 명명 전략을 이용해 물리적 이름을 결정한다. 물리적 이름은 기본적으로 논리적 이름을 그대로 사용한다.
- 결과적으로 물리적 이름이 데이터베이스에 매핑된다.

## 5. 참고
- [https://docs.jboss.org/hibernate/orm/5.5/userguide/html_single/Hibernate_User_Guide.html#naming](https://docs.jboss.org/hibernate/orm/5.5/userguide/html_single/Hibernate_User_Guide.html#naming)
- [https://thorben-janssen.com/naming-strategies-in-hibernate-5/](https://thorben-janssen.com/naming-strategies-in-hibernate-5/)
- [https://www.baeldung.com/hibernate-naming-strategy](https://www.baeldung.com/hibernate-naming-strategy)
- [https://velog.io/@mumuni/Hibernate5-Naming-Strategy-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC](https://velog.io/@mumuni/Hibernate5-Naming-Strategy-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC)