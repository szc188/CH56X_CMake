# CMake CH56X 工程

## 环境

+ 系统：Windows11 x86_64

+ Docker环境：Ubuntu:18.04 x86_64

## 配置环境

1. 安装 Docker（OUTSIDE）

2. 下载源代码并保存到特定文件夹（OUTSIDE）

3. 使用docker创建ubuntu的实例并进入实例（OUTSIDE）

    `C:\workspace\CH56X_CMake` 是我的代码在 Windows 上的路径

    ```bash
    docker run -it --rm -v C:\workspace\CH56X_CMake:/root/CH56X_CMake ubuntu:18.04 /bin/bash
    ```

4. 安装依赖（INSIDE）

    ```bash
    apt update -y && apt install -y cmake wget vim make

    cd /root/CH56X_CMake

    # 下载交叉编译器
    wget https://developer.arm.com/-/media/Files/downloads/gnu-rm/10.3-2021.10/gcc-arm-none-eabi-10.3-2021.10-x86_64-linux.tar.bz2

    tar -xvjf gcc-arm-none-eabi-10.3-2021.10-x86_64-linux.tar.bz2
    ```

## 编译代码并下载

1. 编译代码（INSIDE）

    ```bash
    cd /root/CH56X_CMake
    
    mkdir build && cd build

    cmake ..

    make
    ```

2. 下载代码（Windows）（OUTSIDE）

    由于Linux中不能使用 `WCHISPTool`，所以需要在Windows上打开

    下载文件是 `C:\workspace\CH56X_CMake\build\ch563q-test.hex`

3. 下载代码（Linux）

    没有测试过，这里提供多种开源方案

    [librech551](https://github.com/rgwan/librech551)

    [ch552tool](https://github.com/MarsTechHAN/ch552tool)
