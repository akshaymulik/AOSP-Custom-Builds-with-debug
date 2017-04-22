# error for d855 with AOSP
#vendor files https://github.com/akshaymulik/proprietary_vendor_lge 
1. 
ninja: Entering directory `.'
ninja: error: 'out/target/common/obj/JAVA_LIBRARIES/org.cyanogenmod.platform.internal_intermediates/classes.dex.toc', needed by 'out/target/common/obj/JAVA_LIBRARIES/services_intermediates/with-local/classes.dex', missing and no known rule to make it
build/core/ninja.mk:148: recipe for target 'ninja_wrapper' failed
make: *** [ninja_wrapper] Error 1

#### make failed to build some targets (01:29 (mm:ss)) ####


Solution- Remove CyanogenMod Apps from local-manifest that demand package org.cyanogenmod.platform.internal_intermediates

2.
[  0% 116/32269] target Export Resources: framework-res (out/target/common/obj/APPS/framework-res_intermediates/package-export.apk)

FAILED: /bin/bash -c "(touch out/target/common/obj/APPS/framework-res_intermediates/zipdummy ) && ((cd out/target/common/obj/APPS/framework-res_intermediates/ && jar cf package-export.apk zipdummy) ) && (zip -qd out/target/common/obj/APPS/framework-res_intermediates/package-export.apk zipdummy ) && (rm out/target/common/obj/APPS/framework-res_intermediates/zipdummy ) && (out/host/linux-x86/bin/aapt package -u -x --private-symbols com.android.internal -z  --pseudo-localize   -M frameworks/base/core/res/AndroidManifest.xml -S device/lge/d855/overlay/frameworks/base/core/res/res -S

device/lge/g3-common/overlay/frameworks/base/core/res/res -S frameworks/base/core/res/res -A frameworks/base/core/res/assets  --min-sdk-version 25 --target-sdk-version 25 --product default --version-code 25 --version-name 7.1.2   --skip-symbols-without-default-localization -F out/target/common/obj/APPS/framework-res_intermediates/package-export.apk )"

device/lge/g3-common/overlay/frameworks/base/core/res/res/values/config.xml:129: error: Resource at config_intrusiveBatteryLed appears in overlay but not in the base package; use <add-resource> to add.

device/lge/g3-common/overlay/frameworks/base/core/res/res/values/config.xml:243: error: Resource at config_deviceHardwareKeys appears in overlay but not in the base package; use <add-resource> to add.

device/lge/g3-common/overlay/frameworks/base/core/res/res/values/config.xml:257: error: Resource at config_deviceHardwareWakeKeys appears in overlay but not in the base package; use <add-resource> to add.

device/lge/g3-common/overlay/frameworks/base/core/res/res/values/config.xml:299: error: Resource at config_uiBlurEnabled appears in overlay but not in the base package; use <add-resource> to add.

ninja: build stopped: subcommand failed.
build/core/ninja.mk:148: recipe for target 'ninja_wrapper' failed
make: *** [ninja_wrapper] Error 1



Solution: add this lines to file: https://android.googlesource.com/platform/frameworks/base/+/master/core/res/res/values/config.xml


<!-- Hardware keys present on the device, stored as a bit field.
         This integer should equal the sum of the corresponding value for each
         of the following keys present:
             1 - Home
             2 - Back
             4 - Menu
             8 - Assistant (search)
            16 - App switch
            32 - Camera
            64 - Volume rocker
         For example, a device with Home, Back and Menu keys would set this
         config to 7. -->
    <integer name="config_deviceHardwareKeys">79</integer>
    
<add-resource type="bool" name="config_intrusiveBatteryLed"></add-resource>
<add-resource type="bool" name="config_ui_blur_enabled"></add-resource>


    <!-- Hardware keys present on the device with the ability to wake, stored as a bit field.
         This integer should equal the sum of the corresponding value for each
         of the following keys present:
             1 - Home
             2 - Back
             4 - Menu
             8 - Assistant (search)
            16 - App switch
            32 - Camera
            64 - Volume rocker
         For example, a device with Home, Back and Menu keys would set this
         config to 7. -->
    <integer name="config_deviceHardwareWakeKeys">79</integer>




3. $ ~/AOSP/prebuilts/misc/linux-x86/ccache/ccache -M 50G
error:Could not set cache size limit.
solution: $ sudo ~/AOSP/prebuilts/misc/linux-x86/ccache/ccache -M 50G
Set cache size limit to 50.0 Gbytes

4. FAILED: /bin/bash -c "(out/host/linux-x86/bin/checkpolicy -M -c 30 -o out/target/product/d855/obj/ETC/sepolicy_intermediates/sepolicy.tmp 
out/target/product/d855/obj/ETC/sepolicy_intermediates/policy.conf ) && (out/host/linux-x86/bin/checkpolicy -M -c 30 -o out/target/product/d855/obj/ETC/sepolicy_intermediates//sepolicy.dontaudit out/target/product/d855/obj/ETC/sepolicy_intermediates/policy.conf.dontaudit ) && (out/host/linux-x86/bin/sepolicy-analyze out/target/product/d855/obj/ETC/sepolicy_intermediates/sepolicy.tmp permissive > out/target/product/d855/obj/ETC/sepolicy_intermediates/sepolicy.permissivedomains ) && (if [ \"userdebug\" = \"user\" -a -s out/target/product/d855/obj/ETC/sepolicy_intermediates/sepolicy.permissivedomains ]; then 		echo \"==========\" 1>&2; 		echo \"ERROR: permissive domains not allowed in user builds\" 1>&2; 		echo \"List of invalid domains:\" 1>&2; 		cat out/target/product/d855/obj/ETC/sepolicy_intermediates/sepolicy.permissivedomains 1>&2; 		exit 1; 		fi ) && (mv out/target/product/d855/obj/ETC/sepolicy_intermediates/sepolicy.tmp out/target/product/d855/obj/ETC/sepolicy_intermediates/sepolicy )"
device/qcom/sepolicy/common/audioserver.te:63:ERROR 'attribute qti_debugfs_domain is not declared' at token ';' on line 21364:
typeattribute audioserver qti_debugfs_domain;
#line 63
checkpolicy:  error(s) encountered while parsing configuration
out/host/linux-x86/bin/checkpolicy:  loading policy configuration from out/target/product/d855/obj/ETC/sepolicy_intermediates/policy.conf
ninja: build stopped: subcommand failed.
build/core/ninja.mk:148: recipe for target 'ninja_wrapper' failed
make: *** [ninja_wrapper] Error 1


Solution:
Under system/sepolicy
1. file:attributes
add-line:
```
 + #Domain type used for debugfs access by QCOM
 + attribute qti_debugfs_domain;
