---
sidebar_position: 4
---

# 4. Group Routes

import useBaseUrl from '@docusaurus/useBaseUrl';

**Group Routes** adalah teknik mengelompokkan route API agar lebih teratur dan terdokumentasi dengan baik.

### 4.1 Persiapan Struktur File

Tahap awal, perlu dilakukan persiapan dari sisi folder dan file. Jadi Anda perlu membuat folder baru seperti `handlers` dan `routes`. Kemudian didalam folder `handlers` terdapat file `todo.go`, dan didalam folder `routes` terdapat file `todos.go` dan `routes.go`.

```text {2-3,6-8}
mux-api-app
 ┣ handlers
 ┃ ┗ todo.go
 ┣ .gitignore
 ┣ main.go
 ┗ routes
   ┣ routes.go
   ┗ todos.go
```

### 4.2 Handlers

**Handler** berfungsi sebagai layer yang bertugas untuk menangani proses logic. Ketika client mengakses `route`, maka selanjutnya system akan membawa ke `handler` yang dituju kemudian akan mengeksekusi code logic yang terdapat didalam `handler` tersebut.

<a class="btn-example-code" href="/">
Contoh code
</a>

<br />
<br />

Pertama, kita membuat satu file bernama `todo.go` yang akan ditaruh pada folder `handlers`. Didalamnya kita akan membuat sebuah struktur tipe yang berguna untuk mengelompokkan struct dan tipe datanya, sehingga akan terlihat lebih rapih dan mudah untuk di-atur 

```go {1,3-7,9-13,15-26} title=handlers/todo.go
package handlers

import (
	"net/http"
	"github.com/gorilla/mux"
	"encoding/json"
)

type Todos struct {
	Id string `json:"id"`
	Title string `json:"title"`
	IsDone bool `json:"isDone"`
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
```

Dan setelahnya, kita bisa membuat sebuah fungsi untuk meng-handle berbagai macam logic nantinya

- Handler untuk mencari keseluruhan data

	```go title=handlers/todo.go
	func FindTodos(w http.ResponseWriter, r *http.Request){
		w.Header().Set("Content-Type", "application/json")
		w.WriteHeader(http.StatusOK)
		json.NewEncoder(w).Encode(todos)
	}
	```

- Handler untuk mencari data berdasarkan ID yang spesifik

	```go title=handlers/todo.go
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

- Handler untuk menambah data berdasarkan `global variable`-nya

	```go title=handlers/todo.go
	func CreateTodo(w http.ResponseWriter, r *http.Request){
		var data Todos

		json.NewDecoder(r.Body).Decode(&data)

		todos = append(todos, data)

		w.Header().Set("Content-Type", "application/json")
		w.WriteHeader(http.StatusOK)
		json.NewEncoder(w).Encode(todos)
	}
	```

- Handler untuk melakukan perubahan pada data yang sudah ada

	```go title=handlers/todo.go
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

- Handler untuk menghapus data berdasarkan `global variable`-nya

	```go title=handlers/todo.go
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

### 4.3 Routes

Pada file ini terdapat proses penghubung antara `route` dan `handler`.
Tentunya, untuk penjelasan terkait `routing` sudah disebutkan pada halaman sebelumnya dan seharusnya kamu sudah mengerti kegunaan `routes`.

<a class="btn-example-code" href="/">
Contoh code
</a>

<br />
<br />

Pada bagian ini, kita akan membuat `routing` dari `handlers` yang sudah kita buat

```go {1,3-6,8-14} title=routes/todos.go
package routes

import (
	"dumbmerch/handlers"
	"github.com/gorilla/mux"
)

func TodoRoutes(r *mux.Router) {
	r.HandleFunc("/todos", handlers.FindTodos).Methods("GET")
	r.HandleFunc("/todo/{id}", handlers.GetTodo).Methods("GET")
	r.HandleFunc("/todo", handlers.CreateTodo).Methods("POST")
	r.HandleFunc("/todo/{id}", handlers.UpdateTodo).Methods("PATCH")
	r.HandleFunc("/todo/{id}", handlers.DeleteTodo).Methods("DELETE")
}
```

Kemudian dilanjutkan dengan memasukkan `routing` yang sudah kita buat kedalam `initializer` untuk routing-nya

```go {1,3-5,7-9} title=routes/routes.go
package routes

import (
	"github.com/gorilla/mux"
)

func RouteInit(r *mux.Router) {
	TodoRoutes(r)
}
```

### 4.4 Root file

Pada bagian ini kita melakukan `grouping` berdasarkan `route` yang telah dibuat pada folder `routes`.

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/blob/4-expressjs-fundamental/index.js">
Contoh code
</a>

<br />
<br />

```go {7,13} title=main.go
package main

import (
	"fmt"
	"net/http"
	"github.com/gorilla/mux"
	"dumbmerch/routes"
)

func main() {
	r := mux.NewRouter()

	routes.RouteInit(r.PathPrefix("/api/v1").Subrouter())

	fmt.Println("server running localhost:5000")
	http.ListenAndServe("localhost:5000", r)
}
```

### 4.5 Akses API Group route dengan Postman

```link title=baseUrl
https://ebook-code-results-stage-2-backend-git-4-e-a80eda-demo-dumbways.vercel.app
```

Berikut list route yang telah dibuat :

| Method | Group     | Route       |
| ------ | --------- | ----------- |
| GET    | `/api/v1` | `/todos`    |
| GET    | `/api/v1` | `/todo/{id}` |
| POST   | `/api/v1` | `/todo`     |
| PATCH  | `/api/v1` | `/todo/{id}` |
| DELETE | `/api/v1` | `/todo/{id}` |

Untuk menggunakan grouping route, Anda dapat menambahkan `baseUrl` terlebih dahulu kemudian `group`, lalu diikuti `route`, berikut contohnya:

```
https://ebook-code-results-stage-2-backend-git-4-e-a80eda-demo-dumbways.vercel.app/api/v1/todos
```

```

```
