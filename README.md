**ANAERNET-s-openwrt**
- 基于[ Lean 源码](https://github.com/coolsnowwolf/lede)编译的 OpenWrt 固件,目前仅编译和发布X86平台的固件,如有需要请参考下面的教程自行DIY需要的固件    
- 添加了[ kenzok8 ](https://github.com/kenzok8/openwrt-packages)的软件源
- 固件默认管理地址：`192.168.5.1` 默认用户：`root` 默认密码：`password`  
- 项目编译的固件插件为最新版本，最新版插件可能有 BUG  
- 第一次使用请采用全新安装，避免出现升级失败以及其他一些可能出现的玄学 BUG  

自行编译方式：  
1. 首先装好 Linux 系统，推荐 Debian 11 或 Ubuntu LTS(俺使用的是最新的Ubuntu 22.04 LTS)  
2. 安装依赖  
   ```bash
   sudo apt update -y
   sudo apt full-upgrade -y
   sudo apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential \
   bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
   git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libfuse-dev libglib2.0-dev libgmp3-dev \
   libltdl-dev libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libpython3-dev libreadline-dev \
   libssl-dev libtool lrzsz mkisofs msmtp ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 \
   python3-pyelftools python3-setuptools qemu-utils rsync scons squashfs-tools subversion swig texinfo \
   uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev
   ```
3. 下载源代码，更新 feeds 并选择配置

   ```bash
   git clone https://github.com/
   cd lede
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

4. 下载 dl 库，编译固件
（-j 后面是线程数，第一次编译推荐用单线程）

   ```bash
   make download -j8
   make V=s -j1
   ```
5. 二次编译：

    ```bash
    cd lede
    git pull
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    make defconfig
    make download -j8
    make V=s -j$(nproc)
    ```

6. 如果需要重新配置：

    ```bash
    rm -rf .config
    make menuconfig
    make V=s -j$(nproc)
    ```

编译完成后文件的输出路径为：bin/targets  

一些小tips：  
①编译的插件较多时，建议修改下面两项的默认大小，防止出现空间不足的问题  
Target Images ---> (16) Kernel partition size (in MB)           #默认是 (16) 俺修改的值为 (256)  
Target Images ---> (160) Root filesystem partition size (in MB) #默认是 (160) 俺修改的值为 (4096)  
②源码内的argon主题不是很美观，建议使用以下的方法对argon主题进行替换
```
cd lede/package/lean
rm -rf luci-theme-argon
git clone -b 18.06 https://github.com/jerrykuku/luci-theme-argon.git luci-theme-argon
rm -rf luci-app-argon-config
git clone -b 18.06 https://github.com/jerrykuku/luci-app-argon-config.git luci-app-argon-config
```
