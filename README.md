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
Cho phép lưu lượng HTTP và HTTPS đến cho cấu hình này
```
$ sudo ufw allow in "Apache Full"
```
Truy cập ``` http://localhost ``` sẽ có kết quả sau:
![http://assets.digitalocean.com/articles/how-to-install-lamp-ubuntu-18/small_apache_default_1804.png]
