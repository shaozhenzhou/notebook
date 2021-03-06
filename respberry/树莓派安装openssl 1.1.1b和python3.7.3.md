### 树莓派安装openssl 1.1.1b
- 切换到root用户
```
sudo passwd root #修改密码
su
```
- 设置单用户环境变量
```
vim ~/.bashrc   #末尾增加以下内容：
LD_LIBRARY_PATH=/usr/local/ssl/lib
export LD_LIBRARY_PATH
source .bashrc  #使设置生效
```

- 下载OpenSSL源码包 https://www.openssl.org/source/
```
cd /home/pi/Downloads
wget https://www.openssl.org/source/openssl-1.1.1b.tar.gz
```
- 安装相关组件
```
apt-get install build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev
```
- 安装SSL依赖
```
apt-get install libssl-dev
```
- 安装openssl
```
mkdir /usr/local/ssl
cd /home/pi/Downloads
tar zxvf openssl-1.1.1b.tar.gz -C /usr/local/ssl && cd  /usr/local/ssl/openssl-1.1.1b && ./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl --shared && make && make install
mv /usr/bin/openssl  /usr/bin/openssl.old
ln -s /usr/local/ssl/bin/openssl /usr/bin/openssl
cp /usr/local/ssl/lib/libcrypto.so.1.1 /usr/local/lib64/
cp /usr/local/ssl/lib/libssl.so.1.1 /usr/local/lib64/
ln -s  /usr/local/lib64/libssl.so.1.1 /usr/local/lib64/libssl.so
```
- 修改动态库搜索路径
在/etc/ld.so.conf文件中写入openssl库文件的搜索路径
```
echo "/usr/local/lib64" >> /etc/ld.so.conf
```
运行ldconfig -v 使修改后的/etc/ld.so.conf生效
```
ldconfig -v
```
如果不是root用户，没有权限，则用sudo
```
sudo vim /etc/ld.so.conf
sudo ldconfig -v
```
查看openssl版本
```
openssl version
#OpenSSL 1.1.1b  26 Feb 2019
openssl
OpenSSL> version -a
OpenSSL 1.1.1b  26 Feb 2019
built on: Sun Apr 21 10:31:11 2019 UTC
platform: linux-armv4
options:  bn(64,32) rc4(char) des(long) idea(int) blowfish(ptr) 
compiler: gcc -fPIC -pthread  -march=armv7-a -Wa,--noexecstack -Wall -O3 -DOPENSSL_USE_NODELETE -DOPENSSL_PIC -DOPENSSL_CPUID_OBJ -DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_GF2m -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DKECCAK1600_ASM -DAES_ASM -DBSAES_ASM -DGHASH_ASM -DECP_NISTZ256_ASM -DPOLY1305_ASM -DNDEBUG
OPENSSLDIR: "/usr/local/ssl"
ENGINESDIR: "/usr/local/ssl/lib/engines-1.1"
Seeding source: os-specific
```
### 安装python3.7.3
- 下载python3.7.3源码包 https://www.python.org/ftp/python/
```
cd /home/pi/Downloads
wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tgz
```
- 解压缩
```
tar zxvf Python-3.7.3.tgz
```
- 添加ssl支持
修改Modules/Setup.dist，将以下相应行的注释去除
-- 注意：如果不是第一次安装，Setup文件生成后不会被覆盖
```cat >> /home/pi/Downloads/Python-3.7.3/Modules/Setup.dist <<"EOF"
_socket socketmodule.c
 
SSL=/usr/local/ssl
_ssl _ssl.c \
        -DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
        -L$(SSL)/lib -lssl -lcrypto
EOF
```
- 编译、安装
```
./configure --prefix=/usr/local/python --with-openssl=/usr/local/ssl --with-openssl=/usr/local/ssl && make && make install
```
- 编译过程中注意观察是否设置正确
```
checking for openssl/ssl.h in /usr/local/openssl... yes
checking whether compiling and linking against OpenSSL works... yes
checking for X509_VERIFY_PARAM_set1_host in libssl... yes
checking for --with-ssl-default-suites... python
```
- 设置路径
```
vim ~/.bashrc
LD_LIBRARY_PATH=/usr/local/ssl/lib
LD_LIBRARY_PATH=/usr/local/ssl/lib
export LD_LIBRARY_PATH
export PATH
source .bashrc  #使设置生效
```
- 检查pip安装
```
pip --version
pip3 --version
```
- 查看已安装包
```
pip list
Package    Version
---------- -------
pip        19.0.3 
setuptools 41.0.0
```
- 删除lsb_release
如遇到错误：
```subprocess.CalledProcessError: Command '('lsb_release', '-a')' returned non-zero exit status 1.
```
则需要将lsb_release移除
```
sudo mv /usr/bin/lsb_release /usr/bin/lsb_release.bak
```
- 更新pip3版本
```
pip install --upgrade pip   #或者：python pip3 install --upgrade pip
```

### 巨大的坑
- 编译python3.7.3时，如使用sudo，并出现以下错误，则是由于sudo的环境变量问题：
```
python: /usr/lib/arm-linux-gnueabihf/libssl.so.1.1: version `OPENSSL_1_1_1' not found 
```
```
说明：sudo运行时，会默认重置环境变量为安全的环境变量，也即，但前设置的变量都会失效，只有少数配置文件中指定的环境变量能保存下来。sudo的配置文件是 /etc/sudoers 需要root权限才能读取。
```
- 解决办法有两个
- 1、切换到root
设置单用户环境变量
```
vim ~/.bashrc
```
末尾增加以下内容：
```
LD_LIBRARY_PATH=/usr/local/ssl/lib
export LD_LIBRARY_PATH
source .bashrc  #使设置生效
```
- 2、sudo -E
加上-E选项后，用户可以在sudo执行时保留当前用户已存在的环境变量，不会被sudo重置

- 检查是否生效
```
openssl version
```

### 参考文献
https://www.imooc.com/article/34764
https://blog.csdn.net/lu_yonggang/article/details/62041422
https://blog.csdn.net/yuezhuo_752/article/details/84140168
https://blog.csdn.net/qq_38154948/article/details/83988948
https://blog.csdn.net/wangeen/article/details/8159500
https://blog.csdn.net/little_stupid_child/article/details/82747227
https://blog.csdn.net/jaket5219999/article/details/83687848

