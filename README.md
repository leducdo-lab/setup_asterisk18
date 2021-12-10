### Mục lục
[I. Giới thiệu về Asterisk](#Modau)

[II. Hướng dẫn cài đặt trên ubuntu](#caidat)
- [1. Cập nhật hệ thống](#buoc1)
- [2. Cài đặt Build Dependencies](#buoc2)
- [3. Tải asterisk 18 lts và giải nén](#buoc3)
- [4. Xây dựng và cài đặt asterisk](#buoc4)
- [5. Chạy asterisk service ](#buoc5)

====================================================================

<a name="Modau"></a>

## I. Giới thiệu về [Asterisk](https://www.asterisk.org/get-started/) 
`asterisk` là một phần mềm mã nguồn mở, do Mark Spencer viết ra. Với mục đích tạo nên một hệ thống tổng đài cá nhân (PBX-private branch exchange) kết nối tới IP, PSTN. Sử dụng các chuẩn như SIP, MGCP, H323, IAX.

`asterisk version 18 LTS` có hỗ trợ thêm cho các giao thức STIR / SHAKEN để chống giả mạo ID người gọi. Hỗ trợ cả việc gửi tiêu đề và bảo đảm danh tính cho các cuộc gọi đi.

<a name="caidat"></a>

## II. Hướng dẫn cài đặt trên ubuntu
<a name="buoc1"></a>

### 1. Cập nhật hệ thống
```
# sudo apt update
# sudo apt -y upgrade
```
Khởi động lại hệ thống ubuntu
``` 
# sudo systemctl reboot
```
<a name="buoc2"></a>

### 2. Cài đặt Build Dependencies
```
# sudo apt update
# sudo add-apt-repository universe
# sudo apt -y install git curl wget libnewt-dev libssl-dev libncurses5-dev subversion libsqlite3-dev build-essential libjansson-dev libxml2-dev  uuid-dev
```
<a name="buoc3"></a>

### 3. Tải asterisk 18 lts và giải nén
Tải asterisk:  
```
# cd ~
# wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-18-current.tar.gz
```

Giải nén:
```
# tar xvf asterisk-18-current.tar.gz
```

Tải np3 decoder
```
# cd asterisk-18*/
# contrib/scripts/get_mp3_source.sh
```

Kiểm tra đã tải toàn bộ dependencies:
```
# sudo contrib/scripts/install_prereq install
```

Kết quả:
```
#############################################
## install completed successfully
#############################################
```
<a name="buoc4"></a>

### 4. Xây dựng và cài đặt asterisk

Chạy lệnh kiểm tra cấu hình
```
# sudo ./configure
```

Thiết lập tuỳ chọn menu
```
# sudo make menuselect
```

Sử dụng mũi tên để điều hướng và **Enter** để chọn 

Chọn Add-ons như ảnh:

![menuselect](https://computingforgeeks.com/wp-content/uploads/2018/08/install-asterisk-ubuntu-18.04-01-min.png?ezimgfmt=rs:640x197/rscb23/ng:webp/ngcb23)

Chọn các Core sound muốn sử dụng:

![menuselect](https://computingforgeeks.com/wp-content/uploads/2018/08/install-asterisk-ubuntu-18.04-02-min.png?ezimgfmt=rs:640x349/rscb23/ng:webp/ngcb23)

Khi hoàn tất hãy chạy lệnh sau:
```
# sudo make
```

Kết quả thành công:

```
+--------- Asterisk Build Complete ---------+
 + Asterisk has successfully been built, and +
 + can be installed by running:              +
 +                                           +
 +                make install               +
 +-------------------------------------------+
```

Lệnh chạy để cài đặt:
```
# sudo make install
```

Có thể tuỳ chọn cài đặt thêm:
```
# sudo make progdocs
```

Cuối cùng cài đặt config và samples
```
# sudo make samples
# sudo make config
# sudo ldconfig
```
<a name="buoc4"></a>

### 5. Chạy asterisk service

Tạo người dùng và nhóm người dùng:
```
# sudo groupadd asterisk
# sudo useradd -r -d /var/lib/asterisk -g asterisk asterisk
# sudo usermod -aG audio,dialout asterisk
# sudo chown -R asterisk.asterisk /etc/asterisk
# sudo chown -R asterisk.asterisk /var/{lib,log,spool}/asterisk
# sudo chown -R asterisk.asterisk /usr/lib/asterisk
```

Tạo người dùng mặc định cho asterisk
```
# $ sudo vim /etc/default/asterisk
# AST_USER="asterisk"
# AST_GROUP="asterisk"
# $ sudo vim /etc/asterisk/asterisk.conf
# runuser = asterisk ; The user to run as.
# rungroup = asterisk ; The group to run as.
```

Chạy lại asterisk:
```
# sudo systemctl restart asterisk
```

Bật asterisk:
```
# sudo systemctl enable asterisk
```
Kiểm tra trạng thái của asterisk
```
# $ systemctl status asterisk
● asterisk.service - LSB: Asterisk PBX
     Loaded: loaded (/etc/init.d/asterisk; generated)
     Active: active (running) since Fri 2020-08-14 12:04:41 CEST; 9s ago
       Docs: man:systemd-sysv-generator(8)
      Tasks: 82 (limit: 4567)
     Memory: 44.6M
     CGroup: /system.slice/asterisk.service
             └─54142 /usr/sbin/asterisk -U asterisk -G asterisk
```

Kiểm tra kết nối:
```
$ sudo asterisk -rvv

Asterisk 18.1.1, Copyright (C) 1999 - 2018, Digium, Inc. and others.
 Created by Mark Spencer markster@digium.com
 Asterisk comes with ABSOLUTELY NO WARRANTY; type 'core show warranty' for details.
 This is free software, with components licensed under the GNU General Public
 License version 2 and other licenses; you are welcome to redistribute it under
 certain conditions. Type 'core show license' for details.
 Running as user 'asterisk'
 Running under group 'asterisk'
 Connected to Asterisk 18.1.1 currently running on asterisk (pid = 107650)
 asterisk*CLI>
```

Config các file sip.conf, extensions.conf, voiceemail.conf




