---
author: <1>
title: Golang Lotto
date: 2022-08-10
categories: [Golang, Practice]
tags: [Golang]     # TAG names should always be lowercase
---

```
재미로 만든 로또번호 추출
```

``` go
import (
	"crypto/rand"
	"fmt"
	"math/big"
	"sort"
)

// lotto 번호 추출
func main() {
	for i := 0; i < 5; i++ {
		fmt.Println(makeRandomNumbers())
	}
}

func makeRandomNumbers() []int64 {
	var result []int64
	for len(result) < 6 {
		number, err := extractNumber()
		if err != nil {
			return nil
		}
		if number == 0 {
			continue
		}
		if !contains(result, number) {
			result = append(result, number)
		}
	}
	return sortSlice(result)
}

func extractNumber() (int64, error) {
	n, err := rand.Int(rand.Reader, big.NewInt(45))
	if err != nil {
		return 0, err
	}
	return n.Int64(), nil
}

func sortSlice(slice []int64) []int64 {
	sort.Slice(slice, func(i, j int) bool { return slice[i] < slice[j] })
	return slice
}

func contains(slice []int64, number int64) bool {
	for _, s := range slice {
		if s == number {
			return true
		}
	}
	return false
}
```
