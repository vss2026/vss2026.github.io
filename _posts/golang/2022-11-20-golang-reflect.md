---
author: <1>
title: Golang Struct Copy
date: 2022-11-20
categories: [Golang, Reflect]
tags: [Golang]     # TAG names should always be lowercase
---

### Reflect를 이용 구조체 Field 값 Copy

``` go
import (
	"fmt"
	"reflect"
)

func CopyFields(target interface{}, data interface{}, fields ...string) error {
	reflectValue := reflect.ValueOf(data)
	targetValue := reflect.ValueOf(target)
	model := reflect.Indirect(reflectValue).Type()

	if reflectValue.Kind() != reflect.Ptr {
		return fmt.Errorf("err = %s", "2번쨰 인자는 포인터여야 합니다.")
	}
	if reflectValue.Kind() == reflect.Ptr {
		reflectValue = reflect.Indirect(reflectValue)
	}

	if targetValue.Kind() == reflect.Ptr {
		targetValue = reflect.Indirect(targetValue)
	}

	for i := 0; i < model.NumField(); i++ {
		if isContain(fields, model.Field(i).Name) {
			reflectValue.FieldByName(model.Field(i).Name).Set(targetValue.FieldByName(model.Field(i).Name))
		}
	}
	return nil
}

func CopyAll(target interface{}, data interface{}) error {
	reflectValue := reflect.ValueOf(data)
	targetValue := reflect.ValueOf(target)
	model := reflect.Indirect(reflectValue).Type()
	targetModel := reflect.Indirect(targetValue).Type()

	if reflectValue.Kind() != reflect.Ptr {
		return fmt.Errorf("err = %s", "2번쨰 인자는 포인터여야 합니다.")
	}

	var fields []string
	for i := 0; i < targetModel.NumField(); i++ {
		fields = append(fields, targetModel.Field(i).Name)
	}

	if reflectValue.Kind() == reflect.Ptr {
		reflectValue = reflect.Indirect(reflectValue)
	}

	if targetValue.Kind() == reflect.Ptr {
		targetValue = reflect.Indirect(targetValue)
	}

	for i := 0; i < model.NumField(); i++ {
		if isContain(fields, model.Field(i).Name) {
			reflectValue.FieldByName(model.Field(i).Name).Set(targetValue.FieldByName(model.Field(i).Name))
		}
	}
	return nil
}

func isContain(status []string, target string) bool {
	for _, s := range status {
		if s == target {
			return true
		}
	}
	return false
}
```
