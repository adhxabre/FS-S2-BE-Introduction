---
sidebar_position: 7
---

# 7. Fetching Query with GORM

import useBaseUrl from '@docusaurus/useBaseUrl';

Pada bagian ini, kita akan menambahkan sebuah code yang akan melakukan pengambilan data ke database yang kita tuju.

## 7.1 Repositories

Untuk mendapatkan data dari tabel yang ada di database, kita perlu membuat `repositories` yang akan menjalankan method `FindUsers` dan `GetUser`.

```go {3-4} title=repositories/users.go
type UserRepository interface {
  CreateUser(user models.User) (models.User, error)
  FindUsers() ([]models.User, error)
  GetUser(ID int) (models.User, error)
}
```

```go {1-6,8-13} title=repositories/users.go
func (r *repository) FindUsers() ([]models.User, error) {
  var users []models.User
  err := r.db.Raw("SELECT * FROM users").Scan(&users).Error

  return users, err
}

func (r *repository) GetUser(ID int) (models.User, error) {
  var user models.User
  err := r.db.Raw("SELECT * FROM users WHERE id=?", ID).Scan(&user).Error

  return user, err
}
```

## 7.2 Handlers

Seperti pada code sebelumnya, untuk melakukan handling, tentunya kita perlu menambahkan file `users.go` pada folder `handlers` dan menambahkan code yang akan men-handling code logic kita

```go {3-16,18-34} title=handlers/users.go
// previous code...

func (h *handler) FindUsers(w http.ResponseWriter, r *http.Request) {
  w.Header().Set("Content-Type", "application/json")

  users, err := h.UserRepository.FindUsers()
  if err != nil {
    w.WriteHeader(http.StatusInternalServerError)
    response := dto.ErrorResult{Code: http.StatusBadRequest, Message: err.Error()}
    json.NewEncoder(w).Encode(response)
  }

  w.WriteHeader(http.StatusOK)
  response := dto.SuccessResult{Code: http.StatusOK, Data: users}
  json.NewEncoder(w).Encode(response)
}

func (h *handler) GetUser(w http.ResponseWriter, r *http.Request) {
  w.Header().Set("Content-Type", "application/json")

  id, _ := strconv.Atoi(mux.Vars(r)["id"])

  user, err := h.UserRepository.GetUser(id)
  if err != nil {
    w.WriteHeader(http.StatusBadRequest)
    response := dto.ErrorResult{Code: http.StatusBadRequest, Message: err.Error()}
    json.NewEncoder(w).Encode(response)
    return
  }

  w.WriteHeader(http.StatusOK)
  response := dto.SuccessResult{Code: http.StatusOK, Data: convertResponse(user)}
  json.NewEncoder(w).Encode(response)
}
```

## 7.3 Routes

Tahap akhir yang perlu kita lakukan adalah buat sebuah route untuk menjalakan atau mengakases fungsi pada handler `GetUsers` dan `FindUser` menggunakan HTTP method `get`

```go {16-17} title=routes/users.go
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
  r.HandleFunc("/users", h.FindUsers).Methods("GET")
  r.HandleFunc("/user/{id}", h.GetUser).Methods("GET")
}
```

## 7.4 Penggunaan

Cara mengambil data menggunakan `Postman` sebagai berikut:

- Buat dua request baru, yang bernama `user` dan `user by id`
- Gunakan HTTP Method: `GET`
- Gunakan endpoint: `/user` dan `/user/:id`
- Contoh endpoint pengambilan data user berdasarkan `id`:
  ```
  http://localhost:5000/api/v1/user/1
  ```
  \*Angka `1` merupakan `id` dari `user`
- Silakan tekan tombol `Send` dan pastikan response yang Anda terima sesuai dengan data yang tersimpan pada tabel `user`

## 7.5 Practice

Sesuaikan code Anda dengan contoh code berikut:
<a class="btn-example-code" href="/">
Contoh code
</a>

<br />
<br />

Berikut contoh endpoint yang dapat Anda gunakan:

```
https://ebook-code-results-stage-2-be.herokuapp.com/fundamental/api/v1/users
```

```
https://ebook-code-results-stage-2-be.herokuapp.com/fundamental/api/v1/user/3
```
