---
author: <1>
title: Golang Gorm DB Resolver
date: 2022-08-07
categories: [Golang, Gorm]
tags: [Golang]     # TAG names should always be lowercase
---

## DBResolver

- 기능

1.  읽기/쓰기 분할
2.  수동 연결 전환
3.  작업 테이블/구조체에 따른 자동 연결 전환
4.  소스/복제본 로드 밸런싱
5.  RAW SQL에서 작동
6.  여러 소스, 복제본

### 읽기/쓰기분할 && 수동연결전환

```go
var adminDB = "${user}:${password}@tcp(localhost:3306)/${scheme}?charset=utf8&parseTime=true"
var uerDB = "${user}:${password}@tcp(localhost:3306)/${scheme}?charset=utf8&parseTime=true"
var packageDB = "${user}:${password}@tcp(localhost:3306)/${scheme}?charset=utf8&parseTime=true"

  /**
    Sources - 쓰기조작
    Replicas - 읽기조작
  */
  	err := db.Use(dbresolver.Register(
              dbresolver.Config{
                Sources: []gorm.Dialector{mysql.Open(packageDB)},
                Replicas: []gorm.Dialector{mysql.Open(boxDB), mysql.Open(erpDB)},
              }, "admin").SetMaxOpenConns(5))


  // 위에 지정된 admin으로 worker에 대한 update 진행
  // Sources에 지정된 DB에 update가 실행
  err = db.Clauses(dbresolver.Use("admin")).Updates(&worker).Error

  // 위에 지정된 admin으로 worker에 대한 조회
  // Replicas에 지정된 DB에서 조회, 현재 2개 DB가 지정되어있는데 랜덤?으로 각 DB에서 데이터를 조회 순서에 대한 알고리즘은 현재 알지못함
  err = db.Clauses(dbresolver.Use("admin")).Find(&workers).Error
```

### 작업 테이블/구조체에 따른 자동 연결 전환

```go
  /**
   Config에 구조체 || 테이블을 지정하여 설정가능
   */
   	err := db.Use(dbresolver.Register(
             dbresolver.Config{
               Sources: []gorm.Dialector{mysql.Open(packageDB)},
               Replicas: []gorm.Dialector{mysql.Open(boxDB), mysql.Open(erpDB)},
             }, "admin", Worker{}).SetMaxOpenConns(5))
```

### 소스/복제본 로드 밸런싱

```go
   /**
   Policy - Sources/Replicas 로드 밸런싱 정책
   */
   	err := db.Use(dbresolver.Register(
             dbresolver.Config{
               Sources: []gorm.Dialector{mysql.Open(packageDB)},
               Replicas: []gorm.Dialector{mysql.Open(boxDB), mysql.Open(erpDB)},
               Policy: dbresolver.RandomPolicy{},
             }, "admin", Worker{}).SetMaxOpenConns(5))

 /**
   로드 밸런싱
   GORM은 정책을 기반으로 하는 로드 밸런싱 소스/복제본을 지원하며, 정책은 다음 인터페이스를 구현하는 구조체여야 합니다.
   (현재는 RandomPolicy구현만 되어 있으며 다른 정책이 지정되지 않은 경우 기본 옵션입니다)
 */
 type Policy interface {
   Resolve([]gorm.ConnPool) gorm.ConnPool
 }
```

- 참고자료
  GORM Docs - https://gorm.io/docs/dbresolver.html
  codetd (간단예제) - https://www.codetd.com/en/article/13717782
  dbResolver TestCode - https://github.com/go-gorm/dbresolver/blob/master/dbresolver_test.go
