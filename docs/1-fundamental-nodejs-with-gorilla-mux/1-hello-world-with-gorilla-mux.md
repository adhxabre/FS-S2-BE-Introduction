---
sidebar_position: 1
slug : /
---

# 1. Hello World with Gorrila/Mux

import useBaseUrl from '@docusaurus/useBaseUrl';

**Gorrila/Mux** merupakan framework dari `Golang` yang didesain secara fleksibel dan sederhana untuk membantu tahap pengembangan aplikasi Back-End.

Kita akan mencoba implementasi Gorrila/Mux yang dapat menampilkan teks `Hello World`, berikut contoh codenya:

<a class="btn-example-code" href="/">
Contoh code
</a>

<br />
<br />

```go title=main.go
package main

import (
  "fmt"
  "net/http"
  "github.com/gorilla/mux"
)

func main() {

  fmt.Println("Hello World!")

  r := mux.NewRouter()

  r.HandleFunc("/", func (w http.ResponseWriter, r *http.Request){
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusOK)
    w.Write([]byte("Hello World"))
  }).Methods("GET")

  fmt.Println("server running localhost:5000")
  http.ListenAndServe("localhost:5000", r)
}
```

<img alt="image1-2" src={useBaseUrl('img/docs/image-4-1.png')} width="100%"/>

<br />
<br />

<div>
<a class="btn-demo" href="/">
Demo
</a>
</div>
