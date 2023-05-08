---
author: <1>
title: Golang Tag Update
date: 2022-11-10
categories: [Golang, Reflect]
tags: [Golang]     # TAG names should always be lowercase
---

```
go는 태그를 재정의 할수 없는거 같다.
새로운 태그를 setting 하거나 기존 태그의 옵션을 append하는 함수 들이다.
새로운 구조체를 재정의하며 재정의된 구조체를 리턴한다.
structtag 라이브러리 이용
```

``` go
import (
	"fmt"
	"reflect"

	"github.com/fatih/structtag"
)

func SetStructTag(value interface{}, tag *structtag.Tag, fields ...string) (any, error) {
	reflectValue := reflect.ValueOf(value)
	model := reflect.Indirect(reflectValue).Type()

	structField := make([]reflect.StructField, 0)
	for i := 0; i < model.NumField(); i++ {
		tags, err := structtag.Parse(string(model.Field(i).Tag))
		if err != nil {
			return nil, err
		}

		if !isContain(fields, model.Field(i).Name) {
			structField = append(structField, model.Field(i))
			continue
		}

		if err = tags.Set(tag); err != nil {
			return nil, err
		}

		structField = append(structField, model.Field(i))
		structField[i].Tag = reflect.StructTag(tags.String())
		fmt.Println("setTag() ", structField[i].Name, "= ", structField[i].Tag)
	}

	newType := reflect.StructOf(structField)
	newValue := reflect.Indirect(reflectValue).Convert(newType)
	return newValue.Interface(), nil
}

func AppendStructTag(value interface{}, key string, options []string, fields ...string) (any, error) {
	reflectValue := reflect.ValueOf(value)
	model := reflect.Indirect(reflectValue).Type()

	structField := make([]reflect.StructField, 0)
	for i := 0; i < model.NumField(); i++ {
		tags, err := structtag.Parse(string(model.Field(i).Tag))
		if err != nil {
			return nil, err
		}

		if !isContain(fields, model.Field(i).Name) {
			structField = append(structField, model.Field(i))
			continue
		}

		tags.AddOptions(key, options...)

		structField = append(structField, model.Field(i))
		structField[i].Tag = reflect.StructTag(tags.String())
		fmt.Println("appendTag() ", structField[i].Name, "= ", structField[i].Tag)
	}

	newType := reflect.StructOf(structField)
	newValue := reflect.Indirect(reflectValue).Convert(newType)
	return newValue.Interface(), nil
}
```
