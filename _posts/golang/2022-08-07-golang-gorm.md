---
author: <1>
title: Golang Gorm Tag 및 validate
date: 2022-08-07
categories: [Golang, Gorm]
tags: [Golang]     # TAG names should always be lowercase
---

### GORM TAG 종류

| 태그   |      설명      |        example      | CRUD시 적용 여부  |
|----------|:-------------:|:------:|:------:|
| column	 |  Column 이름 지정 | `gorm:"column:attachment_id"`  | O |
| serializer	 | [직렬화 및 역직렬화](https://gorm.io/docs/serializer.html) | `gorm:"serializer:json"` | O |
| type |    데이터 유형 지정   |  `gorm:"type:varchar(100)"` | X |
| size | 크기를 지정합니다. 기본값: 255 | `gorm:"size:255"` | X |
| primaryKey | 기본키로 지정 | `gorm:"primaryKey"` | X |
| unique | unique 지정| `gorm:"unique"` | X |
| default | default 값 지정 | `gorm:"default:false"` | O |
| precision | precision 지정 (Precision - 전체 자리수) | `gorm:"precision:5"` | X |
| scale | scale 지정 (Scale - 소수점 이하 자리수) |  `gorm:"scale:2"` | X |
| not null | NOT NULL로 지정 | `gorm:"not null"` | X |
| autoIncrement | autoIncrement 지정 | `gorm:"autoIncrement"` | X |
| index | [index 생성](https://gorm.io/docs/indexes.html)| `gorm:"index"` | X |
| uniqueIndex | [unique index 생성](https://gorm.io/docs/indexes.html) |  `gorm:"uniqueIndex"` | X |
| embedded | AutoMigrate, CreateTable시 embedded 정의 | `gorm:"Embedded"` | X |
| embeddedPrefix | AutoMigrate, CreateTable시 embedded prefix 정의 | `gorm:"Embedded;EmbeddedPrefix:author_"` | X |
| check | [check 제약 조건 생성](https://gorm.io/docs/constraints.html) | `gorm:"check:,name <> '진주'"` | X |
| <- | [필드의 쓰기 권한 설정](https://gorm.io/docs/models.html#Advanced) | `gorm:"<-:create"` (read, create 허용)| O |
| -> | [필드의 읽기 권한 설정](https://gorm.io/docs/models.html#Advanced) | `gorm:"->:false;<-:create"` (create 허용) | O |
| - | [필드 무시](https://gorm.io/docs/models.html#Advanced) | `gorm:"-"` | O |

---
### Association

| 태그   |      설명      |        example      |
|----------|:-------------:|:------:|
| foreignKey	 | [조인 테이블에 대한 외래 키로 사용되는 현재 모델의 열 이름을 지정](https://gorm.io/docs/belongs_to.html#Override-Foreign-Key) | `gorm:"foreignKey:CompanyName"` 
| references	 | [조인 테이블의 외래 키에 매핑되는 참조 테이블의 열 이름을 지정](https://gorm.io/docs/has_one.html#Override-References) | `gorm:"references:name"` 
| polymorphic	 | [모델 이름과 같은 다형성 유형을 지정](https://gorm.io/docs/has_many.html#Polymorphism-Association) | `gorm:"polymorphic:Owner;"`
| polymorphicValue	 | [다형성 값, 기본 테이블 이름 지정](https://gorm.io/docs/has_one.html#Override-References) | `gorm:"polymorphic:Owner;polymorphicValue:master"`
| many2many	 | [다대다 관계 정의](https://gorm.io/docs/many_to_many.html#Many-To-Many) | `gorm:"many2many:blog_tags;"`
| joinForeignKey	 | [다대다 관계 foreignKey 정의](https://gorm.io/docs/many_to_many.html#Override-Foreign-Key) | `joinForeignKey:UserReferID`
| joinReferences	 | [다대다 관계 references 정의](https://gorm.io/docs/many_to_many.html#Override-Foreign-Key) | `joinReferences:ProfileRefer`
| constraint	 | [관계 제약 조건](https://gorm.io/docs/has_many.html#FOREIGN-KEY-Constraints) | `gorm:"constraint:OnUpdate:CASCADE,OnDelete:SET NULL;"`

---

### GORM supports databases

> GORM은 공식적으로 MySQL, PostgreSQL, SQLite, SQL Server 데이터베이스를 지원합니다.
oracle을 이용 하고 싶다면 별도의 [driver](https://github.com/cengsin/oracle) 필요합니다. 

----

### 정리
 Q1. 현재 소스 코드에 남아있는 'type:'의 경우 사용해야 하는가?

  > 사용 하지 않아도 됩니다. #GORM-TAG-종류를 확인해보면 CRUD시 적용되지 않기에 현재는 사용 할 필요가 없습니다.
    type 태그는 AutoMigrate을 이용할 때 적용이 됩니다.

Q2. default:CURRENT_TIMESTAMP()를 회피하는 방법

  > `gorm:"default:(-)"` 다음과 같이 태그를 작성하게 되면 데이터베이스의 default value로 입력이 됩니다.  [참조](https://gorm.io/docs/create.html#Default-Values)

Q3. unique key, string의 자릿수 설정

  > gorm에서 지원되는 사양은 없는 듯 합니다.
  >> 대안 1. golang의 [validator](https://pkg.go.dev/github.com/go-playground/validator/v10#section-readme) 라이브러리와 [gorm hocks](https://gorm.io/docs/hooks.html)을 이용


  ``` go
    type Account struct {
      ID       string `validate:"required"`
      Password string `validate:"required"`
      Email    string `validate:"required,email"`
      Age      uint8  `validate:"gte=0,lte=100"`
    }

    // gorm hocks
    func (a *Account) BeforeCreate(_ *gorm.DB) (err error) {
      validate := validator.New()
      err = validate.Struct(a)
      return err
    }
  ````

  >> 대안 2. 라이브러리 이용 [validations](https://github.com/qor/validations) (gorm의 콜백을 사용하며 [govalidator](https://github.com/asaskevich/govalidator)도 지원합니다) [validations](https://github.com/qor/validations) 자체 github star 개수가 적기 때문에 고려가 필요하지만 비교적 간단한 소스코드로 작성 되어있어 분석 후 Fork하여 사용 가능


Q4. default Value 설정
 > gorm의 태그 default의 설정되는 값은 DB function 이다. [참고](https://gorm.io/docs/create.html#Default-Values)
ex) uuid_generate_v3(), CURRENT_TIMESTAMP()

----

## reference
[gorm docs](https://gorm.io/docs/)
[gorm test code](https://github.com/go-gorm/gorm/tree/master/tests)
[blog](https://www.cnblogs.com/zisefeizhu/p/12788017.html)
