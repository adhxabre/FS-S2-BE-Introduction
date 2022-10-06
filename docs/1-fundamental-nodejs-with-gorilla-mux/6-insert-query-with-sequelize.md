---
sidebar_position: 6
---

# 6. Insert Query with GORM

import useBaseUrl from '@docusaurus/useBaseUrl';

Pada bagian ini, kita akan menambahkan sebuah code yang akan melakukan penambahan data ke database yang kita tuju.

## 6.1 Repositories

Untuk menambahkan data ke tabel yang ada di database, kita perlu membuat `repositories` yang akan menjalankan method `CreateUser`.

```go {1,3-7,9-11,13-15,17-19,21-25} title=repositories/users.go
package repositories

import (
  "dumbmerch/models"
  "time"
  "gorm.io/gorm"
)

type UserRepository interface {
  CreateUser(user models.User) (models.User, error)
}

type repository struct {
  db *gorm.DB
}

func RepositoryUser(db *gorm.DB) *repository {
  return &repository{db} 
}

func (r *repository) CreateUser(user models.User) (models.User, error) {
  err := r.db.Exec("INSERT INTO users(name,email,password,created_at,updated_at) VALUES (?,?,?,?,?)",user.Name,user.Email, user.Password, time.Now(), time.Now()).Error

  return user, err
}
```

## 6.2 Handlers

Seperti pada code sebelumnya, untuk melakukan handling, tentunya kita perlu menambahkan file `users.go` pada folder `handlers` dan menambahkan code yang akan men-handling code logic kita

```go {1,3-14,16-18,20-22} title=handlers/users.go
package handlers

import (
  dto "dumbmerch/dto/result"
  usersdto "dumbmerch/dto/users"
  "dumbmerch/models"
  "dumbmerch/repositories"
  "encoding/json"
  "net/http"
  "strconv"

  "github.com/go-playground/validator/v10"
  "github.com/gorilla/mux"
)

type handler struct {
  UserRepository repositories.UserRepository
}

func HandlerUser(UserRepository repositories.UserRepository) *handler {
  return &handler{UserRepository} 
}
```

Kemudian kita akan menambahkan fungsi `CreateUser`, kita akan me-`request` value yang dikirimkan oleh Frontend dan kemudian mengeksekusinya dan pada fungsi `ConvertResponse` akan menampilkan data yang kita tambahkan pada `response`-nya

<br />

Didalam fungsi `CreateUser`, `request` adalah bagian penting yang akan menerima value-nya dan `validator` yang disimpan pada variabel `validation` adalah bagian yang akan melakukan validasi terhadap value yang sudah ditampung oleh `request`

```go {4-10,12-19} title=handlers/users.go
func (h *handler) CreateUser(w http.ResponseWriter, r *http.Request) {
  w.Header().Set("Content-Type", "application/json")

  request := new(usersdto.CreateUserRequest) 
  if err := json.NewDecoder(r.Body).Decode(&request); err != nil {
    w.WriteHeader(http.StatusBadRequest)
    response := dto.ErrorResult{Code: http.StatusBadRequest, Message: err.Error()}	
    json.NewEncoder(w).Encode(response)
    return
  }

  validation := validator.New()
  err := validation.Struct(request)
  if err != nil {
    w.WriteHeader(http.StatusBadRequest)
    response := dto.ErrorResult{Code: http.StatusBadRequest, Message: err.Error()}	
    json.NewEncoder(w).Encode(response)
    return
  }
}

func convertResponse(u models.User) usersdto.UserResponse {
  return usersdto.UserResponse{
    ID:       u.ID,
    Name:     u.Name,
    Email:    u.Email,
    Password: u.Password,
  }
}
```

Kemudian kita perlu juga menambahkan `model` dari `user` yang nantinya akan dirikimkan dari repository `CreateUser` melalui parameter-nya

