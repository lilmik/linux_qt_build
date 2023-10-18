# linux_qt_build
UOS环境下编译安装qt记录

### UOS安装后补充安装的包
```bash
sudo apt install git curl zip unzip tar
```

想要qt配合vcpkg使用，需要使用cmake。vcpkg要求的cmake最低版本为3.15，安装过程发现UOS源中提供的cmake默认版本为3.13，所以需要手动编译安装更高版本的cmake。
