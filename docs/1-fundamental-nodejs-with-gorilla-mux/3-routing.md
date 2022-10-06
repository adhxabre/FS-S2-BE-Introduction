---
sidebar_position: 3
---

# 3. Routing

import useBaseUrl from '@docusaurus/useBaseUrl';

**Routing** adalah cara bagaimana aplikasi berkomunikasi / meresponse sebuah permintaan dari `client` ke titik akhir dari perminataan aplikasi tersebut, yang dimana permintaan tersebut seperti `URI (Path)` ataupun `HTTP Request`. Setiap Routing dapat memiliki satu atau lebih fungsi yang dapat di jalankan.

Struktur Routing :

```go {3}
r := mux.NewRouter()

r.HandleFunc(PATH, HANDLER).Methods(METHOD)
```

Ket :

- r = Route dari gorilla/mux untuk membuat `routing` baru
- HandleFunc = untuk melakukan registrasi rute/endpoint dan handler-nya
- PATH = `URL` alamat yang terdapat di server
- HANDLER = Fungsi yang dijalankan ketika routing yang di eksekusi cocok
- METHOD = Permintaan dari `HTTP Request`

### 3.1 Membuat API

**API** atau `Application Programming Interface` adalah antarmuka yang dapat menghubungkan satu aplikasi dengan aplikasi lain. Dengan demikian, API bertindak sebagai perantara antara aplikasi yang berbeda, baik dalam aplikasi yang sama platform atau lintas platform.

<a class="btn-example-code" href="/">
Contoh code
</a>

<br />
<br />

Berikut adalah contoh code dari beberapa response API :

```go {1,3-8,10-14,16-27,29-40} title=main.go
package main

import (
  "fmt"
  "net/http"
  "github.com/gorilla/mux"
  "encoding/json"
)

type Todos struct {
  Id string `json:"id"`
  Title string `json:"title"`
  IsDone bool `isDone:"isDone"`
}

var todos = []Todos{
  {
    Id: "1",
    Title: "Cuci tangan",
    IsDone: true,
  },
  {
    Id: "2",
    Title: "Jaga jarak",
    IsDone: false,
  },
}

func main() {
  r := mux.NewRouter()

  r.HandleFunc("/todos", FindTodos).Methods("GET")
  r.HandleFunc("/todo/{id}", GetTodo).Methods("GET")
  r.HandleFunc("/todo", CreateTodo).Methods("POST")
  r.HandleFunc("/todo/{id}", UpdateTodo).Methods("PATCH")
  r.HandleFunc("/todo/{id}", DeleteTodo).Methods("DELETE")

  fmt.Println("server running localhost:5000")
  http.ListenAndServe("localhost:5000", r)
}
```

- Function `FindTodos` melakukan pengambilan data secara keseluruhan

  ```go title=main.go
  // previous code...

  func FindTodos(w http.ResponseWriter, r *http.Request){
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode(todos)
  }
  ```

- Function `GetTodo` melakukan pengambilan data berdasarkan ID

  ```go title=main.go
  // previous code...

  func GetTodo(w http.ResponseWriter, r *http.Request){
    w.Header().Set("Content-Type", "application/json")
    params := mux.Vars(r)
    id := params["id"]

    var todoData Todos
    var isGetTodo = false

    for _, todo := range todos {
      if id == todo.Id {
        isGetTodo = true
        todoData = todo
      }
    }

    if isGetTodo == false {
      w.WriteHeader(http.StatusNotFound)
      json.NewEncoder(w).Encode("ID: " + id + " not found")
      return
    }

    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode(todoData)
  }
  ```

- Function `CreateTodo` melakukan penambahan data

  ```go title=main.go
  // previous code...

  func CreateTodo(w http.ResponseWriter, r *http.Request){
    var data Todos

    json.NewDecoder(r.Body).Decode(&data)

    todos = append(todos, data)

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode(todos)
  }
  ```

- Function `UpdateTodo` melakukan perubahan data berdasarkan ID

  ```go title=main.go
  // previous code...

  func UpdateTodo(w http.ResponseWriter, r *http.Request){
    params := mux.Vars(r)
    id := params["id"]
    var data Todos
    var isGetTodo = false

    json.NewDecoder(r.Body).Decode(&data)

    for idx, todo := range todos {
      if id == todo.Id {
        isGetTodo = true
        todos[idx] = data
      }
    }

    if isGetTodo == false {
      w.WriteHeader(http.StatusNotFound)
      json.NewEncoder(w).Encode("ID: " + id + " not found")
      return
    }

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode(todos)
  }
  ```

- Function `DeleteTodo` melakukan penghapusan data berdasarkan ID

  ```go title=main.go
  // previous code...

  func DeleteTodo(w http.ResponseWriter, r *http.Request){
    params := mux.Vars(r)
    id := params["id"]
    var isGetTodo = false
    var index = 0

    for idx, todo := range todos {
      if id == todo.Id {
        isGetTodo = true
        index = idx
      }
    }

    if isGetTodo == false {
      w.WriteHeader(http.StatusNotFound)
      json.NewEncoder(w).Encode("ID: " + id + " not found")
      return
    }

    todos = append(todos[:index], todos[index+1:]...)

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode("ID: " + id + " delete success")
  }
  ```

### 3.2 Akses API dengan Postman

Untuk Mencoba API yang sudah kita buat kita memerlukan sebuah aplikasi yang dapat mengakses dan menyajian data dari sebuah API. untuk itu pastikan kalian sudah memiliki aplikasi yang bernama Postman, jika belum bisa mendownload melalui link dibawah ini

**Postman :[Download](https://www.postman.com/downloads/?utm_source=postman-home)**

**Dokumentasi Penggunaan Lebih lengkap : [Here](https://learning.postman.com/docs/sending-requests/requests/)**

```link title=baseUrl
https://ebook-code-results-stage-2-backend-git-3-e-d5f17e-demo-dumbways.vercel.app
```

Berikut list route yang telah dibuat ...

| Method | Route       |
| ------ | ----------- |
| GET    | `/todos`    |
| GET    | `/todo/:id` |
| POST   | `/todo`     |
| PATCH  | `/todo/:id` |
| DELETE | `/todo/:id` |

- Buka Aplikasi Postman
- Klik +, maka akan muncul tampilan seperti berikut :
  <img alt="image1-2" src={useBaseUrl('img/docs/Postman-1.png')} width="60%"/>
- Pastekan link yang sudah anda copy kesini

  <img alt="image1-2" src={useBaseUrl('img/docs/Postman-2.png')} width="60%"/>

- Tentukan Method dan Route yang tertera pada ebook

  <img alt="image1-2" src={useBaseUrl('img/docs/Postman-3.png')} width="60%"/>
