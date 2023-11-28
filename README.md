# HƯỚNG DẪN SETUP CHƯƠNG TRÌNH
## A. Cài đặt máy ảo
## B. Cài đặt môi trường
#### 1. Sử dụng Git:
```bash
sudo apt update
sudo apt install git
```
- Kiểm tra xem đã cài git thành công hay chưa:

    ```bash
    git --version
    ```
- Thiết lập tài khoản Git trên máy ảo:
    ```bash
    git config --global user.name "Tên của bạn"
    git config --global user.email "địa chỉ email của bạn"
    ```

#### 2. **Cài đặt và cấu hình SSH**
**2.1. Cài đặt**
- Cài đặt như sau:
     ```bash
    sudo apt-get update
    ```
    ```bash
    sudo apt-get install openssh-server -y
    ```
    ```bash
    sudo systemctl start sshd
    ```
    ```bash
    sudo systemctl enable ssh
    ```
- Để xác minh cài đặt thành công và SSH đang chạy, hãy sử dụng lệnh:
    ```bash
    sudo service ssh status
    ```
    ![Alt text](./img/readme/image.png)
**2.2. Cấu hình SSH.**
- Mở file cấu hình:
    ```bash
    sudo nano /etc/ssh/sshd_config
    ```
- Cấu hình như sau:
    ```
    Include /etc/ssh/sshd_config.d/*.conf

    Port 22
    ListenAddress 0.0.0.0

    HostKey /etc/ssh/ssh_host_rsa_key
    HostKey /etc/ssh/ssh_host_ecdsa_key
    HostKey /etc/ssh/ssh_host_ed25519_key
    SyslogFacility AUTHPRIV
    PermitRootLogin yes
    PubkeyAuthentication yes
    AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
    # Chophep/Tu choi SSH
    # AlowUsers user1
    # DenyUsers user1
    # Tu choi SFTP
    #Match User user2
    #ForceCommand /bin/false
    #Match User user3
    #ForceCommand internal-sftp

    UsePAM yes
    X11Forwarding yes
    PrintMotd no
    AcceptEnv LANG LC_*
    Subsystem sftp /usr/lib/openssh/sftp-server
    ```

- Để thực thi thay đổi, chạy lệnh:
    ```bash
    sudo systemctl restart sshd.service
    ```
**2.3. Cấu hình tường lửa.**
- Công cụ cấu hình tường lửa mặc định trong Ubuntu là UFW, cấu hình bằng lệnh:
    ```bash
    sudo ufw allow from any to any port 22 proto tcp
    ```

#### 3. Cài đặt mysql
- Cài đặt gói mysql:
    ```bash
    sudo apt install mysql-server -y
    ```
- Kích hoạt:
    ```bash
    sudo systemctl start mysql.service
    sudo systemctl is-active mysql
    ```
- Cài đặt passwork:
    ```bash
    sudo mysql
    ALTER USER root@localhost IDENTIFIED WITH mysql_native_password BY 'admin';
    ```
- Cài bảo mật cho mysql:
    ```bash
    sudo mysql_secure_installation
    ```
    ![Alt text](./img/readme/image-1.png)
    ![Alt text](./img/readme/image-2.png)

#### 4. Cấu hình máy chủ web Apache

- Sử dụng lệnh sau để cài đặt Apache trên máy ảo:
    ```bash
    sudo apt update
    sudo apt install apache2
    ```
- Kiểm tra trạng thái Apache:

    ```bash
    sudo systemctl status apache2
    ```

- Mở cổng truy cập cho Apache:
    ```bash
    sudo ufw allow 80/tcp
    ```

- Kiểm tra Apache bằng trình duyệt web:
    ```
    http://your_server_ip/
    ```
    `your_server_ip`: Địa chỉ IP của máy ảo.


#### 5. Cài đặt PHP
- Cài đặt PHP và các gói liên quan:
   ```bash
   sudo apt update
   sudo apt install php libapache2-mod-php php-mysql
   ```

        `php`: Cài đặt gói chính của PHP.
        `libapache2-mod-php`: Gói này liên kết PHP với Apache.
        `php-mysql`: Gói này cho phép PHP làm việc với cơ sở dữ liệu MySQL.

