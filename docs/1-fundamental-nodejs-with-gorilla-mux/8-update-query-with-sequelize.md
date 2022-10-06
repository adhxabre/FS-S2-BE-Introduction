---
sidebar_position: 8
---

# 8. Update Query with GORM

import useBaseUrl from '@docusaurus/useBaseUrl';

Pada bagian ini, kita akan menambahkan sebuah code yang akan melakukan perubahan data ke database yang kita tuju.

## 8.1 Repositories

Untuk mengubah data dari tabel yang ada di database, kita perlu membuat `repositories` yang akan menjalankan method `UpdateUsers`.

```go {5} title=repositories/users.go
type UserRepository interface {
  CreateUser(user models.User) (models.User, error)
  FindUsers() ([]models.User, error)
  GetUser(ID int) (models.User, error)
  UpdateUser(user models.User, ID int) (models.User, error)
}
```

```go title=repositories/users.go {3-8}
// previous code...

func (r *repository) UpdateUser(user models.User, ID int) (models.User, error) {
  err := r.db.Raw("UPDATE users SET name=?, email=?, password=? WHERE id=?", user.Name, user.Email, user.Password,ID).Scan(&user).Error

  return user, err
}
```

## 8.2 Handler

Seperti pada code sebelumnya, untuk melakukan handling, tentunya kita perlu menambahkan file `users.go` pada folder `handlers` dan menambahkan code yang akan men-handling code logic kita

```go {3-41} title=handlers/users.go
// previous code...

func (h *handler) UpdateUser(w http.ResponseWriter, r *http.Request) {
  w.Header().Set("Content-Type", "application/json")

  request := new(usersdto.UpdateUserRequest)
  if err := json.NewDecoder(r.Body).Decode(&request); err != nil {
    w.WriteHeader(http.StatusBadRequest)
    response := dto.ErrorResult{Code: http.StatusBadRequest, Message: err.Error()}	
    json.NewEncoder(w).Encode(response)
    return
  }

  id, _ := strconv.Atoi(mux.Vars(r)["id"])

  user := models.User{}

  if request.Name != "" {
    user.Name = request.Name
  }

  if request.Email != "" {
    user.Email = request.Email
  }

  if request.Password != "" {
    user.Password = request.Password
  }

  data, err := h.UserRepository.UpdateUser(user,id)
  if err != nil {
    w.WriteHeader(http.StatusInternalServerError)
    response := dto.ErrorResult{Code: http.StatusInternalServerError, Message: err.Error()}	
    json.NewEncoder(w).Encode(response)
    return
	}

  w.WriteHeader(http.StatusOK)
  response := dto.SuccessResult{Code: http.StatusOK, Data: convertResponse(data)}
  json.NewEncoder(w).Encode(response)
}
```

<!-- Untuk mengubah data, kita membutuhkan sebuah `id` yang akan digunakan untuk menentukan data mana yang ingin diubah. Kemudian kita siapkan `query` untuk mengubah data beserta `data terbaru` dan `id` sebagai kondisinya. Lalu eksekusi `query` tersebut menggunakan `sequelize`. -->

## 8.3 Route

Tahap akhir yang perlu kita lakukan adalah buat sebuah route untuk menjalakan atau mengakases fungsi pada handler `UpdateUser` menggunakan HTTP method `patch`

```go {18} title=routes/users.go
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
  r.HandleFunc("/user/{id}", h.UpdateUser).Methods("PATCH")
}
```

## 8.4 Penggunaan

Cara mengubah data menggunakan `Postman` sebagai berikut:

- Buat sebuah request baru, yang bernama `user`
- Gunakan HTTP Method: `PATCH`
- Gunakan endpoint: `/user/:id`
- Contoh endpoint mengubah data user berdasarkan `id`:
  ```
  http://localhost:5000/api/v1/user/1
  ```
  \*Angka `1` merupakan `id` dari `user`
- Pilih `Body` &rarr; `raw` &rarr; ubah `Text` menjadi `JSON`
- Ketik pada bagian `Request Body` seperti berikut:

  ```json {4} title=Request
  {
    "email": "user1@mail.com",
    "password": "123456",
    "name": "user 01",
    "status": "customer"
  }
  ```

- Silakan tekan tombol `Send`, kemudian Anda akan mendapatkan `Response` seperti berikut:

  ```json title=Response
  {
    "status": "success",
    "message": "Update user id: 1 finished",
    "data": {
      "email": "user1@mail.com",
      "password": "123456",
      "name": "user 01",
      "status": "customer"
    }
  }
  ```

## 8.5 Practice

Sesuaikan code Anda dengan contoh code berikut:
<a class="btn-example-code" href="/">
Contoh code
</a>

<br />
<br />

Berikut contoh endpoint yang dapat Anda gunakan:

```
https://ebook-code-results-stage-2-be.herokuapp.com/fundamental/api/v1/user/3
```
