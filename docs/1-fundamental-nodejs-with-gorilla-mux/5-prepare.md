---
sidebar_position: 5
---

# 5. Preparation

import useBaseUrl from '@docusaurus/useBaseUrl';

## 5.1 Installation Package

- GORM

  ```bash
  go get -u gorm.io/gorm
  ```

- MySql

  ```bash
  go get -u gorm.io/driver/mysql
  ```

  Setelah menjalankan perintah diatas, maka terdapat tambahan file & folder pada aplikasi `mux-api-app` sebagai berikut:

  ```text {2-3,8-12}
  mux-api-app
 ┣ database
 ┃ ┗ migration.go
 ┣ handlers
 ┃ ┗ todo.go
 ┣ .gitignore
 ┣ main.go
 ┣ models
 ┃ ┗ user.go
 ┣ pkg
 ┃ ┗ mysql
 ┃ ┃ ┗ mysql.go
 ┗ routes
   ┣ routes.go
   ┗ todos.go
  ```

## 5.2 Database

- Buat sebuah database terlebih dahulu (Contoh: `course-mux`)

- Buat konfigurasi tabel pada file `user.go` yang terdapat dalam folder `models`

  ```go {5-15,17-21,23-25} title=models/user.go
  package models

  import "time"

  type User struct {
    ID        int                   `json:"id"`
    Name      string                `json:"name" gorm:"type: varchar(255)"`
    Email     string                `json:"email" gorm:"type: varchar(255)"`
    Password  string                `json:"-" gorm:"type: varchar(255)"`
    Status    string                `json:"status" gorm:"type: varchar(50)"`
    Profile   ProfileResponse       `json:"profile"`
    Products  []ProductUserResponse `json:"products" gorm:"constraint:OnUpdate:CASCADE,OnDelete:CASCADE;"`
    CreatedAt time.Time             `json:"-"`
    UpdatedAt time.Time             `json:"-"`
  }

  type UsersProfileResponse struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
    Email string `json:"email"`
  }

  func (UsersProfileResponse) TableName() string {
    return "users"
  }
  ```

- Atur konfigurasi migrasi pada file `migration.go` yang terdapat dalam folder `database`

  ```go {1,3-7,9-18} title=database/migration.go
  package database

  import (
    "dumbmerch/models"
    "dumbmerch/pkg/mysql"
    "fmt"
  )

  func RunMigration() {
    err := mysql.DB.AutoMigrate(&models.User{})

    if err != nil {
      fmt.Println(err)
      panic("Migration Failed")
    }

    fmt.Println("Migration Success")
  }
  ```

## 5.3 Database Connection

- Buatlah folder `pkg`, kemudian buatlah folder `mysql` dan buat file `mysql.go` didalamnya

  ```go {1,3-7,9,11-18,20-21} title=pkg/mysql/mysql.go
  package mysql

  import (
    "fmt"
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
  )

  var DB *gorm.DB

  func DatabaseInit() {
    var err error
    dsn := "{USER}:{PASSWORD}@tcp({HOST}:{POST})/{DATABASE}?charset=utf8mb4&parseTime=True&loc=Local"
    DB, err = gorm.Open(mysql.Open(dsn), &gorm.Config{})

    if err != nil {
      panic(err)
    }

    fmt.Println("Connected to Database")
  }
  ```

  Pada fungsi `DatabaseInit`, kita akan meng-konfigurasi koneksi antara aplikasi Backend kita dengan Database

## 5.4 Data Transfer Object (DTO)

- Buatlah folder `dto`, dan didalamnya buatlah folder `result` dan `users`

- Pada folder `result`, buatlah `result.go`

  ```go {1,3-6,8-11} title=dto/result/result.go
  package dto

  type SuccessResult struct {
    Code int         `json:"code"`
    Data interface{} `json:"data"`
  }

  type ErrorResult struct {
    Code    int    `json:"code"`
    Message string `json:"message"`
  }
  ```

- Pada folder `users`, buatlah `user_request.go`

  ```go {1,3-7,9-13} title=dto/users/user_request.go
  package usersdto

  type CreateUserRequest struct {
    Name     string `json:"name" form:"name" validate:"required"`
    Email    string `json:"email" form:"email" validate:"required"`
    Password string `json:"password" form:"password" validate:"required"`
  }

  type UpdateUserRequest struct {
    Name     string `json:"name" form:"name"`
    Email    string `json:"email" form:"email"`
    Password string `json:"password" form:"password"`
  }
  ```

- Pada folder `users`, buatlah `user_response.go`

  ```go {1,3-8} title=dto/users/user_response.go
  package usersdto

  type UserResponse struct {
    ID       int    `json:"id"`
    Name     string `json:"name" form:"name" validate:"required"`
    Email    string `json:"email" form:"email" validate:"required"`
    Password string `json:"password" form:"password" validate:"required"`
  }
  ```