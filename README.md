# panthor-linux6.12

## Mesa 25.1.3
```
sudo apt install python3-pkg-resources meson gcc g++ python3-pip python3-mako python3-yaml llvm llvm-15-dev bison flex glslang-tools libllvmspirvlib-15-dev libclang-cpp15-dev libxcb-randr0-dev libxcb-glx0-dev libxcb-present-dev libxxf86vm-dev
```
```
https://github.com/KhronosGroup/SPIRV-Tools.git
python3 utils/git-sync-deps
mkdir build; cd build
cmake ..
sudo make install -j8
```
```
git clone https://gitlab.freedesktop.org/mesa/mesa.git
git checkout -f mesa-25.1.4 -b 25.1.4
meson build -Dvulkan-drivers=panfrost -Dgallium-drivers=panfrost -Dplatforms=x11 -Dglx=auto -Dprefix=/usr/local
sudo ninja -C build install
```