- Kiểm tra cài đặt PHP:
   ```bash
   php -v
   ```

- Kiểm tra tệp cấu hình:
   ```bash
   ls /etc/apache2/mods-enabled/php*.conf
   ```

- Khởi động lại Apache:
   ```bash
   sudo systemctl restart apache2
   ```

## C. Đưa project lên máy ảo
#### 1. Đưa file lên máy ảo và quản lý source bằng GIT
- Chuyển vào đường dẫn `/var/www` để clone project vào:
    ```bash
    cd ./var/www
    sudo git clone https://github.com/batruong1704/BurningHotel.git
    ```
#### 2. Export file .sql:
```sql
     mysql -u root -p
     CREATE DATABASE burninghotel
     USE burninghotel;
     source /path/to/yourfile.sql;
```
#### 3. Cấu hình Virtual Host
- Phân quyền cho tệp và thư mục:
    ```bash
    sudo chown -R www-data:www-data /var/www/html
    ```

- Khởi động lại Apache:
    ```bash
    sudo systemctl restart apache2
    ```

- Tạo một tệp cấu hình Virtual Host:
    ```bash
    sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/burninghotel.conf
    sudo nano /etc/apache2/sites-available/burninghotel.conf
    ```

- Cấu hình Virtual Host:
     ```apache
     <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        ServerName burninghotel.com
        DocumentRoot /var/www/BurningHotel
         
         ErrorLog ${APACHE_LOG_DIR}/error.log
         CustomLog ${APAC
     HE_LOG_DIR}/access.log combined
     </VirtualHost>
     ```

- Kích hoạt Virtual Host và khởi động lại Apache:
    ```bash
    sudo a2ensite burninghotel.conf
    sudo systemctl restart apache2
    ```
#### 4. Ánh xạ tên miền:

- Mở tệp `/etc/hosts` để chỉnh sửa:
   ```bash
   sudo nano /etc/hosts
   ```

- Thêm các cặp tên miền và địa chỉ IP:
   ```plaintext
   ip_address burninghotel.com
   ```

#### 5. Cấu hình file hosts trên window
- File nằm trên địa chỉ `C:\Windows\System32\drivers\etc`, sau đó thêm ip_address và domain_name vào file và truy cập thử trên trình duyệt.
- Tại file hosts:
    ```
    # Copyright (c) 1993-2009 Microsoft Corp.
    #
    # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
    #
    # This file contains the mappings of IP addresses to host names. Each
    # entry should be kept on an individual line. The IP address should
    # be placed in the first column followed by the corresponding host name.
    # The IP address and the host name should be separated by at least one
    # space.
    #
    # Additionally, comments (such as these) may be inserted on individual
    # lines or following the machine name denoted by a '#' symbol.
    #
    # For example:
    #
    #      102.54.94.97     rhino.acme.com          # source server
    #       38.25.63.10     x.acme.com              # x client host
    192.168.179.134 burninghotel.com
    # localhost name resolution is handled within DNS itself.
    #	127.0.0.1       localhost
    #	::1             localhost
    ```
- Truy cập trên trình duyệt với địa chỉ:
    ```
    http://burninghotel.com/
    ```
    ![Alt text](./img/readme/image-3.png)

===============================================================================================================================================================
# 1. GIT
### 1.1. Nhánh và các vấn đề liên quan tới nhánh:
- **Branch**:
     ```
     git branch `tuychon`
     ```
| Tùy Chọn               | Mô Tả                                                                           |
|-----------------------|---------------------------------------------------------------------------------|
| git branch           | Hiển thị danh sách các nhánh local và đánh dấu nhánh hiện tại bằng một dấu '*' |
| git branch -a        | Hiển thị tất cả các nhánh, bao gồm cả remote branches                            |
| git branch -d <tên>  | Xóa một nhánh local (nếu không có thay đổi chưa được kết hợp)                 |
| git branch -D <tên>  | Xóa một nhánh local mà không cần kiểm tra thay đổi                               |
| git branch -m <tên_cũ> <tên_mới>  | Đổi tên của một nhánh local                                                   |
| git branch -r        | Hiển thị danh sách các nhánh từ xa (remote branches)                             |
| git branch -v        | Hiển thị danh sách các nhánh local với thông tin về commit gần nhất            |
| git branch --set-upstream-to=<remote>/<tên> | Thiết lập theo dõi cho một nhánh local để theo dõi một nhánh từ xa |

