# Bài 4: Mô phỏng quy trình Hotfix & Gitflow thực tế

## 1. Mô tả

Hệ thống production đang chạy trên nhánh `main` với phiên bản ổn định `v1.0.0`.

Trong khi đó, nhánh `develop` đang phát triển các tính năng mới nên chưa thể deploy lên production.

Khi phát hiện lỗi nghiêm trọng liên quan đến việc lộ dữ liệu người dùng, em sử dụng quy trình Hotfix của Gitflow để sửa lỗi khẩn cấp.

## 2. Quy trình thực hiện

### Bước 1: Tạo nhánh Hotfix

Nhánh `hotfix/v1.0.1` được tạo trực tiếp từ nhánh `main`:

```bash
git checkout main
git checkout -b hotfix/v1.0.1
```

### Bước 2: Sửa lỗi

Thực hiện sửa lỗi bảo mật trên nhánh `hotfix/v1.0.1` và tạo commit:

```bash
git add .
git commit -m "fix: patch critical user data exposure"
```

### Bước 3: Merge Hotfix vào main

Sau khi kiểm tra bản sửa lỗi, merge nhánh hotfix vào `main`:

```bash
git checkout main
git merge hotfix/v1.0.1
```

### Bước 4: Tạo tag phiên bản

Tạo tag `v1.0.1` cho phiên bản production mới:

```bash
git tag -a v1.0.1 -m "Release Hotfix 1.0.1"
```

### Bước 5: Merge Hotfix ngược vào develop

Để đảm bảo lỗi không xuất hiện lại trong phiên bản tương lai, merge bản sửa lỗi vào `develop`:

```bash
git checkout develop
git merge hotfix/v1.0.1
```

## 3. Sơ đồ Gitflow

```text
                         develop
                    D1---D2---D3
                   /           \
                  /             \
                 /               \
                /                 \
main       M1---+-----------------M2
           |                       |
         v1.0.0                  v1.0.1
             \
              \
               H1
               |
               | fix: patch critical user data exposure
               |
               +----------------------> main
               |
               +----------------------> develop
```

Trong đó:

* `main`: nhánh production ổn định.
* `develop`: nhánh chứa các tính năng đang phát triển.
* `hotfix/v1.0.1`: nhánh sửa lỗi khẩn cấp, được tạo trực tiếp từ `main`.
* `v1.0.0`: phiên bản production trước khi sửa lỗi.
* `v1.0.1`: phiên bản production sau khi phát hành hotfix.
* Commit `H1`: commit chứa bản sửa lỗi bảo mật.

## 4. Kiểm tra kết quả

Kiểm tra branch:

```bash
git branch -a
```

Kiểm tra tag:

```bash
git tag
```

Kết quả cần có:

```text
v1.0.0
v1.0.1
```

Kiểm tra lịch sử commit và merge:

```bash
git log --graph --oneline --decorate --all
```

## 5. Kết luận

Quy trình Hotfix đã được thực hiện theo Gitflow:

`main` → `hotfix/v1.0.1` → `main`

Sau khi merge vào `main` và tạo tag `v1.0.1`, bản sửa lỗi tiếp tục được merge ngược vào:

`hotfix/v1.0.1` → `develop`

Việc này đảm bảo cả phiên bản production và phiên bản đang phát triển đều chứa bản sửa lỗi.
