---
sidebar_position: 9
---

# 9. Delete Query with GORM

import useBaseUrl from '@docusaurus/useBaseUrl';

Pada bagian ini, kita akan menambahkan sebuah code yang akan melakukan penghapusan data ke database yang kita tuju.

## 9.1 Repositories

Untuk melakukan penghapusan data dari tabel yang ada di database, kita perlu menambah `repositories`.

```go {6} title=repositories/users.go
type UserRepository interface {
  CreateUser(user models.User) (models.User, error)
  FindUsers() ([]models.User, error)
  GetUser(ID int) (models.User, error)
  UpdateUser(user models.User, ID int) (models.User, error)
  DeleteUser(user models.User, ID int) (models.User, error)
}
```

```go {3-7} title=repositories/users.go
// previous code...

func (r *repository) DeleteUser(user models.User,ID int) (models.User, error) {
  err := r.db.Raw("DELETE FROM users WHERE id=?",ID).Scan(&user).Error

  return user, err
}
```

## 9.2 Handler

Seperti pada code sebelumnya, untuk melakukan handling, tentunya kita perlu menambahkan file `users.go` pada folder `handlers` dan menambahkan code yang akan men-handling code logic kita

```go {3-27} title=handlers/users.go
// previous code...

func (h *handler) DeleteUser(w http.ResponseWriter, r *http.Request) {
  w.Header().Set("Content-Type", "application/json")

  id, _ := strconv.Atoi(mux.Vars(r)["id"])

  user, err := h.UserRepository.GetUser(id)
  if err != nil {
    w.WriteHeader(http.StatusBadRequest)
    response := dto.ErrorResult{Code: http.StatusBadRequest, Message: err.Error()}	
    json.NewEncoder(w).Encode(response)
    return
  }

  data, err := h.UserRepository.DeleteUser(user,id)
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

## 9.3 Route

Tahap akhir yang perlu kita lakukan adalah buat sebuah route untuk menjalakan atau mengakases fungsi pada handler `DeleteUser` menggunakan HTTP method `delete`

```go {19} title=routes/users.go
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
  r.HandleFunc("/user/{id}", h.DeleteUser).Methods("DELETE")
}
```

## 9.4 Penggunaan

Cara menghapus data menggunakan `Postman` sebagai berikut:

- Buat sebuah request baru, yang bernama `user`
- Gunakan HTTP Method: `DELETE`
- Gunakan endpoint: `/user/:id`
- Contoh endpoint pengambilan data user berdasarkan `id`:
  ```
  http://localhost:5000/api/v1/user/1
  ```
  \*Angka `1` merupakan `id` dari `user`
- Silakan tekan tombol `Send`, kemudian Anda akan mendapatkan `Response` seperti berikut:

  ```json title=Response
  {
    "status": "success",
    "message": "Delete user id: 1 finished"
  }
  ```

## 9.5 Practice

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
