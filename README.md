# panthor-linux6.12
panthor integration for rockchip platform

## rk3588 Mali-G610 Linux-6.12配置:
- arch/arm64/configs/rk3588_linux.config
```
diff --git a/arch/arm64/configs/rk3588_linux.config b/arch/arm64/configs/rk3588_linux.config
index f521d5b90ca60..a0fde3b0d3bbf 100644
--- a/arch/arm64/configs/rk3588_linux.config
+++ b/arch/arm64/configs/rk3588_linux.config
@@ -1,2 +1,5 @@
 # CONFIG_BCMDHD_SDIO=y is not set
 CONFIG_BCMDHD_PCIE=y
+CONFIG_DRM_PANTHOR=y
+CONFIG_EXTRA_FIRMWARE="arm/mali/arch10.8/mali_csffw.bin"
```

- arch/arm64/boot/dts/rockchip/rk3588s.dtsi
```
    gpu: gpu@fb000000 {
        compatible = "rockchip,rk3588-mali", "arm,mali-valhall-csf";
        reg = <0x0 0xfb000000 0x0 0x200000>;
        interrupts = <GIC_SPI 92 IRQ_TYPE_LEVEL_HIGH>,
                 <GIC_SPI 93 IRQ_TYPE_LEVEL_HIGH>,
                 <GIC_SPI 94 IRQ_TYPE_LEVEL_HIGH>;
        interrupt-names = "job", "mmu", "gpu";

        clock-names = "core", "coregroup", "stacks";
        clocks = <&cru CLK_GPU>, <&cru CLK_GPU_COREGROUP>,
             <&cru CLK_GPU_STACKS>;
        assigned-clocks = <&scmi_clk SCMI_CLK_GPU>;
        assigned-clock-rates = <200000000>;
        power-domains = <&power RK3588_PD_GPU>;
        operating-points-v2 = <&gpu_opp_table>;
        #cooling-cells = <2>; 
        dynamic-power-coefficient = <2982>;

        status = "disabled";
    };

    gpu_opp_table: gpu-opp-table {
        compatible = "operating-points-v2";

        nvmem-cells = <&gpu_leakage>;
        nvmem-cell-names = "leakage";

        rockchip,pvtm-voltage-sel = <
            0   815 0
            816 835 1
            836 860 2
            861 885 3
            886 910 4
            911 9999    5
        >;
        rockchip,pvtm-pvtpll;
        rockchip,pvtm-offset = <0x1c>;
        rockchip,pvtm-sample-time = <1100>;
        rockchip,pvtm-freq = <800000>;
        rockchip,pvtm-volt = <750000>;
        rockchip,pvtm-ref-temp = <25>;
        rockchip,pvtm-temp-prop = <(-135) (-135)>;
        rockchip,pvtm-thermal-zone = "gpu-thermal";

        clocks = <&cru CLK_GPU>;
        clock-names = "clk";
        rockchip,grf = <&gpu_grf>;
        volt-mem-read-margin = <
            855000  1
            765000  2
            675000  3
            495000  4
        >;
        low-volt-mem-read-margin = <4>;
        intermediate-threshold-freq = <400000>; /* KHz */

        rockchip,temp-hysteresis = <5000>;
        rockchip,low-temp = <10000>;
        rockchip,low-temp-min-volt = <750000>;
        rockchip,high-temp = <85000>;
        rockchip,high-temp-max-freq = <800000>;

        opp-300000000 {
            opp-hz = /bits/ 64 <300000000>;
            opp-microvolt = <675000 675000 850000>;
        };
        opp-400000000 {
            opp-hz = /bits/ 64 <400000000>;
            opp-microvolt = <675000 675000 850000>;
        };
        opp-500000000 {
            opp-hz = /bits/ 64 <500000000>;
            opp-microvolt = <675000 675000 850000>;
        };
        opp-600000000 {
            opp-hz = /bits/ 64 <600000000>;
            opp-microvolt = <675000 675000 850000>;
        };
        opp-700000000 {
            opp-hz = /bits/ 64 <700000000>;
            opp-microvolt = <700000 700000 850000>;
            opp-microvolt-L2 = <687500 687500 850000>;
            opp-microvolt-L3 = <675000 675000 850000>;
            opp-microvolt-L4 = <675000 675000 850000>;
            opp-microvolt-L5 = <675000 675000 850000>;
        };                                                                                                                                                           
        opp-800000000 {
            opp-hz = /bits/ 64 <800000000>;
            opp-microvolt = <750000 750000 850000>;
            opp-microvolt-L1 = <737500 737500 850000>;
            opp-microvolt-L2 = <725000 725000 850000>;
            opp-microvolt-L3 = <712500 712500 850000>;                                     
            opp-microvolt-L4 = <700000 700000 850000>;
            opp-microvolt-L5 = <700000 700000 850000>;
        };
        opp-900000000 {
            opp-hz = /bits/ 64 <900000000>;
            opp-microvolt = <800000 800000 850000>;
            opp-microvolt-L1 = <787500 787500 850000>;
            opp-microvolt-L2 = <775000 775000 850000>;
            opp-microvolt-L3 = <762500 762500 850000>;
            opp-microvolt-L4 = <750000 750000 850000>;
            opp-microvolt-L5 = <737500 737500 850000>;
        };
        opp-1000000000 {
            opp-hz = /bits/ 64 <1000000000>;
            opp-microvolt = <850000 850000 850000>;
            opp-microvolt-L1 = <837500 837500 850000>;
            opp-microvolt-L2 = <825000 825000 850000>;
            opp-microvolt-L3 = <812500 812500 850000>;
            opp-microvolt-L4 = <800000 800000 850000>;
            opp-microvolt-L5 = <787500 787500 850000>;                                                                                                               
        };
    };
```