```

2. create file: qti-testscripts.te
add-line:
```
+ userdebug_or_eng('type qti-testscripts, domain, domain_deprecated, mlstrustedsubject;')
```

3.file:domain.te
chane line:
```
- neverallow { domain -init -system_server -dumpstate } debugfs:file no_rw_file_perms;
- appdomain -shell userdebug_or_eng(`-su')  
+ neverallow { domain -init -system_server -dumpstate userdebug_or_eng(`-qti_debugfs_domain')} debugfs:file no_rw_file_perms;
+ appdomain -shell userdebug_or_eng(`-su -qti-testscripts')
```
4. hardware/libhardware/include/hardware -I system/security/softkeymaster/include/keymaster -I system/vold -I out/target/product/d855/obj/EXECUTABLES/vold_intermediates -I out/target/product/d855/gen/EXECUTABLES/vold_intermediates -I libnativehelper/include/nativehelper \$(cat out/target/product/d855/obj/EXECUTABLES/vold_intermediates/import_includes) -isystem system/core/include -isystem system/media/audio/include -isystem hardware/libhardware/include -isystem hardware/libhardware_legacy/include -isystem hardware/ril/include -isystem libnativehelper/include -isystem frameworks/native/opengl/include -isystem frameworks/av/include -isystem frameworks/base/include -isystem out/target/product/d855/obj/include -isystem bionic/libc/arch-arm/include -isystem bionic/libc/include -isystem bionic/libc/kernel/uapi -isystem bionic/libc/kernel/common -isystem bionic/libc/kernel/uapi/asm-arm -isystem bionic/libm/include -isystem bionic/libm/include/arm -c    -fno-exceptions -Wno-multichar -msoft-float -ffunction-sections -fdata-sections -funwind-tables -fstack-protector-strong -Wa,--noexecstack -Werror=format-security -D_FORTIFY_SOURCE=2 -fno-short-enums -no-canonical-prefixes -mcpu=cortex-a15 -D__ARM_FEATURE_LPAE=1 -mfloat-abi=softfp -mfpu=neon -DANDROID -fmessage-length=0 -W -Wall -Wno-unused -Winit-self -Wpointer-arith -Werror=return-type -Werror=non-virtual-dtor -Werror=address -Werror=sequence-point -Werror=date-time -DNDEBUG -g -Wstrict-aliasing=2 -DNDEBUG -UDEBUG  -D__compiler_offsetof=__builtin_offsetof -Werror=int-conversion -Wno-reserved-id-macro -Wno-format-pedantic -Wno-unused-command-line-argument -fcolor-diagnostics -nostdlibinc  -mcpu=krait -mfpu=neon-vfpv4 -target arm-linux-androideabi    -target arm-linux-androideabi -Bprebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/arm-linux-androideabi/bin    -std=gnu99     -mthumb -Os -fomit-frame-pointer -fno-strict-aliasing   -Werror -Wall -Wno-missing-field-initializers -Wno-unused-variable -Wno-unused-parameter -DCONFIG_HW_DISK_ENCRYPTION -fpie -D_USING_LIBCXX -std=c11  -Werror=int-to-pointer-cast -Werror=pointer-to-int-cast  -Werror=address-of-temporary -Werror=null-dereference -Werror=return-type  -MD -MF out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d -o out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.o system/vold/cryptfs.c ) && (cp out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.P; sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\\\\$//' -e '/^\$/ d' -e 's/\$/ :/' < out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d >> out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.P; rm -f out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d )"
system/vold/cryptfs.c:74:10: fatal error: 'cryptfs_hw.h' file not found
#include "cryptfs_hw.h"
         ^
1 error generated.
[  0% 8/12809] Ensure Jack server is installed and started
Jack server already installed in "/home/akshay/.jack-server"
Server is already running
ninja: build stopped: subcommand failed.
build/core/ninja.mk:148: recipe for target 'ninja_wrapper' failed
make: *** [ninja_wrapper] Error 1

#### make failed to build some targets (05:43 (mm:ss)) ####

Solution:
file: system/vold/Android.mk
after line: ifeq ($(TARGET_HW_DISK_ENCRYPTION),true)
add
```
+TARGET_CRYPTFS_HW_PATH ?= device/qcom/common/cryptfs_hw
```
