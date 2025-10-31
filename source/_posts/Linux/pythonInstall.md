---
title: CentOS7中Python安装
date: 2025-10-31 08:59:14
---
## 检查gcc版本
CentOS7默认GCC版本为4.8.5，如果要安装3.8以上的版本的Python，Python源码包是需要高版本的GCC支持的，建议安装GCC 7及以上版本

```bash
gcc --version
```

## 升级GCC
在root目录操作即可
```bash
wget https://mirrors.aliyun.com/gnu/gcc/gcc-10.1.0/gcc-10.1.0.tar.gz
tar -zxvf gcc-10.1.0.tar.gz
cd gcc-10.1.0
mkdir build
cd build
../configure --prefix=/usr/local/gcc-10.1.0/ --enable-checking=release --enable-languages=c,c++ --disable-multilib
```
若遇到依赖包缺失问题,例如
```bash
configure: error: Building GCC requires GMP 4.2+, MPFR 3.1.0+ and MPC 0.8.0+.
```
则可以根据提示编译安装对应的依赖包，以下步骤顺序执行
### gmp安装
```bash
wget https://mirrors.aliyun.com/gnu/gmp/gmp-5.0.1.tar.bz2
tar -vxf gmp-5.0.1.tar.bz2
cd gmp-5.0.1
./configure --prefix=/usr/local/gmp-5.0.1/
make && make install
```
### mpfr安装
```bash
wget https://mirrors.aliyun.com/gnu/mpfr/mpfr-3.1.5.tar.xz
cd mpfr-3.1.5
./configure --prefix=/usr/local/mpfr-3.1.5/ --with-gmp=/usr/local/gmp-5.0.1/
make && make install
```
### mpc安装
```bash
wget https://mirrors.aliyun.com/gnu/mpc/mpc-1.0.1.tar.gz
tar -vxf mpc-1.0.1.tar.gz
cd mpc-1.0.1
./configure --prefix=/usr/local/mpc-1.0.1/ --with-gmp=/usr/local/gmp-5.0.1/ --with-mpfr=/usr/local/mpfr-3.1.5/
make && make install
```
### 继续安装GCC
依赖包安装完成后，继续GCC的安装
```bash
cd /root/gcc-10.1.0/build
../configure --prefix=/usr/local/gcc-10.1.0/ --enable-checking=release --enable-languages=c,c++ --disable-multilib --with-gmp=/usr/local/gmp-5.0.1 --with-mpfr=/usr/local/mpfr-3.1.5 --with-mpc=/usr/local/mpc-1.0.1
```
执行编译安装命令，-j4表示使用4个CPU核心进行编译，可以根据实际情况调整
```bash
make -j4 && make install
```
### 配置环境变量
- 备份历史版本
```bash
mv /usr/bin/gcc /usr/bin/gcc485
mv /usr/bin/g++ /usr/bin/g++485
mv /usr/bin/c++ /usr/bin/c++485
mv /usr/bin/cc /usr/bin/cc485
```
- 建立新版本的软连接
```bash
ln -s /usr/local/gcc-10.1.0/bin/gcc /usr/bin/gcc
ln -s /usr/local/gcc-10.1.0/bin/g++ /usr/bin/g++
ln -s /usr/local/gcc-10.1.0/bin/c++ /usr/bin/c++
ln -s /usr/local/gcc-10.1.0/bin/gcc /usr/bin/cc
```

- 备份历史版本的库文件
```bash
mv /usr/lib64/libstdc++.so.6 /usr/lib64/libstdc++.so.6.bak
```
- 建立新版本库文件的软连接
```bash
ln -s /usr/local/gcc-10.1.0/lib64/libstdc++.so.6.0.28 /usr/lib64/libstdc++.so.6
```
### 检查GCC版本
```bash
gcc --version
```

## 安装Python
### Python安装目录
```bash
cd /usr/local/src
```
### 下载Python源码包
```bash
wget https://www.python.org/ftp/python/3.12.4/Python-3.12.4.tgz
```
### 编译安装
```bash
tar -xvzf Python-3.12.4.tgz
cd Python-3.12.4
./configure --prefix=/usr/local/python3.12.4 --enable-optimizations --prefix=/usr/local/python3.12.4 --with-openssl=/usr/include/openssl
```
参数说明:
  - --enable-optimizations: 启用Python的优化选项，提高性能
  - --with-openssl: 指定OpenSSL库的位置，确保Python支持SSL
  - --prefix: 指定Python的安装路径


执行编译安装命令，-j4表示使用4个CPU核心进行编译，可以根据实际情况调整；altinstall用于防止覆盖系统默认的python版本
```bash
make -j4 && make altinstall
```

### 配置环境变量
```bash
ln -s /usr/local/python3.12/bin/python3.12 /usr/bin/python3.12 # 创建python3.12的软连接
ln -s /usr/local/python3.12/bin/pip3.12 /usr/bin/pip3.12 # 创建pip3.12的软连接
```

### 检查Python版本
```bash
python3.12 --version # python版本
```
```bash
pip3.12 --version # pip版本
```