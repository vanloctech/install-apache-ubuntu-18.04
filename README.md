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
![ahihi](http://assets.digitalocean.com/articles/how-to-install-lamp-ubuntu-18/small_apache_default_1804.png)

Cài đặt CURL
```
$ sudo apt install curl
```

## Bước 2: Cài đặt MYSQL

Cập nhật và cài mysql
```
$ sudo apt-get update
$ sudo apt install mysql-server
```
Nhấn Y và enter khi được hỏi để tiếp tục

Chạy lệnh để bảo mật mysql
```
$ sudo mysql_secure_installation
```

Nhấn `Y` để tiếp tục

```
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
```
Chọn 1 và nhấn enter, sau đó điền mật khẩu gồm chữ, số, ky tự đặc biệt
Nhấn `Y` với tất cả các phần còn lại nếu được hỏi

Truy cập mysql
```
$ sudo mysql
```

Tiếp theo
```
mysql> SELECT user,authentication_string,plugin,host FROM mysql.user;
```
Có kết quả sau
```
Output
+------------------+-------------------------------------------+-----------------------+-----------+
| user             | authentication_string                     | plugin                | host      |
+------------------+-------------------------------------------+-----------------------+-----------+
| root             |                                           | auth_socket           | localhost |
| mysql.session    | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| debian-sys-maint | *CC744277A401A7D25BE1CA89AFF17BF607F876FF | mysql_native_password | localhost |
+------------------+-------------------------------------------+-----------------------+-----------+
4 rows in set (0.00 sec)
```
Tiếp theo chạy lệnh sau và thay password thành password của bạn
```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
Chạy lệnh sau để khai báo thay đổi cho máy chủ
```
mysql> FLUSH PRIVILEGES;
```
Exit mysql
```
mysql> exit;
```

Nếu cài mysql bị lỗi, kéo xuống dưới để xem cách gỡ cài đặt và chạy lại

## Bước 3: Cài đặt PHP

Cập nhât và cài đặt php
```
$ sudo apt install php libapache2-mod-php php-mysql
```
Chuyển loại file chạy mặc định `index.php` lên đầu, mở file `dir.conf`
```
$ sudo nano /etc/apache2/mods-enabled/dir.conf
```
Nó sẽ như thế này
```
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```
Chuyển `index.php` lên đầu
```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
Đóng và lưu bằng cách nhấn `CTRL + X` và `Y` sau đó `Enter`
Khởi động lại apache2
```
$ sudo systemctl restart apache2
```
Kiểm tra php đã hoạt động chưa
Chạy lệnh sau để tạo file `info.php`
```
$ sudo nano /var/www/html/info.php
```
Paste nội dung sau vào file
```php
<?php
  phpinfo();
?>
```
Đóng và lưu bằng cách nhấn `CTRL + X` và `Y` sau đó `Enter`
Truy cập `localhost/info.php`

## Bước 4: Cài đặt Phpmyadmin

```
$ sudo apt update
$ sudo apt install phpmyadmin php-mbstring php-gettext
```
Sau đó sẽ hiện ra hộp thoại

<b style="color:red;">Cảnh báo:</b> Khi lời nhắc xuất hiện "apache2" highlight, nhưng không được chọn. Nhấn `SPACE`, `TAB` và sau đó `ENTER` để chọn Apache.
Chọn `yes` khi được hỏi
Cài thêm extension
```
$ sudo phpenmod mbstring
```
Khởi động lại apache2
```
sudo systemctl restart apache2
```
Hoàn thành

## Bonus 1: Tạo thêm tài khoản cho mysql truy cập phpmyadmin

Đăng nhập vào mysql với mật khẩu mysql root tạo bên trên
```
$ mysql -u root -p
```
Tạo user, thay đổi `sammy` và `password` tương ứng với `username` và `password` bạn muốn
```
mysql> CREATE USER 'sammy'@'localhost' IDENTIFIED BY 'password';
```
Cấp quyền cho user vừa tạo, chú ý thay đổi `sammy` thành `username`
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'sammy'@'localhost' WITH GRANT OPTION;
```
Exit mysql
```
mysql> exit;
```
Truy cập `localhost/phpmyadmin`

## Bonus 2: Gỡ cài đặt sạch sẽ mysql nếu cài đặt bị lỗi

Chạy lệnh sau để xem các package mysql đã cài
```
$ sudo dpkg -l | grep mysql
```
Chạy lệnh sau với từng package được liệt kê khi chạy câu lệnh trên
```
$ sudo apt-get --purge autoremove <tên gói lấy được từ bước 1>
```
Chạy lệnh sau để xóa sạch mysql
```
$ sudo apt-get purge mysql-server mysql-client mysql-common mysql-server-core-* mysql-client-core-*
$ sudo rm -rf /etc/mysql /var/lib/mysql
$ sudo apt-get autoremove
$ sudo apt-get autoclean
```

## Bonus 3: Gỡ cài đặt phpmyadmin

```
$ sudo apt-get purge phpmyadmin
```
