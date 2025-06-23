# panthor-linux6.12

## Mesa 25.1.3
```
sudo apt update 
sudo apt install byacc,flex,libxcb-randr0-dev libxcb-glx0-dev libxcb-present-dev libx11-xcb-dev libxcb-dri3-dev libxshmfence-dev
libxxf86vm-dev python3-pkg-resources python3-pip python3-mako python3-yaml
```
```
sudo apt install setup-tools
sudo wget https://github.com/mesonbuild/meson/releases/download/1.8.2/meson-1.8.2.tar.gz
tar -xf meson-1.8.2.tar.gz
sudo python3 setup.py install
```
```
sudo apt install lsb-release wget software-properties-common gnupg
wget https://apt.llvm.org/llvm.sh
chmod +x llvm.sh
sudo ./llvm.sh 19 all
```
```
git clone https://github.com/KhronosGroup/SPIRV-Headers.git
sudo cmake -S SPIRV-Headers -B build -DCMAKE_INSTALL_PREFIX=/usr/local
sudo cmake --build build --target install
```
```
git clone -b llvm_release_190 https://github.com/KhronosGroup/SPIRV-LLVM-Translator.git
cd SPIRV-LLVM-Translator
mkdir build && cd build
cmake .. -DLLVM_DIR=/usr/lib/llvm-15/lib/cmake/llvm -DCMAKE_INSTALL_PREFIX=/usr/local 
sudo make install
```
```
git clone https://gitlab.freedesktop.org/mesa/mesa.git
git checkout -f mesa-25.1.4 -b 25.1.4
meson build -Dvulkan-drivers=panfrost -Dgallium-drivers=panfrost -Dplatforms=x11 -Dglx=auto -Dprefix=/usr/local
sudo ninja -C build install
```