- **Checkout:**
     ```
     git checkout `tuychon`
     ```
| Tùy Chọn           | Mô Tả                                                                                     |
|-------------------|-------------------------------------------------------------------------------------------|
| git checkout <tên_nhánh>         | Chuyển nhánh                                                                        |
| git checkout -b <tên_nhánh>      | Tạo một nhánh mới và chuyển sang nhánh mới đó                                              |
| git checkout -- <đường_dẫn_tệp>  | Loại bỏ thay đổi trên tệp đã chỉ định và khôi phục lại phiên bản trước đó của tệp   |
| git checkout -f                  | Đặt lại các thay đổi chưa commit trong thư mục làm việc hiện tại                         |
| git checkout <commit>            | Di chuyển HEAD và thư mục làm việc hiện tại đến commit đã chỉ định                    |

- **Các lệnh xử lý:**
     + Xem lịch sử commit của một nhánh:
        ```
        git log
        ```

     + So sánh nhánh với nhánh khác:
        ```
        git diff main..feature
        ```
 
     + Xem sự khác biệt giữa các thay đổi đã staged và commit:
          ```
          git diff --staged
          ```
     Ví dụ, để so sánh nhánh hiện tại (ví dụ: "main") với một nhánh khác (ví dụ: "feature"), bạn có thể sử dụng:

     + Xem thay đổi trên một commit cụ thể:
        ```
        git show <mã_commit>
        ```
- Xoá tệp ra khởi stage:
     ```
     git reset file1.txt    # Loại bỏ file1.txt khỏi staging area
     git reset              # Loại bỏ tất cả các thay đổi đã staged
     git restore --staged file1.txt  # Loại bỏ file1.txt khỏi staging area
     git restore file1.txt           # Khôi phục file1.txt từ trạng thái đã staged hoặc trạng thái commit trước đó
     ```
- Hoàn tác:
     ```
     git revert <mã_commit>
     ```
