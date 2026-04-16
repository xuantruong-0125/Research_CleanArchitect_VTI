# A. Clean Architecture Overview

## 1. Giới thiệu

Clean Architecture là một phương pháp thiết kế phần mềm nhằm tách biệt **business logic** khỏi các thành phần bên ngoài như framework, database hay giao diện người dùng. Mục tiêu chính là giúp hệ thống:

- Dễ bảo trì
- Dễ mở rộng
- Dễ kiểm thử
- Không phụ thuộc vào công nghệ cụ thể

Nguyên lý cốt lõi của kiến trúc này dựa trên tư tưởng **độc lập** và nguyên lý **Dependency Inversion**.

---

## 2. Nguyên tắc cốt lõi

### Dependency Rule

> Các layer bên trong không được phụ thuộc vào các layer bên ngoài.

Điều này có nghĩa:

- Business logic không biết gì về framework, database hay UI
- Các phần bên ngoài có thể thay đổi mà không ảnh hưởng đến lõi hệ thống
- Dependency luôn hướng từ ngoài vào trong

---

## 3. Các layer trong Clean Architecture

### 3.1 Entities (Domain Layer)

Đây là layer quan trọng nhất của hệ thống.

**Vai trò:**
- Chứa business logic cốt lõi
- Định nghĩa các quy tắc nghiệp vụ
- Đảm bảo tính đúng đắn của hệ thống

**Đặc điểm:**
- Không phụ thuộc bất kỳ framework nào
- Không liên quan đến database hay UI
- Có thể chạy độc lập và test dễ dàng

---

### 3.2 Use Cases (Application Layer)

Layer này định nghĩa các hành động mà hệ thống cho phép thực hiện.

**Vai trò:**
- Điều phối business logic
- Xử lý các kịch bản nghiệp vụ cụ thể
- Kết nối giữa entities và các layer bên ngoài

**Đặc điểm:**
- Không phụ thuộc vào cách dữ liệu được lưu trữ
- Không phụ thuộc UI
- Chỉ tập trung vào việc "hệ thống làm gì"

---

### 3.3 Interface Adapters

Đây là lớp trung gian giữa hệ thống và thế giới bên ngoài.

**Vai trò:**
- Chuyển đổi dữ liệu giữa các layer
- Đảm bảo dữ liệu phù hợp với từng mục đích sử dụng
- Kết nối use case với các thành phần bên ngoài

**Đặc điểm:**
- Không chứa business logic
- Chỉ xử lý việc chuyển đổi và giao tiếp

---

### 3.4 Frameworks & Drivers

Đây là layer ngoài cùng của hệ thống.

**Vai trò:**
- Cung cấp các công cụ để hệ thống hoạt động
- Bao gồm database, framework, UI, network

**Đặc điểm:**
- Là phần dễ thay đổi nhất
- Không nên chứa business logic
- Có thể thay thế mà không ảnh hưởng đến core

---

## 4. Chuyển đổi dữ liệu giữa các layer

Mỗi layer nên có model riêng phù hợp với mục đích của nó:

- Domain model phục vụ business logic
- Data model phục vụ lưu trữ
- View model phục vụ hiển thị

Việc chuyển đổi giữa các model là cần thiết để:
- Tránh phụ thuộc chéo giữa các layer
- Giữ cho mỗi layer tập trung đúng trách nhiệm
- Tăng khả năng bảo trì và mở rộng

---

## 5. Lợi ích

### Mạch lạc
- Cấu trúc rõ ràng
- Dễ hiểu mục đích của từng phần trong hệ thống

### Linh hoạt
- Không phụ thuộc framework hay database
- Dễ thay đổi công nghệ

### Dễ kiểm thử
- Test business logic độc lập
- Không cần chạy toàn bộ hệ thống

---

# B. Giới thiệu project

Project này xây dựng hệ thống **quản lý người dùng và phân quyền theo menu và chức năng** dựa trên mô hình RBAC (Role-Based Access Control), với các bảng chính: `users`, `roles`, `permissions`, `menus`, `organizations`.

Trong project này, Clean Architecture được áp dụng nhằm:

- Tách biệt hoàn toàn **business logic phân quyền** khỏi framework và database  
- Đảm bảo logic kiểm tra quyền (user → role → permission → menu) nằm ở **core system**  
- Giúp hệ thống dễ mở rộng khi thay đổi UI, database hoặc cơ chế xác thực  

Cụ thể:

- **Domain** chứa logic cốt lõi như User, Role, Permission và các quy tắc phân quyền  
- **Use Case** xử lý các nghiệp vụ như tạo user, gán quyền, kiểm tra quyền truy cập  
- **Interface Adapter** đảm nhiệm việc giao tiếp với client (API) và chuyển đổi dữ liệu  
- **Infrastructure** xử lý các chi tiết kỹ thuật như database và framework  

Cách tổ chức này giúp đảm bảo:

> Logic phân quyền là trung tâm và không bị ảnh hưởng bởi các thay đổi công nghệ

## Cấu trúc project
```text
src/main/java/org/example/moduleuser_ca
│
├── domain/
│   ├── model/
│   ├── repository/
│   └── usecase/
│
├── application/
│   └── service/
│
├── infrastructure/
│   └── persistence/
│       ├── entity/
│       ├── repository/
│       └── mapper/
│
├── interfaces/
│   ├── controller/
│   ├── dto/
│   │   ├── request/
│   │   └── response/
│   └── mapper/
│
├── config/
└── common/
```

---

### 1. Entities Layer (Domain Layer)

Đây là layer lõi, chứa business logic quan trọng nhất của hệ thống.

#### `domain/model`
**Vai trò:**
- Định nghĩa các đối tượng nghiệp vụ (User, Role, Permission, Menu, Organization)
- Chứa business rules cốt lõi
- Đảm bảo tính đúng đắn của hệ thống

**Đặc điểm:**
- Không phụ thuộc framework
- Không liên quan database hay API
- Có thể chạy độc lập và dễ test

---

#### `domain/repository`
**Vai trò:**
- Định nghĩa interface truy cập dữ liệu (Repository pattern)
- Là cầu nối giữa domain và các tầng bên ngoài

**Đặc điểm:**
- Không chứa logic truy vấn cụ thể
- Không biết database là gì
- Chỉ mô tả “cần dữ liệu như thế nào”

---

### 2. Use Cases Layer (Application Business Rules)

Layer này định nghĩa và thực thi các hành động của hệ thống.

#### `domain/usecase`
**Vai trò:**
- Định nghĩa các use case (hành động hệ thống)
- Là contract cho business logic

**Ví dụ:**
- CreateUserUseCase
- CheckPermissionUseCase

---

#### `application/service`
**Vai trò:**
- Implement các use case
- Điều phối logic nghiệp vụ

**Đặc điểm:**
- Sử dụng domain model và repository interface
- Không phụ thuộc vào framework cụ thể

---

### 3. Interface Adapters Layer

Layer này đóng vai trò trung gian giữa hệ thống và thế giới bên ngoài.

#### `interfaces/controller`
**Vai trò:**
- Nhận request từ client
- Gọi use case để xử lý

**Lưu ý:**
- Không chứa business logic

---

#### `interfaces/dto`
**Vai trò:**
- Định nghĩa dữ liệu request/response cho API
- Tách biệt dữ liệu giao tiếp với domain model

---

#### `interfaces/mapper`
**Vai trò:**
- Chuyển đổi dữ liệu giữa DTO và domain model

---

#### `infrastructure/persistence/mapper`
**Vai trò:**
- Chuyển đổi giữa Entity (database) và Domain model

---

### 4. Frameworks & Drivers Layer

Đây là layer ngoài cùng, chứa các công cụ và công nghệ cụ thể.

#### `infrastructure/persistence/entity`
**Vai trò:**
- Mapping dữ liệu với database
- Định nghĩa cấu trúc bảng

---

#### `infrastructure/persistence/repository`
**Vai trò:**
- Implement các repository interface từ domain
- Thực hiện truy vấn database

---

#### `config`
**Vai trò:**
- Cấu hình hệ thống (security, bean, filter, ...)

---

### 5. Common (Shared Layer)

#### `common`
**Vai trò:**
- Chứa các thành phần dùng chung toàn hệ thống:
  - Exception
  - Utils
  - Constants

**Đặc điểm:**
- Không thuộc riêng layer nào
- Được sử dụng bởi nhiều layer khác nhau

---

## 6. Tổng kết

- `domain` là lõi hệ thống, chứa logic quan trọng nhất  
- `application` thực thi các hành động nghiệp vụ  
- `interfaces` xử lý giao tiếp với bên ngoài  
- `infrastructure` xử lý chi tiết kỹ thuật (database, framework)  
- `config` và `common` hỗ trợ toàn hệ thống  

