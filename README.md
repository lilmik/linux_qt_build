# linux_qt_build
UOS环境下编译安装qt记录

### UOS安装后补充安装的包
```bash
sudo apt install git curl zip unzip tar gcc g++ build-essential ninja-build
```



### UOS编译安装cmake

想要qt配合vcpkg使用，需要使用cmake。vcpkg要求的cmake最低版本为3.15，安装过程发现UOS源中提供的cmake默认版本为3.13，所以需要手动编译安装更高版本的cmake。

cmake源码下载地址：

https://cmake.org/download/

本次编译安装的版本为当前最新的版本2.27.7

```bash
tar -zxvf cmake-3.27.7.tar.gz
cd cmake-3.27.7/

# bootstrap检查可能会报错，一般而言可能是缺少openssl或make，Ubuntu系的系统可以通过apt安装libssl-dev build-sessential两个包。如果没有提示报错信息，则可进入编译环节。
./bootstrap 
make -j8
# 编译完成后安装
sudo make install
# 检查系统cmake版本
cmake --version
```



### UOS编译安装qt6.6.0

qtcreator的编译安装需要依赖qt的版本，5.15.x版的qt最高只能编译8.x版本的qtcreator。本次期望编译最新版本的qtcreator（11.0.3），官方文档中提到最低的qt版本为6.2，所以本次编译6.6.0版的qt。

```bash
tar -xJf qt-everywhere-src-6.6.0.tar.xz
```

或者可以分步骤来解压.tar.xz文件

```bash
xz -d qt-everywhere-src-6.6.0.tar.xz

tar -xvf t-everywhere-src-6.6.0.tar
```

安装必备的的编译依赖

```bash
sudo apt install flex bison gperf libxcb* libgl1-mesa-dev libglu1-mesa-dev libegl1-mesa-dev freeglut3-dev llvm clang libclang-dev
```

为了保持qt源码目录的整洁，可以在qt源码目录的同级位置新建一个用于build的临时目录。

```
mkdir qt-6.6.0-build
cd qt-6.6.0-build/

# 指定编译好的qt安装到/opt/qt/qt-6.6.0目录下，并且编译release版本
./../qt-everywhere-src-6.6.0/configure -release -prefix /opt/qt/qt-6.6.0

```

```bash
#配置完成后会有以下提示

Qt is now configured for building. Just run 'cmake --build . --parallel'

Once everything is built, you must run 'cmake --install .'
Qt will be installed into '/opt/qt/qt-6.6.0'

To configure and build other Qt modules, you can use the following convenience script:
        /opt/qt/qt-6.6.0/bin/qt-configure-module

If reconfiguration fails for some reason, try removing 'CMakeCache.txt' from the build directory
Alternatively, you can add the --fresh flag to your CMake flags.

-- Configuring done (42.5s)
-- Generating done (4.1s)
-- Build files have been written to: /home/lianchao/Downloads/qt-6.6.0-build
```

根据提示使用cmake进行编译

```
cmake --build . --parallel
```

然后就出现了因为gcc版本过低导致编译失败的问题。看官方文档说ubuntu编译通过的情况是使用的gcc11的版本，目前uos只提供到gcc8。查到的gcc编译安装比较复杂，暂时没搞定gcc编译更新的问题。。。暂时搁置高版本qt编译。。。。-_-||

### UOS编译安装qtcreator

qt-6.6.0编译完成后，开始编译qtcreator11.0.3。

```bash
tar -zxvf qt-creator-opensource-src-11.0.3.tar.gz
```



### UOS编译安装qt5.15.11

参照qt6.6.0编译流程以及文档配置qt5.15.11的编译内容

```
tar -xJf qt-everywhere-opensource-src-5.15.11.tar.xz
mkdir qt-5.15.11-build
cd qt-5.15.11-build/

./../qt-everywhere-src-5.15.11/configure -release -prefix /opt/qt/qt-5.15.11 -opensource -nomake tests
make -j8
sudo make install
```



### UOS安装vcpkg的问题

arm版本的linux可能会报错`Environment variable VCPKG_FORCE_SYSTEM_BINARIES must be set on arm, s390x, ppc64le and riscv platforms.`需要在~/.bashrc中添加一部分内容。

```bash
nano ~/.bashrc

# 文件末尾添加以下内容，可以解决上述问题
# vcpkg
export VCPKG_FORCE_SYSTEM_BINARIES=1

# 保存后使之生效

source ~/.bashrc

cd ~
git clone https://github.com/Microsoft/vcpkg.git
./vcpkg/bootstrap-vcpkg.sh

nano ~/.bashrc

# 文件末尾添加以下内容，可以在任意目录下使用vcpkg来管理包的内容
# vcpkg
export PATH="/home/$USER/vcpkg/:$PATH"

source ~/.bashrc
```

