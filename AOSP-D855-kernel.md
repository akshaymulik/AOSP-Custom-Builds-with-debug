#Kernel for AOSP D855
```sh
Error:
No private recovery resources for TARGET_DEVICE d855
Starting build with ninja
ninja: Entering directory `.'
ninja: error: 'out/target/product/d855/kernel', needed by 'out/target/product/d855/boot.img', missing and no known rule to make it
build/core/ninja.mk:148: recipe for target 'ninja_wrapper' failed
make: *** [ninja_wrapper] Error 1

#### make failed to build some targets (01:58 (mm:ss)) ####
```
Solution: Asuming that you have set up a build-environment:
```sh
git clone https://github.com/akshaymulik/android_kernel_lge_g3
cd android_kernel_lge_g3
export CROSS_COMPILE=~/AOSP/prebuilts/gcc/linux-x86/arm/arm-eabi-4.8/bin/arm-eabi-
make ARCH=arm CROSS_COMPILE=$CROSS_COMPILE lineageos_d855_defconfig
make ARCH=arm CROSS_COMPILE=$CROSS_COMPILE -j4
```
Output is present in arch/arm/boot
Copy the image file that is your kernel with extension " zImage-dtb " file size 6.2 MB (approx)
edit device/lge/d855/device.mk
```mk
#Kernel
ifeq ($(TARGET_PREBUILT_KERNEL),)
LOCAL_KERNEL := device/lge/d855-kernel/zImage-dtb
else
LOCAL_KERNEL := $(TARGET_PREBUILT_KERNEL)
endif

PRODUCT_COPY_FILES := \
    $(LOCAL_KERNEL):kernel
#kernel-block-ends
```
