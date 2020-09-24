# Hướng dẫn cài Apache, MySQL, PHP (LAMP) stack, Phpmyadmin, CURL trên Ubuntu 16.04, 18.04, 20.04

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

<img src="http://assets.digitalocean.com/articles/how-to-install-lamp-ubuntu-18/small_apache_default_1804.png" alt="localhost" width="300"/>

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

Có 2 tùy chọn cài đặt mysql: 1 là không yêu cầu mật khẩu, 2 là có mật khẩu.

Bài viết này sử dụng tùy chọn 2 là "Có mật khẩu" để không phải chỉnh trong các file khác sẽ nhanh hơn và bảo mật hơn.

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
Chọn 0 và nhấn enter, sau đó điền mật khẩu gồm 8 ký tự trở lên.
(Có thể chọn 1 hoặc 2 nếu bạn muốn mật khẩu gồm ký tự chữ số,... để bảo mật hơn - khuyên chọn 0)
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
<b style="color:red;">!!!Lưu ý: đọc kỹ dòng bên dưới</b><br/>

Tiếp theo chạy lệnh sau và thay chỗ <mật khẩu của bạn> thành mật khẩu mà bạn muốn dùng để đăng nhập vào mysql cho tài khoản "root"
```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '<mật khẩu của bạn>';
```
Chạy lệnh sau để khai báo thay đổi mật khẩu cho máy chủ
```
mysql> FLUSH PRIVILEGES;
```
Kiểm tra lại bằng cách chạy lệnh
```
mysql> SELECT user,authentication_string,plugin,host FROM mysql.user;
```
Dòng `root` tương tự bảng sau là thành công
```
+------------------+-------------------------------------------+-----------------------+-----------+
| user             | authentication_string                     | plugin                | host      |
+------------------+-------------------------------------------+-----------------------+-----------+
| root             | *DE06E242B88EFB1FE4B5083587C260BACB2A6158 | mysql_native_password | localhost |
| mysql.session    | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE | mysql_native_password | localhost |
| debian-sys-maint | *8486437DE5F65ADC4A4B001CA591363B64746D4C | mysql_native_password | localhost |
| phpmyadmin       | *5FD2B7524254B7F81B32873B1EA6D681503A5CA9 | mysql_native_password | localhost |
+------------------+-------------------------------------------+-----------------------+-----------+
5 rows in set (0.00 sec)
```
Exit mysql
```
mysql> exit;
```

Nếu cài mysql bị lỗi, kéo xuống dưới để xem cách gỡ cài đặt và cài lại

## Bước 3: Cài đặt PHP

Cập nhât và cài đặt php
```
$ sudo apt install php libapache2-mod-php php-mysql
```
Chuyển loại file chạy mặc định `index.php` lên đầu, mở file `dir.conf`
```
$ sudo nano /etc/apache2/mods-enabled/dir.conf
```
Khi chạy lệnh trên nó sẽ như thế này
```
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```
Chuyển `index.php` vào giữa "index.html" và sau "DirectoryIndex" như dưới đây
```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
Lưu lại và đóng bằng cách nhấn `CTRL + X` và `Y` sau đó `Enter`
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

<b style="color:red;">Cảnh báo:</b> Khi hộp thoại xuất hiện "apache2" được highlight, nhưng nó chưa được chọn. Nhấn `SPACE`, `TAB` và sau đó `ENTER` để chọn Apache.
Chọn `yes` khi được hỏi
Chọn `yes` khi được hỏi có sử dụng `dbconfig-common` để thiết lập cơ sở dữ liệu hay không.

Sau đó, bạn sẽ được yêu cầu chọn và xác nhận mật khẩu ứng dụng MySQL cho phpMyAdmin

<b>BẮT ĐẦU LƯU Ý</b><br>
Lưu ý : Giả sử bạn bị lỗi như hình bên dưới <b>(nếu không lỗi vui lòng bỏ qua lưu ý này)</b>, 
bạn có thể đã bật plugin Xác thực mật khẩu, việc kích hoạt thành phần này sẽ gây ra lỗi khi bạn cố gắng đặt mật khẩu cho người dùng phpmyadmin:

<img src="https://assets.digitalocean.com/articles/phpmyadmin_2004/pma_vpp_error.png" alt="error_phpmyadmin" width="300">

Để giải quyết vấn đề này, hãy chọn tùy chọn `abort` để dừng quá trình cài đặt.

Chạy lệnh dưới để gỡ phpmyadmin:

```
sudo apt purge phpmyadmin
```
```
sudo apt autoremove
```

Sau đó, mở lời nhắc MySQL của bạn:

```
sudo mysql
```

hoặc nếu bạn đã thực hiện tạo mật khẩu cho account root ở trên:

```
sudo mysql -u root -p
```

Khi truy cập vào mysql chạy lệnh sau để tắt xác thực mật khẩu:
```
mysql> UNINSTALL COMPONENT "file://component_validate_password";
```
```
mysql> exit
```

Cài đặt lại phpmyadmin:
```
sudo apt install phpmyadmin
```

Khi phpMyAdmin được cài đặt, bạn có thể mở lại lời nhắc MySQL bằng `sudo mysql` hoặc `mysql -u root -p`
và sau đó chạy lệnh sau để kích hoạt lại thành phần Xác thực Mật khẩu:
```
mysql> INSTALL COMPONENT "file://component_validate_password";

```
<b>KẾT THÚC LƯU Ý</b><br>

Cài thêm extension
```
$ sudo phpenmod mbstring
```
Khởi động lại apache2
```
$ sudo systemctl restart apache2
```
Chạy `localhost/phpmyadmin` để kiểm tra, sẽ có yêu cầu đăng nhập như sau, dùng tài khoản `root` và `password` để đăng nhập

<img src="https://assets.digitalocean.com/articles/phpmyadmin_1804/phpmyadmin_sammy_login_small.png" alt="phpmyadmin" width="300"/>

## Bước 4: Cài Sqlite3 (tùy chọn - cài cho chắc :3)

```
$ sudo apt-get install php-sqlite3
```

## Bước 5: Cấu hình mod_rewrite cho apache2

Chạy lệnh
```
$ sudo a2enmod rewrite
```
Sau đó mở file `000-default.conf`
```
$ sudo nano /etc/apache2/sites-enabled/000-default.conf
```
Tìm dòng `<VirtualHost *:80>` và thêm đoạn code sau vào
```
<Directory /var/www/html>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
</Directory>
```
File của bạn bây giờ phải giống như sau. Hãy chắc chắn rằng tất cả các khối được thụt lề đúng cách.
```
<VirtualHost *:80>
    <Directory /var/www/html>

        . . .

    </Directory>

    . . .
</VirtualHost>
```
Khởi động lại apache2
```
$ sudo service apache2 restart
```
Tạo file `.htaccess`
```
$ sudo nano /var/www/html/.htaccess
```
Thêm dòng này vào file
```
RewriteEngine on
```
Set quyền cho file
```
sudo chmod 644 /var/www/html/.htaccess
```

Sau đó chạy lệnh sau để cấp quyền cho thư mục
```
$ sudo chmod -R 777 /var/www
```
Đường dẫn chứa các file và thư mục bạn code php sẽ phải nằm trong đường dẫn `/var/www/html/`

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
