# DevOps Hackathon - Đề 001

## 1. Thông tin sinh viên

- **Họ và tên**: Đặng thanh bình
- **Mã sinh viên**: K24
- **Lớp**: CNTT2
- **Tài khoản Linux**: binh2810
- **GitHub**: binhptit2810
- **Cổng Nginx**: 8080

## 2. Môi trường triển khai

- **Ubuntu version**: Ubuntu 20.04.6 LTS
- **Nginx version**: 1.18.0 (Ubuntu)
- **Git version**: [chạy: git --version]
- **Môi trường**: VPS
- **IP server**: 221.121.3.53
- **Port**: [CẦN DÙNG PORT CÁ NHÂN, KHÔNG DÙNG 80]

## 4. Cấu hình Nginx

| Tham số         | Giá trị                                         | Giải thích                 |
| --------------- | ----------------------------------------------- | -------------------------- |
| PORT            | [PORT]                                          | Cổng Nginx được phân bổ    |
| SERVER_NAME     | 221.121.4.58                                    | IP address của server      |
| WEB_ROOT        | /var/www/devops-hackathon-de001-An256122006/src | Thư mục chứa file website  |
| INDEX_FILE      | index.html                                      | File mặc định khi truy cập |
| TEN_TAI_KHOAN   | binh2810                                        | Tên tài khoản Linux        |
| ALLOW_DIRECTIVE | allow all                                       | Cho phép tất cả truy cập   |

## 5. UFW Firewall

Các rule đã cấu hình:

```
Status: active

22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
8080/tcp                   ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)
8080/tcp (v6)              ALLOW IN    Anywhere (v6)
```

## 6. Các bước triển khai

### 1.3. Sinh cặp khóa ED25519

**PowerShell (Windows)**

Chạy:

```powershell
ssh-keygen -t ed25519
```

Khi xuất hiện:

```text
Enter file in which to save the key (C:\Users\Dell/.ssh/id_ed25519):
```

→ Nhấn **Enter**

Tiếp tục:

```text
Enter passphrase (empty for no passphrase):
```

→ Nhấn **Enter**

Tiếp tục:

```text
Enter same passphrase again:
```

→ Nhấn **Enter**

**Kết quả:**

- `C:\Users\Dell\.ssh\id_ed25519` → Private Key
- `C:\Users\Dell\.ssh\id_ed25519.pub` → Public Key

---

### 1.4. Đăng nhập VPS bằng SSH (mật khẩu)

**PowerShell (Windows)**

Sau khi tạo key, đăng nhập VPS:

```powershell
ssh root@<IP_VPS>
```

Lần đầu có thể hỏi:

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

→ Gõ `yes` và nhấn **Enter**.

Sau đó nhập mật khẩu `root`.

**Thành công** khi thấy dấu nhắc:

```text
root@IT209:~#
```

> **Lưu ý:** Tạo SSH Key trên Windows không có nghĩa là VPS đã biết public key. Vì vậy lần đăng nhập đầu tiên vẫn yêu cầu mật khẩu.

---

### 1.5. Đưa public key lên VPS

#### Bước A – Tạo thư mục SSH

**VPS**

Tại dấu nhắc `root@IT209:~#`, chạy:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

#### Bước B – Lấy Public Key

**PowerShell (Windows)**

Mở PowerShell mới hoặc thoát VPS bằng lệnh `exit`. Sau đó chạy:

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

Hoặc:

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub
```

Kết quả sẽ có dạng:

```text
ssh-ed25519 AAAAC3... dell@DESKTOP-XXXX
```

Copy toàn bộ một dòng, bắt đầu bằng `ssh-ed25519`.

#### Bước C – Ghi Public Key vào VPS

**VPS**

Đăng nhập lại:

```powershell
ssh root@<IP_VPS>
```

Nhập mật khẩu `root`. Sau khi thấy `root@IT209:~#`, chạy lệnh:

````bash
echo 'PUBLIC_KEY' > ~/.ssh/authorized_keys
**Ví dụ:**

```bash
echo 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGfXmRFV9EAPAAhi08DLHvnJEs0v16mGwO+fKUF3UucF dell@DESKTOP-3HIJIT2' > ~/.ssh/authorized_keys
````

#### Bước D – Cấp quyền cho authorized_keys

**VPS**

Chạy:

```bash
chmod 600 ~/.ssh/authorized_keys
```

Kiểm tra nội dung:

```bash
cat ~/.ssh/authorized_keys
```

Kiểm tra phân quyền:

```bash
ls -la ~/.ssh
```

Kết quả mong đợi:

```text
drwx------  ... .
-rw-------  ... authorized_keys
```

---

### 1.6. Kiểm tra đăng nhập bằng SSH Key

> **Không đóng phiên SSH hiện tại** cho đến khi kiểm tra key thành công.

Mở một cửa sổ PowerShell mới trên Windows và chạy:

```powershell
ssh root@<IP_VPS>
```

Nếu cấu hình đúng, VPS sẽ đăng nhập thẳng:

```text
Welcome to Ubuntu ...
root@IT209:~#
```

Không hỏi mật khẩu → SSH Key hoạt động thành công.  
Sau khi xác nhận đăng nhập thành công, bạn có thể đóng phiên SSH cũ.

### Trình tự 6 bước chuẩn cần nhớ

```text
1. ssh-keygen -t ed25519
        ↓
2. ssh root@IP_VPS
   (đăng nhập password lần đầu)
        ↓
3. mkdir -p ~/.ssh && chmod 700 ~/.ssh
        ↓
4. echo '<PUBLIC_KEY>' > ~/.ssh/authorized_keys
        ↓
5. chmod 600 ~/.ssh/authorized_keys
        ↓
6. ssh root@IP_VPS (đăng nhập không cần password)
```

---

### Checklist

- [ ] Đã tạo key ED25519
- [ ] Có file `id_ed25519`
- [ ] Có file `id_ed25519.pub`
- [ ] Đã đăng nhập VPS bằng password
- [ ] `~/.ssh` có quyền `700`
- [ ] Đã đưa Public Key vào `authorized_keys`
- [ ] `authorized_keys` có quyền `600`
- [ ] Đăng nhập SSH bằng key không hỏi password

## 4. Phần 3 – Thư mục website, chown, chmod

**VPS (user có sudo)**

### 3.1. Tạo thư mục website

```bash
sudo mkdir -p /var/www/my-web
```

### 3.2. Gán quyền sở hữu (chown)

```bash
sudo chown -R <user_name>:<user_name> /var/www/my-web
```

### 3.3. Cấp quyền (chmod 755)

```bash
sudo chmod -R 755 /var/www/my-web
```

```text
7 = rwx  → Owner : đọc + ghi + thực thi
5 = r-x  → Group : đọc + thực thi
5 = r-x  → Others: đọc + thực thi
```

### 3.4. Tạo `index.html`

```bash
nano /var/www/my-web/index.html
```

```html
<!DOCTYPE html>
<html>
  <head>
    <title></title>
  </head>
  <body>
    <h1></h1>
  </body>
</html>
```

Lưu: `Ctrl + O` → `Enter` → `Ctrl + X`

### 3.5. Tạo `404.html`

```bash
nano /var/www/my-web/404.html
```

```html
<!DOCTYPE html>
<html>
  <head>
    <title>404</title>
  </head>
  <body>
    <h1>404 - Page Not Found</h1>
  </body>
</html>
```
