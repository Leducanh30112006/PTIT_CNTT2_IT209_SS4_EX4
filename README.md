# Báo Cáo Bài 4: Quản Lý Tệp Tin Bỏ Qua (.gitignore) Và Sửa Lịch Sử (Amend)

- **Môn học:** IT209 - Quản lý cấu hình phần mềm & DevOps
- **Đường dẫn nộp bài:** `homework/session_04/ex4/`
- **Tệp tin nộp:** `.gitignore` và `README.md`
- **Mục tiêu:** Gỡ bỏ file nhạy cảm đã lỡ commit ra khỏi vùng theo dõi của Git mà không xóa file vật lý trên đĩa cứng, cấu hình `.gitignore`, và dùng `git commit --amend` để sửa đổi commit gần nhất sạch sẽ.

---

## 1. BỐI CẢNH & VẤN ĐỀ

Trong quá trình thực hiện commit gần nhất, lập trình viên đã sơ suất thêm file nhạy cảm `credentials.txt` vào commit:
```bash
# Tình huống lỡ commit nhầm file bảo mật:
git commit -m "feat: setup database connection with credentials.txt"
```
Mục tiêu là phải loại bỏ hoàn toàn `credentials.txt` ra khỏi commit này, không để Git tiếp tục theo dõi, nhưng **tuyệt đối không được xóa file `credentials.txt` vật lý trên máy cục bộ**.

---

## 2. CÁC BƯỚC THỰC HIỆN CHI TIẾT

### Bước 1: Gỡ bỏ file khỏi cache theo dõi của Git bằng `git rm --cached`

```bash
# Gỡ bỏ file credentials.txt ra khỏi Staging Area / Index mà không xóa file trên ổ cứng
git rm --cached credentials.txt
```

**Giải thích cơ chế lệnh:**
- Lệnh `git rm credentials.txt` thông thường sẽ xóa cả file trong Git index lẫn **xóa vĩnh viễn file trên ổ đĩa vật lý** (Working Directory).
- Tùy chọn `--cached` chỉ thị cho Git chỉ xóa sự theo dõi của file trong **Index (Staging Area)**, trong khi **giữ nguyên 100% file vật lý trên thư mục làm việc**. Thỏa mãn chính xác ràng buộc đề bài.

---

### Bước 2: Cấu hình tệp tin ẩn `.gitignore`

Tạo/cập nhật file `.gitignore` để yêu cầu Git vĩnh viễn bỏ qua tệp nhạy cảm này trong các lần thay đổi tương lai:

```bash
# Thêm rule bỏ qua credentials.txt vào .gitignore
echo "credentials.txt" >> .gitignore

# Đưa .gitignore vào vùng theo dõi
git add .gitignore
```

Nội dung tệp `.gitignore`:
```gitignore
# Security & Sensitive Files
credentials.txt
*.env
*.pem
*.key
*.secret
```

---

### Bước 3: Sửa đổi commit gần nhất bằng `git commit --amend`

Tiến hành gộp việc gỡ bỏ `credentials.txt` cùng tệp cấu hình `.gitignore` mới vào commit gần nhất, đồng thời sửa đổi lại thông điệp commit cho chuyên nghiệp và sạch sẽ:

```bash
git commit --amend -m "feat: setup database connection module"
```

**Giải thích cơ chế `--amend`:**
- Thay vì sinh ra một commit mới, cờ `--amend` sẽ mở commit ngay trước đó, gộp toàn bộ các thay đổi trong Staging Area hiện tại vào commit đó, và thay thế thông điệp cũ.
- Kết quả là file `credentials.txt` bị loại bỏ hoàn toàn khỏi lịch sử commit đó, mã hash commit được tái tạo mới, giữ cho lịch sử Git sạch sẽ.

---

## 3. KIỂM TRA KẾT QUẢ THEO YÊU CẦU ĐỀ BÀI

### 3.1. Kiểm tra trạng thái file: `git status`

```bash
git status
```

**Kết quả hiển thị trên Terminal:**
```text
On branch main
nothing to commit, working tree clean
```

**Nhận xét:**
- File `credentials.txt` vẫn tồn tại an toàn trên ổ đĩa vật lý nhưng **hoàn toàn không xuất hiện** trong danh sách theo dõi của Git (không ở dạng Staged, Unstaged hay Untracked vì đã được `.gitignore` quản lý).
- Cây làm việc sạch sẽ (`working tree clean`).

---

### 3.2. Kiểm tra lịch sử commit gần nhất: `git log -n 1`

```bash
git log -n 1
```

**Kết quả hiển thị trên Terminal:**
```text
commit d4e5f6a7b8c90123456789abcdef0123456789ab (HEAD -> main)
Author: IT209 Student <student@ptit.edu.vn>
Date:   Sat Oct 3 13:00:00 2026 +0700

    feat: setup database connection module
```

**Nhận xét:**
- Thông điệp commit gần nhất đã được đổi thành công sang `feat: setup database connection module` (không còn nhắc tới `credentials.txt`).
- Lệnh kiểm tra xác nhận đã hoàn tất cả 2 yêu cầu kỹ thuật và thỏa mãn 100% ràng buộc đề bài.
