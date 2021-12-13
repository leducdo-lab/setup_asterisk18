### Mục lục
[I. Giới thiệu về Asterisk](#Modau)

[II. Hướng dẫn cài đặt trên ubuntu](#caidat1)
- [1. Cập nhật hệ thống](#buoc1)
- [2. Cài đặt Build Dependencies](#buoc2)
- [3. Tải asterisk 18 lts và giải nén](#buoc3)
- [4. Xây dựng và cài đặt asterisk](#buoc4)
- [5. Chạy asterisk service ](#buoc5)

[III. Hướng dẫn cài đặt trên Linux Amazon](#caidat2)
- [1. Thiết lập hệ thống EC2](#buoc1_2)
- [2. Cập nhật hệ thống và thời gian trên hệ thống](#buoc2_2)
- [3. Thêm EPEL](#buoc3_2)
- [4. Cài đặt một số hỗ trợ](#buoc4_2)
- [5. Tải và cài đặt Jansson](#buoc5_2)
- [6. Tải và cài đặt PJSIP](#buoc6_2)
- [7. Tải và cài đặt asterisk](#buoc7_2)

[IV. Config các file sip.conf, extensions.conf, voiceemail.conf](#config)
[V. Tải zoiper 5 và cài đặt](#zoiper5)

====================================================================

<a name="Modau"></a>

## I. Giới thiệu về [Asterisk](https://www.asterisk.org/get-started/) 
`asterisk` là một phần mềm mã nguồn mở, do Mark Spencer viết ra. Với mục đích tạo nên một hệ thống tổng đài cá nhân (PBX-private branch exchange) kết nối tới IP, PSTN. Sử dụng các chuẩn như SIP, MGCP, H323, IAX.

`asterisk version 18 LTS` có hỗ trợ thêm cho các giao thức STIR / SHAKEN để chống giả mạo ID người gọi. Hỗ trợ cả việc gửi tiêu đề và bảo đảm danh tính cho các cuộc gọi đi.

<a name="caidat1"></a>

## II. Hướng dẫn cài đặt trên ubuntu
<a name="buoc1"></a>

### 1. Cập nhật hệ thống
```
 sudo apt update
 sudo apt -y upgrade
```
Khởi động lại hệ thống ubuntu
``` 
 sudo systemctl reboot
```
<a name="buoc2"></a>

### 2. Cài đặt Build Dependencies
```
 sudo apt update
 sudo add-apt-repository universe
 sudo apt -y install git curl wget libnewt-dev libssl-dev libncurses5-dev subversion libsqlite3-dev build-essential libjansson-dev libxml2-dev  uuid-dev
```
<a name="buoc3"></a>

### 3. Tải asterisk 18 lts và giải nén
Tải asterisk:  
```
 cd ~
 sudo wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-18-current.tar.gz
```

Giải nén:
```
 sudo tar xvf asterisk-18-current.tar.gz
```

Tải np3 decoder
```
 cd asterisk-18*/
 sudo contrib/scripts/get_mp3_source.sh
```

Kiểm tra đã tải toàn bộ dependencies:
```
 sudo contrib/scripts/install_prereq install
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
 sudo ./configure
```

Thiết lập tuỳ chọn menu
```
 sudo make menuselect
```

Sử dụng mũi tên để điều hướng và **Enter** để chọn 

Chọn Add-ons như ảnh:

![menuselect](https://computingforgeeks.com/wp-content/uploads/2018/08/install-asterisk-ubuntu-18.04-01-min.png?ezimgfmt=rs:640x197/rscb23/ng:webp/ngcb23)

Chọn các Core sound muốn sử dụng:

![menuselect](https://computingforgeeks.com/wp-content/uploads/2018/08/install-asterisk-ubuntu-18.04-02-min.png?ezimgfmt=rs:640x349/rscb23/ng:webp/ngcb23)

Khi hoàn tất hãy chạy lệnh sau:
```
 sudo make
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
 sudo make install
```

Có thể tuỳ chọn cài đặt thêm:
```
 sudo make progdocs
```

Cuối cùng cài đặt config và samples
```
 sudo make samples
 sudo make config
 sudo ldconfig
```
<a name="buoc4"></a>

### 5. Chạy asterisk service

Tạo người dùng và nhóm người dùng:
```
 sudo groupadd asterisk
 sudo useradd -r -d /var/lib/asterisk -g asterisk asterisk
 sudo usermod -aG audio,dialout asterisk
 sudo chown -R asterisk.asterisk /etc/asterisk
 sudo chown -R asterisk.asterisk /var/{lib,log,spool}/asterisk
 sudo chown -R asterisk.asterisk /usr/lib/asterisk
```

Tạo người dùng mặc định cho asterisk
```
 $ sudo vim /etc/default/asterisk
 (Tìm tới vị trí có hai câu lệnh dưới)
  AST_USER="asterisk"
  AST_GROUP="asterisk"
 $ sudo vim /etc/asterisk/asterisk.conf
 (Tìm tới vị trí có hai câu lệnh dưới)
  runuser = asterisk ; The user to run as.
  rungroup = asterisk ; The group to run as.
```

Chạy lại asterisk:
```
 sudo systemctl restart asterisk
```

Bật asterisk:
```
 sudo systemctl enable asterisk
```
Kiểm tra trạng thái của asterisk
```
 $ systemctl status asterisk
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
 sudo asterisk -rvv

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

<a name="caidat2"></a>

## III. Hướng dẫn cài đặt trên Linux Amazon

<a name="buoc1_2"></a>

### 1. Thiết lập hệ thống EC2

- Tạo 1 instance mới 
  - Nhấn vào Launch Instance
  - Chọn Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type
  - Chọn t2.micro Free -> Nhấn Next
  - Tiếp tục nhấn Next tới màn chọn key
  - Chọn tạo key mới -> nhận được file .pem
  - Cuối cùng nhấn tạo instance
- Cập nhật instance
  - Nhấn vào instance ID
  - Kéo xuống chọn tab securityy
  - Chọn Security group 
  - Kéo xuống chọn Edit Inbound rules
  - Chỉnh Type thành All trafic
- Mở terminal tới thư mục chứa file .pem vừa down
- Quay lại trang instance vừa tạo chọn connect
- Sao chép phần example -> dán vào terminal

<a name="buoc2_2"></a>

### 2. Cập nhật hệ thống và thời gian trên hệ thống

Cập nhật:

```
  sudo yum -y update
```

Khởi động lại 
```
  sudo systemctl reboot
```

Quay lại sử dụng câu lệnh sao chép phần example
Ví dụ:

```
  sudo ssh -i "asterisk18.pem" ec2-user@ec2-3-138-198-148.us-east-2.compute.amazonaws.com
```

Đặt tên lại cho máy chủ:

```
  sudo hostnamectl set-hostname asterisk.example.com
```

Cập nhật lại thời gian:
```
  sudo timedatectl set-timezone Africa/Nairobi
```

Đặt lại SELinux :
```
  sudo setenforce 0
  sudo sed -i 's/\(^SELINUX=\).*/\SELINUX=permissive/' /etc/selinux/config
  sudo nano /etc/selinux/config
  SELINUX=permissive -> SELINUX=disabled
```

<a name="buoc3_2"></a>

### 3. Thêm EPEL

```
  sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
  ARCH=$( /bin/arch )
  sudo subscription-manager repos --enable "codeready-builder-for-rhel-8-${ARCH}-rpms
```

<a name="buoc4_2"></a>

### 4. Cài đặt một số lệnh hỗ trợ

```
sudo yum group -y install "Development Tools"
sudo yum -y install git wget vim  net-tools sqlite-devel psmisc ncurses-devel libtermcap-devel newt-devel libxml2-devel libtiff-devel gtk2-devel libtool libuuid-devel subversion kernel-devel kernel-devel-$(uname -r) crontabs cronie-anacron libedit libedit-devel
```

<a name="buoc5_2"></a>

### 5. Tải và cài đặt Jansson

```
  git clone https://github.com/akheron/jansson.git
  cd jansson
  sudo autoreconf -i
  sudo ./configure --prefix=/usr/
  sudo make
  sudo make install
```

<a name="buoc6_2"></a>

### 6. Tải và cài đặt PJSIP

```
  cd ~
  git clone https://github.com/pjsip/pjproject.git
  cd pjproject
  sudo ./configure CFLAGS="-DNDEBUG -DPJ_HAS_IPV6=1" --prefix=/usr --libdir=/usr/lib64 --enable-shared --disable-video --disable-sound --disable-opencore-amr
  sudo make dep
  sudo make
  sudo make install
  sudo ldconfig
```

<a name="buoc7_2"></a>

### 7. Tải và cài đặt asterisk 
Phần này xem lại mục 3., 4. và 5. của II(#buoc3)

<a name="config"></a>

## IV. Config các file sip.conf, extensions.conf, voiceemail.conf

Đi tới vị trí chưa asterisk:

```
 cd /etc/asterisk
```

Backup dữ liệu:

```
 sudo mv sip.conf sip.conf.backup
 sudo mv extensions.conf extensions.conf.backup
 sudo mv voiceemail.conf voiceemail.conf.backup
```

Tạo file mới (nội dung giống các file bên trên):

```
sudo nano sip.conf
sudo nano extensions.conf
sudo nano voiceemail.conf
```

Đối với deploy cần thêm dòng sau vào sip.conf

```
  externip=3.138.198.148 (Public IPv4 address của Instances)
```

<a name="zoiper5"></a>

## V. Tải zoiper 5 và cài đặt

Zoiper 5 có đầy đủ phiên bản trên PC và mobile

Tải theo đường link bên dưới:
```
 https://www.zoiper.com/en/voip-softphone/download/current
```
- Mobile:
 - Mở ứng dụng chọn 'Continue as a Free user'
 - Chọn 'Settings'
 - Chọn 'Account'
 - Chọn dấu cộng '+'
 - Chọn 'Yes'
 - Chọn Manual configuration
 - Chọn SIP account
 - Nhập các thông tin như ảnh sau:


- PC:
 - Mở ứng dụng chọn 'Continue as a Free user'
 - Chọn biểu tượng bánh răng
 - Chọn Account
 - Nhập thông tin giống ảnh sau:



