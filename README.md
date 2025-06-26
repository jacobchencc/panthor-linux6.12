# panthor-linux6.12
## Mesa 开源驱动依赖说明

要求解释构建 Mesa 25.1.4（特别是 Panfrost 驱动）所涉及的依赖项和步骤，以下是详细说明：

提供的 `README.md` 概述了使用 Panfrost 驱动构建 Mesa 25.1.4 的步骤。它涉及安装各种库、构建工具、SPIR-V 组件，然后编译 Mesa 本身。

以下是命令及其目的的解释：

### 1. Mesa 25.1.3（初始依赖项 - 尽管最终目标 Mesa 版本是 25.1.4，根据 `git checkout` 命令）

sudo apt update: 此命令更新软件包列表，以便进行升级和新软件包安装。在安装任何软件之前，这是确保您能够访问最新软件包信息的标准第一步.

sudo apt install byacc flex libxcb-randr0-dev libxcb-glx0-dev libxcb-present-dev libx11-xcb-dev libxcb-dri3-dev libxshmfence-dev libxxf86vm-dev python3-pkg-resources python3-pip python3-mako python3-yaml  
此命令安装一系列开发库和 Python 工具。这些是像 Mesa 这样与 X Window System 和各种图形功能交互的项目常见的构建依赖项：  
- byacc, flex: 这些是解析器和词法分析器生成器，常用于编译源代码。
- libxcb-randr0-dev, libxcb-glx0-dev, libxcb-present-dev, libx11-xcb-dev, libxcb-dri3-dev:  这些是 XCB（X-protocol C-language Binding）库的开发文件。XCB 提供与 X Window System 的接口，这些特定的库与 RandR（Resize and Rotate 扩展）、GLX（OpenGL Extension to the X Window System）、present、核心 X11 功能和 DRI3（Direct Rendering Infrastructure 3）相关。Mesa 作为图形栈，严重依赖这些库进行显示和渲染。  
- libxshmfence-dev: 此库提供共享内存栅栏，用于进程间访问共享内存时的同步。这在图形领域对于显示服务器和图形驱动之间的有效通信至关重要。
- libxxf86vm-dev: 这提供了 XFree86-VidMode 扩展的开发文件，该扩展允许应用程序更改显示器的视频模式。
- python3-pkg-resources, python3-pip, python3-mako, python3-yaml: 这些是 Python 3 工具和库。Python 经常用于 Mesa 等大型项目的构建系统中，用于各种脚本、配置和代码生成任务。pip 是 Python 的包安装程序，mako 是一个模板库，yaml 用于处理 YAML 数据序列化。

### 2. Meson 构建系统
sudo apt install setup-tools: 安装 setup-tools，它是一组对 distutils（标准 Python 分发工具）的增强，为构建和分发 Python 包提供了更好的支持。  
sudo wget https://github.com/mesonbuild/meson/releases/download/1.8.2/meson-1.8.2.tar.gz: 下载 Meson 构建系统 1.8.2 版。Meson 是 Mesa 使用的快速且用户友好的构建系统。  
tar -xf meson-1.8.2.tar.gz: 解压下载的 Meson 归档文件.  
sudo python3 setup.py install: 使用其 setup.py 脚本在系统范围内安装 Meson.

### 3. LLVM (Low Level Virtual Machine)
sudo apt install lsb-release wget software-properties-common gnupg: 安装添加和管理外部软件仓库所需的实用程序，特别是针对 LLVM。
  - lsb-release: 识别 Linux Standard Base 和发行版的实用程序。
  - wget: 使用 HTTP、HTTPS 和 FTP 检索文件的命令行实用程序。
  - software-properties-common: 提供 add-apt-repository，用于添加新的 APT 仓库。
  - gnupg: GNU Privacy Guard，用于验证软件包和仓库。

wget https://apt.llvm.org/llvm.sh: 从官方 LLVM APT 仓库下载脚本。此脚本自动化将 LLVM 仓库添加到系统并安装 LLVM 组件的过程。  
chmod +x llvm.sh: 使下载的脚本可执行。
sudo ./llvm.sh 19 all: 执行 LLVM 安装脚本。19 指定 LLVM 版本（LLVM 19），all 通常表示安装该版本的所有相关组件（如 clang、lld 等）。LLVM 是一系列模块化和可重用的编译器和工具链技术，Mesa（尤其是其着色器编译器）通常依赖它进行即时 (JIT) 编译和其他优化。
### 4. SPIRV-Headers
git clone https://github.com/KhronosGroup/SPIRV-Headers.git  
克隆官方 SPIRV-Headers 仓库。SPIR-V（Standard Portable Intermediate Representation）是一种用于并行计算和图形的标准中间语言。此仓库包含定义 SPIR-V 指令集的头文件。  

sudo cmake -S SPIRV-Headers -B build -DCMAKE_INSTALL_PREFIX=/usr/local  
使用 CMake 配置 SPIRV-Headers 的构建。
sudo cmake --build build --target install: 构建并安装 SPIRV-Headers。
  - -S SPIRV-Headers: 指定源目录。
  - -B build: 指定构建目录。
  - -DCMAKE_INSTALL_PREFIX=/usr/local: 将安装前缀设置为 /usr/local，这是从源代码手动编译软件的常见位置  


### 5. SPIRV-LLVM-Translator

git clone -b llvm_release_190 https://github.com/KhronosGroup/SPIRV-LLVM-Translator.git: 克隆 SPIRV-LLVM-Translator 仓库，并专门检出 llvm_release_190 分支。此项目将 SPIR-V 转换为 LLVM 中间表示 (IR)，从而允许 SPIR-V 着色器由 LLVM 编译和优化。  
cd SPIRV-LLVM-Translator: 将当前目录更改为克隆的仓库。  
mkdir build && cd build: 创建一个 build 目录并进入该目录。  
cmake .. -DLLVM_DIR=/usr/lib/llvm-19/lib/cmake/llvm -DCMAKE_INSTALL_PREFIX=/usr/local: 配置 SPIRV-LLVM-Translator 的构建。
 - ..: 指向父目录（克隆仓库的根目录）作为源。
 - -DLLVM_DIR=/usr/lib/llvm-19/lib/cmake/llvm: 指定 LLVM 的 CMake 配置文件位置，确保翻译器能找到必要的 LLVM 组件。
 - -DCMAKE_INSTALL_PREFIX=/usr/local: 设置安装前缀。
sudo make install: 编译并安装 SPIRV-LLVM-Translator。

### 6. SPIRV-Tools
git clone https://github.com/KhronosGroup/SPIRV-Tools.git:SPIRV-Tools 提供了一套用于处理 SPIR-V 的工具，包括验证、优化以及汇编/反汇编。  
python3 utils/git-sync-deps: 此命令特定于 SPIRV-Tools 仓库，用于获取项目所需的任何子模块或外部依赖项。  mkdir build; cd build: 创建一个 build 目录并进入该目录。  
cmake ..: 配置 SPIRV-Tools 的构建。  
sudo make install -j8: 使用 8 个并行作业 (-j8) 编译并安装 SPIRV-Tools，以加快编译过程。

### 7. Mesa（最终构建）
git clone https://gitlab.freedesktop.org/mesa/mesa.git: 从 FreeDesktop.org 克隆官方 Mesa 3D 图形库仓库。  
git checkout -f mesa-25.1.4 -b 25.1.4: 检出特定的 mesa-25.1.4 标签并创建一个名为 25.1.4 的新分支。这确保您正在构建已知、稳定的 Mesa 版本。-f (force) 选项可能用于在分支已存在时丢弃本地更改。  
meson build -Dvulkan-drivers=panfrost -Dgallium-drivers=panfrost -Dplatforms=x11 -Dglx=auto -Dprefix=/usr/local: 使用 Meson 配置 Mesa 构建。  
 - build: 指定构建目录。
 - -Dvulkan-drivers=panfrost: 启用 Panfrost Vulkan 驱动。Panfrost 是开源的 Mali Midgard/Bifrost GPU 驱动。
 - -Dgallium-drivers=panfrost: 启用 Panfrost Gallium 驱动。Gallium3D 是 Mesa 中的一个状态跟踪层，为硬件驱动提供 API。
 - -Dplatforms=x11: 指定 Mesa 应构建为支持 X11 平台，这是许多 Linux 系统上标准的图形显示服务器。
 - -Dglx=auto: 配置 GLX（OpenGL Extension to the X Window System）支持。auto 表示如果满足依赖项，它将启用。
 - -Dprefix=/usr/local: 设置 Mesa 的安装前缀。
sudo ninja -C build install: 编译并安装 Mesa。ninja 是一个专注于速度的小型构建系统，常用于 Meson。-C build 告诉 ninja 在 build 目录中执行命令。

总而言之，这些步骤旨在：
1.  提供编译图形驱动所需的开发工具和库。
2.  安装 Mesa 使用的 Meson 构建系统。
3.  设置 LLVM 编译器基础设施，这对于 Mesa 内部的着色器编译至关重要。
4.  安装 SPIR-V 工具链（头文件、翻译器和工具），这对于处理 Vulkan 和 OpenGL 使用的现代着色器语言至关重要。
5.  最后，克隆、配置和编译 Mesa 本身，并专门支持 Panfrost 驱动、X11 和 GLX，从而允许您使用兼容 Mali GPU 的开源图形驱动。