- arch/arm64/boot/dts/rockchip/rk3588-rk806-dual.dtsi
```
diff --git a/arch/arm64/boot/dts/rockchip/rk3588-rk806-dual.dtsi b/arch/arm64/boot/dts/rockchip/rk3588-rk806-dual.dtsi
index 49ba5baf1a760..cf1fb7e8355df 100644
--- a/arch/arm64/boot/dts/rockchip/rk3588-rk806-dual.dtsi
+++ b/arch/arm64/boot/dts/rockchip/rk3588-rk806-dual.dtsi
@@ -149,14 +149,14 @@ rk806_dvs3_gpio: rk806_dvs3_gpio {
 
                regulators {
                        vdd_gpu_s0: DCDC_REG1 {
-                               regulator-boot-on;
+                               regulator-always-on;
                                regulator-min-microvolt = <550000>;
                                regulator-max-microvolt = <950000>;
                                regulator-ramp-delay = <12500>;
                                regulator-name = "vdd_gpu_s0";
                                regulator-enable-ramp-delay = <400>;
                                regulator-state-mem {
-                                       regulator-off-in-suspend;
+                                       regulator-always-on;
                                };
                        };
 
@@ -198,14 +198,14 @@ regulator-state-mem {
                        };
 
                        vdd_gpu_mem_s0: DCDC_REG5 {
-                               regulator-boot-on;
+                               regulator-always-on;
                                regulator-min-microvolt = <675000>;
                                regulator-max-microvolt = <950000>;
                                regulator-ramp-delay = <12500>;
                                regulator-enable-ramp-delay = <400>;
                                regulator-name = "vdd_gpu_mem_s0";
                                regulator-state-mem {
-                                       regulator-off-in-suspend;
+                                       regulator-always-on;
                                };
                        };
```

- 下载Mali-G610 firmware镜像文件mali_csffw.bin (也可以直接使用本仓库中的的mali_csffw.bin文件)：
https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git

- 拷贝到linux-6.12内核源码以下位置后，编译linux-6.12内核镜像并烧写：
firmware/arm/mali/arch10.8/mali_csffw.bin

## Mesa 25.x.x编译安装说明
- 安装依赖项
```
sudo apt update
sudo apt install byacc flex libxcb-randr0-dev libxcb-glx0-dev libxcb-present-dev libx11-xcb-dev libxcb-dri3-dev libxshmfence-dev libxxf86vm-dev python3-pkg-resources python3-pip python3-mako python3-yaml  
```

- 安装meson 1.8.2版本
```
sudo apt install setup-tools
sudo wget https://github.com/mesonbuild/meson/releases/download/1.8.2/meson-1.8.2.tar.gz
tar -xf meson-1.8.2.tar.gz
sudo python3 setup.py install
```

- 安装LLVM-19
```
sudo apt install lsb-release wget software-properties-common gnupg

wget https://apt.llvm.org/llvm.sh 
chmod +x llvm.sh
sudo ./llvm.sh 19 all
```

- 安装SPIRV-Headers
```
git clone https://github.com/KhronosGroup/SPIRV-Headers.git  
sudo cmake -S SPIRV-Headers -B build -DCMAKE_INSTALL_PREFIX=/usr/local  
sudo cmake --build build --target install
```

- 安装SPIRV-LLVM-Translator
```
git clone -b llvm_release_190 https://github.com/KhronosGroup/SPIRV-LLVM-Translator.git:
cd SPIRV-LLVM-Translator
mkdir build && cd build
cmake .. -DLLVM_DIR=/usr/lib/llvm-19/lib/cmake/llvm -DCMAKE_INSTALL_PREFIX=/usr/local
sudo make install
```

- 安装SPIRV-Tools
```
git clone https://github.com/KhronosGroup/SPIRV-Tools.git
python3 utils/git-sync-deps
cmake ..
sudo make install -j8
```

- 安装Mesa（最终构建）
```
git clone https://gitlab.freedesktop.org/mesa/mesa.git
git checkout -f mesa-25.1.4 -b 25.1.4
meson build -Dvulkan-drivers=panfrost -Dgallium-drivers=panfrost -Dplatforms=x11 -Dglx=auto -Dprefix=/usr/local
sudo ninja -C build install
```
