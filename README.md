# Hướng dẫn cài Apache, MySQL, PHP (LAMP) stack, Phpmyadmin, CURL trên Ubuntu 18.04

## Bước 1: Cài đặt Apache và Update tường lửa

Cập nhật Ubuntu
```
$ sudo apt update
```
Cài đặt Apache sử bằng câu lệnh sau `apt`
```
$ sudo apt install apache2
```
Điều chỉnh tường lửa để cho phép lưu lượng truy cập web
```
$ sudo ufw app list
```
Chạy câu lệnh trên sẽ có kết quả như sau
```
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
```
Kiểm tra apache đang chạy port http và https là bao nhiêu
```
$ sudo ufw app info "Apache Full"
```
Sẽ có kết quả apache đang chạy port 80 và 443
```
Profile: Apache Full
Title: Web Server (HTTP,HTTPS)
Description: Apache v2 is the next generation of the omnipresent Apache web
server.

Ports:
  80,443/tcp
```