# 2. Phân quyền người dùng:
#### 1. `chmod` Change mode dùng để phân quyền hạn cho thư mục hoặc cho file:
###### Ví dụ: Khi gõ lệnh ls -l
![image](https://github.com/batruong1704/SHH_FTP_SFTP/assets/142201301/299af603-7bfd-4396-bd21-1c9f40ffa188)
- Kí tự đầu tiên thể hiện là thư mục hay là file:
   | Kí tự      | Ý nghĩa                                          |
   |------------|--------------------------------------------------|
   | d          | Đây là thư mục `directory`                       |
   | -          | Đây là 1 file                                    |
   | l          | Đây là 1 shortcut                                |

- 3 kí tự tiếp theo chỉ quyền hạn cho nhóm `user`, 3 kí tự bên cạnh là của nhóm `group`, 3 kí tự cuối cùng cho nhóm còn lại `Other`:
   | Kí tự      | Hệ số | Ý nghĩa                                          |
   |------------|-------|--------------------------------------------------|
   | r          | -4    | Quyền đọc                                        |
   | w          | -2    | Quyền viết                                       |
   | x          | -1    | Quyền thực thi                                   |
   |            | -0    | Không được phân quyền                            |
  
###### Lệnh:
   ```
   chmod [<option>] [permissions] file/forder_name
   ```
**`[<option>]`:**
   - `-R`: phân quyền cho tất cả nội dung trong thư mục.
   - `-c`: Hiển thị thông tin khi thay đổi được thực hiện.
   - `-f`: Ngăn chặn các thông báo lỗi.
   - `-v`: Hiển thị chuẩn đoán cho mỗi tệp được xử lý
**`[permissions]`:**
   - Thay với hệ số ở trên, 3 hệ số lần lượt với phân quyền cho đối tượng `user`, `group` và `other`. *Ví dụ:* `chmod -r 755 folder_name`
   - Hoặc có thể thay đổi bằng ký tự ví dụ như `u=rwx, g=rx, o=r` (Hoặc dùng `a` để chỉ tất cả). Có thể thay thế dấu `=` thành `+` giữ nguyên các quyền cũ và cộng thêm 1 quyền, thay thế `-` giữ nguyên các quyền cũ và bỏ bớt 1 quyền.


##### 2. `chowd` Change owner dùng để thay đổi quyền hạn cho folder/file:
   ```
   chown [<options>] [user]:[<group>] file/folder_name
   ```

#### 3. Để cho phép người dùng sử dụng SSH và FTP, hãy đảm bảo rằng người dùng đã được thêm vào các nhóm tương ứng (sudo và ftp):
   ```
   sudo usermod -aG sudo,ftp <tên_người_dùng>
   ```

#### 4. Đảm bảo rằng thư mục người dùng có đủ quyền để truy cập qua SFTP và FTP. Bạn có thể thực hiện điều này bằng cách chỉnh sửa quyền thư mục cụ thể:
   ```
   sudo chown -R <tên_người_dùng>:<tên_người_dùng> /home/<tên_người_dùng>
   ```

#### 5. Group: 
   ```
   sudo groupadd <group_name>
   
   ```
- Kiểm tra các group đã được thêm:
   ```
   cat /etc/group
   ```
- Loại bỏ người dùng ra 1 nhóm:
   ```
   sudo gpasswd -d <user_name> <group_name>
   ```
#### 6. User:
   ``` 
   sudo adduser <user_name>
   ```
- Kiểm tra xem đã thêm thành công hay chưa:
   ```
   cat /etc/passwd
   ```
- Thêm user vào group:
   ```
   sudo usermod -aG <group_name> <user_name>
   ```
- Xoá user:
   ```
   userdel -r <user_name>
   ```

#### 7. Phân quyền cho 1 file:
   ```
   sudo chmod 777 <file_name.txt>
   sudo chown <user_name> <file/folder_name>
   sudo chgrp <group_name> <file/folder_name>
   ```

#### 8. Cách swich người dùng
   ```
   sudo su
   sudo su <username>
   ```
# 3. SFTP trong SSH:
##### Khi cài đặt OpenSSH Server, nó đã có sẵn sftp-server. Giao thức SFTP sử dụng để kết nối, duyệt file, tải file, upload giữa server và máy khách.
=> Thực hiện kết nối:
   ```
   sftp username@idhost
   ```
| Lệnh            | Mô tả                                                      | Ví dụ                                     |
|-----------------|------------------------------------------------------------|-------------------------------------------|
| `ls`            | Liệt kê các tệp và thư mục trên máy từ xa.                 |                                           |
| `cd`            | Di chuyển đến thư mục trên máy từ xa.                      |                                           |
| `df [path]`     | Hiển thị thông tin về không gian đĩa trên máy từ xa.       |                                           |
| `pwd`           | Hiển thị đường dẫn hiện tại trên máy từ xa.                |                                           |
| `mkdir`         | Tạo một thư mục trên máy từ xa.                            |                                           |
| `rename`        | Đổi tên hoặc di chuyển tệp/thư mục trên máy từ xa.         | `rename <tencu> <tenmoi>`                 |
| `rm`            | Xóa tệp hoặc thư mục trên máy từ xa.                       |                                           |
| `get`           | Tải tệp từ máy từ xa về máy cục bộ.                        | `get remote_file [local_file]`            |
| `put`           | Tải tệp từ máy cục bộ lên máy từ xa.                       | `put local_file [remote_file]`            |
| `exit` hoặc `quit` | Thoát khỏi phiên SFTP.                                  | `exit` hoặc `quit`                        |

###### => Thêm tiền tố l đứng trước để thao tác trên local*.
"# Website_BurningHotel" 