```go {21-25,27-31,33-35} title=handlers/users.go
func (h *handler) CreateUser(w http.ResponseWriter, r *http.Request) {
  w.Header().Set("Content-Type", "application/json")

  request := new(usersdto.CreateUserRequest) 
  if err := json.NewDecoder(r.Body).Decode(&request); err != nil {
    w.WriteHeader(http.StatusBadRequest)
    response := dto.ErrorResult{Code: http.StatusBadRequest, Message: err.Error()}	
    json.NewEncoder(w).Encode(response)
    return
  }

  validation := validator.New()
  err := validation.Struct(request)
  if err != nil {
    w.WriteHeader(http.StatusBadRequest)
    response := dto.ErrorResult{Code: http.StatusBadRequest, Message: err.Error()}	
    json.NewEncoder(w).Encode(response)
    return
  }

  user := models.User{
    Name:     request.Name,
    Email:    request.Email,
    Password: request.Password,
  }

  data, err := h.UserRepository.CreateUser(user)
  if err != nil {
    w.WriteHeader(http.StatusInternalServerError)
    json.NewEncoder(w).Encode(err.Error())
  }

  w.WriteHeader(http.StatusOK)
  response := dto.SuccessResult{Code: http.StatusOK, Data: convertResponse(data)}
  json.NewEncoder(w).Encode(response)
}

func convertResponse(u models.User) usersdto.UserResponse {
  return usersdto.UserResponse{
    ID:       u.ID,
    Name:     u.Name,
    Email:    u.Email,
    Password: u.Password,
  }
}
```

## 6.3 Routes

Kemudian yang terakhir, kita perlu membuat `routes` pada file `users.go`. Seperti pada code sebelumnya, tentu penggunaan `routes` disini juga untuk me-routing dari `handlers` yang kita buat sebelumnya

Kita perlu membuat sebuah route untuk menjalakan atau mengakases fungsi pada handler `CreateUser` menggunakan HTTP method `post`

```go {1,3-9,11-16} title=routes/users.go
package routes

import (
  "dumbmerch/handlers"
  "dumbmerch/pkg/mysql"
  "dumbmerch/repositories"

  "github.com/gorilla/mux"
)

func UserRoutes(r *mux.Router) {
  userRepository := repositories.RepositoryUser(mysql.DB)
  h := handlers.HandlerUser(userRepository)

  r.HandleFunc("/user", h.CreateUser).Methods("POST")
}
```

Setelah menambahkan `routes` pada file `users.go`, kita akan menambahkan `RouteInit` yang ada pada `routes.go`

```go {9} title=routes/users.go
package routes

import (
  "github.com/gorilla/mux"
)

func RouteInit(r *mux.Router) {
  TodoRoutes(r)
  UserRoutes(r)
}
```

## 6.4 Penggunaan

Cara menambahkan data menggunakan `Postman` sebagai berikut:

- Buat sebuah request baru, yang bernama `user`
- Gunakan HTTP Method: `POST`
- Gunakan endpoint: `/user`
- Pilih `Body` &rarr; `raw` &rarr; ubah `Text` menjadi `JSON`
- Ketik pada bagian `Request Body` seperti berikut:

  ```json title=Request
  {
    "email": "user1@mail.com",
    "password": "123456",
    "name": "user satu"
  }
  ```

- Silakan tekan tombol `Send`, kemudian Anda akan mendapatkan `Response` seperti berikut:

  ```json title=Response
  {
    "code": 200,
    "data" : {
      "name": "user satu",
      "email": "user1@mail.com",
      "password": "123456"
    }
  }
  ```

- Silakan cek Database Anda, untuk memastikan data berhasil disimpan

## 6.5 Practice

Sesuaikan code Anda dengan contoh code berikut:
<a class="btn-example-code" href="/">
Contoh code
</a>

<br />
<br />

Berikut contoh endpoint yang dapat Anda gunakan:

```
https://ebook-code-results-stage-2-be.herokuapp.com/fundamental/api/v1/user
```
