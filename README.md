# Error while compiling AOSP for D855
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
5. hardware/libhardware/include/hardware -I system/security/softkeymaster/include/keymaster -I system/vold -I out/target/product/d855/obj/EXECUTABLES/vold_intermediates -I out/target/product/d855/gen/EXECUTABLES/vold_intermediates -I libnativehelper/include/nativehelper \$(cat out/target/product/d855/obj/EXECUTABLES/vold_intermediates/import_includes) -isystem system/core/include -isystem system/media/audio/include -isystem hardware/libhardware/include -isystem hardware/libhardware_legacy/include -isystem hardware/ril/include -isystem libnativehelper/include -isystem frameworks/native/opengl/include -isystem frameworks/av/include -isystem frameworks/base/include -isystem out/target/product/d855/obj/include -isystem bionic/libc/arch-arm/include -isystem bionic/libc/include -isystem bionic/libc/kernel/uapi -isystem bionic/libc/kernel/common -isystem bionic/libc/kernel/uapi/asm-arm -isystem bionic/libm/include -isystem bionic/libm/include/arm -c    -fno-exceptions -Wno-multichar -msoft-float -ffunction-sections -fdata-sections -funwind-tables -fstack-protector-strong -Wa,--noexecstack -Werror=format-security -D_FORTIFY_SOURCE=2 -fno-short-enums -no-canonical-prefixes -mcpu=cortex-a15 -D__ARM_FEATURE_LPAE=1 -mfloat-abi=softfp -mfpu=neon -DANDROID -fmessage-length=0 -W -Wall -Wno-unused -Winit-self -Wpointer-arith -Werror=return-type -Werror=non-virtual-dtor -Werror=address -Werror=sequence-point -Werror=date-time -DNDEBUG -g -Wstrict-aliasing=2 -DNDEBUG -UDEBUG  -D__compiler_offsetof=__builtin_offsetof -Werror=int-conversion -Wno-reserved-id-macro -Wno-format-pedantic -Wno-unused-command-line-argument -fcolor-diagnostics -nostdlibinc  -mcpu=krait -mfpu=neon-vfpv4 -target arm-linux-androideabi    -target arm-linux-androideabi -Bprebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/arm-linux-androideabi/bin    -std=gnu99     -mthumb -Os -fomit-frame-pointer -fno-strict-aliasing   -Werror -Wall -Wno-missing-field-initializers -Wno-unused-variable -Wno-unused-parameter -DCONFIG_HW_DISK_ENCRYPTION -fpie -D_USING_LIBCXX -std=c11  -Werror=int-to-pointer-cast -Werror=pointer-to-int-cast  -Werror=address-of-temporary -Werror=null-dereference -Werror=return-type  -MD -MF out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d -o out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.o system/vold/cryptfs.c ) && (cp out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.P; sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\\\\$//' -e '/^\$/ d' -e 's/\$/ :/' < out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d >> out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.P; rm -f out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d )"
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

6. [  1% 194/12242] target thumb C: vold <= system/vold/cryptfs.c
FAILED: /bin/bash -c "(PWD=/proc/self/cwd prebuilts/misc/linux-x86/ccache/ccache prebuilts/clang/host/linux-x86/clang-2690385/bin/clang -I system/extras/ext4_utils -I system/extras/f2fs_utils -I external/scrypt/lib/crypto -I frameworks/native/include -I system/security/keystore -I hardware/libhardware/include/hardware -I system/security/softkeymaster/include/keymaster -I device/qcom/common/cryptfs_hw -I system/vold -I out/target/product/d855/obj/EXECUTABLES/vold_intermediates -I out/target/product/d855/gen/EXECUTABLES/vold_intermediates -I libnativehelper/include/nativehelper \$(cat out/target/product/d855/obj/EXECUTABLES/vold_intermediates/import_includes) -isystem system/core/include -isystem system/media/audio/include -isystem hardware/libhardware/include -isystem hardware/libhardware_legacy/include -isystem hardware/ril/include -isystem libnativehelper/include -isystem frameworks/native/opengl/include -isystem frameworks/av/include -isystem frameworks/base/include -isystem out/target/product/d855/obj/include -isystem bionic/libc/arch-arm/include -isystem bionic/libc/include -isystem bionic/libc/kernel/uapi -isystem bionic/libc/kernel/common -isystem bionic/libc/kernel/uapi/asm-arm -isystem bionic/libm/include -isystem bionic/libm/include/arm -c    -fno-exceptions -Wno-multichar -msoft-float -ffunction-sections -fdata-sections -funwind-tables -fstack-protector-strong -Wa,--noexecstack -Werror=format-security -D_FORTIFY_SOURCE=2 -fno-short-enums -no-canonical-prefixes -mcpu=cortex-a15 -D__ARM_FEATURE_LPAE=1 -mfloat-abi=softfp -mfpu=neon -DANDROID -fmessage-length=0 -W -Wall -Wno-unused -Winit-self -Wpointer-arith -Werror=return-type -Werror=non-virtual-dtor -Werror=address -Werror=sequence-point -Werror=date-time -DNDEBUG -g -Wstrict-aliasing=2 -DNDEBUG -UDEBUG  -D__compiler_offsetof=__builtin_offsetof -Werror=int-conversion -Wno-reserved-id-macro -Wno-format-pedantic -Wno-unused-command-line-argument -fcolor-diagnostics -nostdlibinc  -mcpu=krait -mfpu=neon-vfpv4 -target arm-linux-androideabi    -target arm-linux-androideabi -Bprebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/arm-linux-androideabi/bin    -std=gnu99     -mthumb -Os -fomit-frame-pointer -fno-strict-aliasing   -Werror -Wall -Wno-missing-field-initializers -Wno-unused-variable -Wno-unused-parameter -DCONFIG_HW_DISK_ENCRYPTION -fpie -D_USING_LIBCXX -std=c11  -Werror=int-to-pointer-cast -Werror=pointer-to-int-cast  -Werror=address-of-temporary -Werror=null-dereference -Werror=return-type  -MD -MF out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d -o out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.o system/vold/cryptfs.c ) && (cp out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.P; sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\\\\$//' -e '/^\$/ d' -e 's/\$/ :/' < out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d >> out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.P; rm -f out/target/product/d855/obj/EXECUTABLES/vold_intermediates/cryptfs.d )"
system/vold/cryptfs.c:3365:106: error: too few arguments to function call, expected 3, have 2
            int rc = update_hw_device_encryption_key(DEFAULT_PASSWORD, (char*) crypt_ftr.crypto_type_name);
                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~                                                     ^
device/qcom/common/cryptfs_hw/cryptfs_hw.h:37:1: note: 'update_hw_device_encryption_key' declared here
int update_hw_device_encryption_key(const char*, const char*, const char*);
^
system/vold/cryptfs.c:3370:95: error: too few arguments to function call, expected 3, have 2
            int rc = update_hw_device_encryption_key(newpw, (char*) crypt_ftr.crypto_type_name);
                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~                                          ^
device/qcom/common/cryptfs_hw/cryptfs_hw.h:37:1: note: 'update_hw_device_encryption_key' declared here
int update_hw_device_encryption_key(const char*, const char*, const char*);
^
2 errors generated.

Solution:
in file: .repo/local_manifest/local_manifest.xml
add lines:
```xml
<remove-project name="platform/system/vold"/>
    <project path="system/vold" name="LineageOS/android_system_vold" remote="github" revision="cm-14.1" />
```
Then in terminal
```
$repo sync --force-sync system/vold
```
When you make you will again get this error:
ninja: error: 'out/target/product/d855/obj/SHARED_LIBRARIES/libcrypto_utils_intermediates/export_includes', needed by 'out/target/product/d855/obj/EXECUTABLES/vold_intermediates/import_includes', missing and no known rule to make it
build/core/ninja.mk:148: recipe for target 'ninja_wrapper' failed
make: *** [ninja_wrapper] Error 1

Solution:
File: system/vold/Android.mk
remove
```
- libcrypto_utils \
```
under: common_static_libraries := \
add line:
```
+ libmincrypt  \
```
7.[  0% 46/12810] target thumb C: libvold <= system/vold/cryptfs.c
FAILED: /bin/bash -c "(PWD=/proc/self/cwd prebuilts/misc/linux-x86/ccache/ccache prebuilts/clang/host/linux-x86/clang-2690385/bin/clang -I system/extras/ext4_utils -I system/extras/f2fs_utils -I external/scrypt/lib/crypto -I frameworks/native/include -I system/security/keystore -I hardware/libhardware/include/hardware -I system/security/softkeymaster/include/keymaster -I external/e2fsprogs/lib -I device/qcom/common/cryptfs_hw -I system/vold -I out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates -I out/target/product/d855/gen/STATIC_LIBRARIES/libvold_intermediates -I libnativehelper/include/nativehelper \$(cat out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/import_includes) -isystem system/core/include -isystem system/media/audio/include -isystem hardware/libhardware/include -isystem hardware/libhardware_legacy/include -isystem hardware/ril/include -isystem libnativehelper/include -isystem frameworks/native/opengl/include -isystem frameworks/av/include -isystem frameworks/base/include -isystem out/target/product/d855/obj/include -isystem bionic/libc/arch-arm/include -isystem bionic/libc/include -isystem bionic/libc/kernel/uapi -isystem bionic/libc/kernel/common -isystem bionic/libc/kernel/uapi/asm-arm -isystem bionic/libm/include -isystem bionic/libm/include/arm -c    -fno-exceptions -Wno-multichar -msoft-float -ffunction-sections -fdata-sections -funwind-tables -fstack-protector-strong -Wa,--noexecstack -Werror=format-security -D_FORTIFY_SOURCE=2 -fno-short-enums -no-canonical-prefixes -mcpu=cortex-a15 -D__ARM_FEATURE_LPAE=1 -mfloat-abi=softfp -mfpu=neon -DANDROID -fmessage-length=0 -W -Wall -Wno-unused -Winit-self -Wpointer-arith -Werror=return-type -Werror=non-virtual-dtor -Werror=address -Werror=sequence-point -Werror=date-time -DNDEBUG -g -Wstrict-aliasing=2 -DNDEBUG -UDEBUG  -D__compiler_offsetof=__builtin_offsetof -Werror=int-conversion -Wno-reserved-id-macro -Wno-format-pedantic -Wno-unused-command-line-argument -fcolor-diagnostics -nostdlibinc  -mcpu=krait -mfpu=neon-vfpv4 -target arm-linux-androideabi    -target arm-linux-androideabi -Bprebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/arm-linux-androideabi/bin    -std=gnu99     -mthumb -Os -fomit-frame-pointer -fno-strict-aliasing   -Werror -Wall -Wno-missing-field-initializers -Wno-unused-variable -Wno-unused-parameter -DCONFIG_HW_DISK_ENCRYPTION -fPIC -D_USING_LIBCXX -std=c11  -Werror=int-to-pointer-cast -Werror=pointer-to-int-cast  -Werror=address-of-temporary -Werror=null-dereference -Werror=return-type  -MD -MF out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/cryptfs.d -o out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/cryptfs.o system/vold/cryptfs.c ) && (cp out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/cryptfs.d out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/cryptfs.P; sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\\\\$//' -e '/^\$/ d' -e 's/\$/ :/' < out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/cryptfs.d >> out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/cryptfs.P; rm -f out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/cryptfs.d )"

system/vold/cryptfs.c:2280:24: error: implicit declaration of function 'fs_mgr_is_mdtp_activated' is invalid in C99 [-Werror,-Wimplicit-function-declaration]
  int mdtp_activated = fs_mgr_is_mdtp_activated();
                       ^
1 error generated.

Solution:
file:system/core/fs_mgr/include/fs_mgr.h
before 
```cpp 
#ifdef __cplusplus
```
add line: 
```cpp
+  int fs_mgr_is_mdtp_activated(void);
```
in file:system/core/fs_mgr/fs_mgr.c
add this function
```cpp
int fs_mgr_is_mdtp_activated()
{
      char cmdline[2048];
      char *ptr;
      int fd;
      static int mdtp_activated = 0;
      static int mdtp_activated_set = 0;

      if (mdtp_activated_set) {
          return mdtp_activated;
      }

      fd = open("/proc/cmdline", O_RDONLY);
      if (fd >= 0) {
          int n = read(fd, cmdline, sizeof(cmdline) - 1);
          if (n < 0) n = 0;

          /* get rid of trailing newline, it happens */
          if (n > 0 && cmdline[n-1] == '\n') n--;

          cmdline[n] = 0;
          close(fd);
      } else {
          cmdline[0] = 0;
      }

      ptr = cmdline;
      while (ptr && *ptr) {
          char *x = strchr(ptr, ' ');
          if (x != 0) *x++ = 0;
          if (!strcmp(ptr,"mdtp")) {
            mdtp_activated = 1;
            break;
          }
          ptr = x;
      }

      mdtp_activated_set = 1;

      return mdtp_activated;
}


```
```cpp
change function:
int fs_mgr_mount_all(struct fstab *fstab, int mount_mode)
{
    int i = 0;
    int encryptable = FS_MGR_MNTALL_DEV_NOT_ENCRYPTABLE;
    int error_count = 0;
    int mret = -1;
    int mount_errno = 0;
    int attempted_idx = -1;
    char propbuf[PROPERTY_VALUE_MAX];
    bool is_ffbm = false;

    if (!fstab) {
        return -1;
    }
    /**get boot mode*/
    property_get("ro.bootmode", propbuf, "");
    if (strncmp(propbuf, "ffbm", 4) == 0)
        is_ffbm = true;

    for (i = 0; i < fstab->num_entries; i++) {
        /* Skip userdata partition in ffbm mode */
        if (is_ffbm && !strcmp(fstab->recs[i].mount_point, "/data")){
            INFO("ffbm mode,skip mount userdata");
            continue;
        }

        /* Don't mount entries that are managed by vold or not for the mount mode*/
        if ((fstab->recs[i].fs_mgr_flags & (MF_VOLDMANAGED | MF_RECOVERYONLY)) ||
             ((mount_mode == MOUNT_MODE_LATE) && !fs_mgr_is_latemount(&fstab->recs[i])) ||
             ((mount_mode == MOUNT_MODE_EARLY) && fs_mgr_is_latemount(&fstab->recs[i]))) {
            continue;
        }

        /* Skip swap and raw partition entries such as boot, recovery, etc */
        if (!strcmp(fstab->recs[i].fs_type, "swap") ||
            !strcmp(fstab->recs[i].fs_type, "emmc") ||
            !strcmp(fstab->recs[i].fs_type, "mtd")) {
            continue;
        }

        /* Skip mounting the root partition, as it will already have been mounted */
        if (!strcmp(fstab->recs[i].mount_point, "/")) {
            if ((fstab->recs[i].fs_mgr_flags & MS_RDONLY) != 0) {
                fs_mgr_set_blk_ro(fstab->recs[i].blk_device);
            }
            continue;
        }

        /* Translate LABEL= file system labels into block devices */
        if (!strcmp(fstab->recs[i].fs_type, "ext2") ||
            !strcmp(fstab->recs[i].fs_type, "ext3") ||
            !strcmp(fstab->recs[i].fs_type, "ext4")) {
            int tret = translate_ext_labels(&fstab->recs[i]);
            if (tret < 0) {
                ERROR("Could not translate label to block device\n");
                continue;
            }
        }

        if (fstab->recs[i].fs_mgr_flags & MF_WAIT) {
            wait_for_file(fstab->recs[i].blk_device, WAIT_TIMEOUT);
        }

        if ((fstab->recs[i].fs_mgr_flags & MF_VERIFY) && device_is_secure()) {
            int rc = fs_mgr_setup_verity(&fstab->recs[i]);
            if (device_is_debuggable() && rc == FS_MGR_SETUP_VERITY_DISABLED) {
                INFO("Verity disabled");
            } else if (rc != FS_MGR_SETUP_VERITY_SUCCESS) {
                ERROR("Could not set up verified partition, skipping!\n");
                continue;
            }
        }

        if (fs_mgr_is_mdtp_activated() && ((fstab->recs[i].fs_mgr_flags & MF_FORCECRYPT) ||
            device_is_force_encrypted())) {
            INFO("%s(): mdtp activated, blkdev %s for mount %s type %s expected to be encrypted)\n",
                 __func__, fstab->recs[i].blk_device, fstab->recs[i].mount_point,
                 fstab->recs[i].fs_type);
            if (fs_mgr_do_tmpfs_mount(fstab->recs[i].mount_point) < 0) {
                ++error_count;
                continue;
            }

            encryptable = FS_MGR_MNTALL_DEV_MIGHT_BE_ENCRYPTED;

        } else {
            int last_idx_inspected;
            int top_idx = i;

            mret = mount_with_alternatives(fstab, i, &last_idx_inspected, &attempted_idx);
            i = last_idx_inspected;
            mount_errno = errno;

            /* Deal with encryptability. */
            if (!mret) {
                int status = handle_encryptable(&fstab->recs[attempted_idx]);

                if (status == FS_MGR_MNTALL_FAIL) {
                    /* Fatal error - no point continuing */
                    return status;
                }

                if (status != FS_MGR_MNTALL_DEV_NOT_ENCRYPTABLE) {
                    if (encryptable != FS_MGR_MNTALL_DEV_NOT_ENCRYPTABLE) {
                        // Log and continue
                        ERROR("Only one encryptable/encrypted partition supported\n");
                    }
                    encryptable = status;
                }

                /* Success!  Go get the next one */
                continue;
            }

            /* mount(2) returned an error, handle the encryptable/formattable case */
            bool wiped = partition_wiped(fstab->recs[top_idx].blk_device);
            if (mret && mount_errno != EBUSY && mount_errno != EACCES &&
                fs_mgr_is_formattable(&fstab->recs[top_idx]) && wiped) {
                /* top_idx and attempted_idx point at the same partition, but sometimes
                 * at two different lines in the fstab.  Use the top one for formatting
                 * as that is the preferred one.
                 */
                ERROR("%s(): %s is wiped and %s %s is formattable. Format it.\n", __func__,
                      fstab->recs[top_idx].blk_device, fstab->recs[top_idx].mount_point,
                      fstab->recs[top_idx].fs_type);
                if (fs_mgr_is_encryptable(&fstab->recs[top_idx]) &&
                    strcmp(fstab->recs[top_idx].key_loc, KEY_IN_FOOTER)) {
                    int fd = open(fstab->recs[top_idx].key_loc, O_WRONLY, 0644);
                    if (fd >= 0) {
                        INFO("%s(): also wipe %s\n", __func__, fstab->recs[top_idx].key_loc);
                        wipe_block_device(fd, get_file_size(fd));
                        close(fd);
                    } else {
                        ERROR("%s(): %s wouldn't open (%s)\n", __func__,
                              fstab->recs[top_idx].key_loc, strerror(errno));
                    }
                }
                if (fs_mgr_do_format(&fstab->recs[top_idx]) == 0) {
                    /* Let's replay the mount actions. */
                    i = top_idx - 1;
                    continue;
                } else {
                    ERROR("%s(): Format failed. Suggest recovery...\n", __func__);
                    encryptable = FS_MGR_MNTALL_DEV_NEEDS_RECOVERY;
                    continue;
                }
            }
            if (mret && mount_errno != EBUSY && mount_errno != EACCES &&
                fs_mgr_is_encryptable(&fstab->recs[attempted_idx])) {
                if (wiped) {
                    ERROR("%s(): %s is wiped and %s %s is encryptable. Suggest recovery...\n", __func__,
                          fstab->recs[attempted_idx].blk_device, fstab->recs[attempted_idx].mount_point,
                          fstab->recs[attempted_idx].fs_type);
                    encryptable = FS_MGR_MNTALL_DEV_NEEDS_RECOVERY;
                    continue;
                } else {
                    /* Need to mount a tmpfs at this mountpoint for now, and set
                     * properties that vold will query later for decrypting
                     */
                    ERROR("%s(): possibly an encryptable blkdev %s for mount %s type %s )\n", __func__,
                          fstab->recs[attempted_idx].blk_device, fstab->recs[attempted_idx].mount_point,
                          fstab->recs[attempted_idx].fs_type);
                    if (fs_mgr_do_tmpfs_mount(fstab->recs[attempted_idx].mount_point) < 0) {
                        ++error_count;
                        continue;
                    }
                }
                encryptable = FS_MGR_MNTALL_DEV_MIGHT_BE_ENCRYPTED;
            } else {
                if (fs_mgr_is_nofail(&fstab->recs[attempted_idx])) {
                    ERROR("Ignoring failure to mount an un-encryptable or wiped partition on"
                           "%s at %s options: %s error: %s\n",
                           fstab->recs[attempted_idx].blk_device, fstab->recs[attempted_idx].mount_point,
                           fstab->recs[attempted_idx].fs_options, strerror(mount_errno));
                } else {
                    ERROR("Failed to mount an un-encryptable or wiped partition on"
                           "%s at %s options: %s error: %s\n",
                           fstab->recs[attempted_idx].blk_device, fstab->recs[attempted_idx].mount_point,
                           fstab->recs[attempted_idx].fs_options, strerror(mount_errno));
                    ++error_count;
                }
                continue;
            }
        }
    }

    if (error_count) {
        return -1;
    } else {
        return encryptable;
    }
}
```
8.[  0% 53/10500] target Executable: vold (out/target/...d855/obj/EXECUTABLES/vold_intermediates/LINKED/vold)
FAILED: /bin/bash -c "prebuilts/misc/linux-x86/ccache/ccache prebuilts/clang/host/linux-x86/clang-2690385/bin/clang++ -pie -nostdlib -Bdynamic -Wl,-dynamic-linker,/system/bin/linker -Wl,--gc-sections -Wl,-z,nocopyreloc  -Lout/target/product/d855/obj/lib -Wl,-rpath-link=out/target/product/d855/obj/lib out/target/product/d855/obj/lib/crtbegin_dynamic.o             out/target/product/d855/obj/EXECUTABLES/vold_intermediates/vold.o        -Wl,--whole-archive   -Wl,--no-whole-archive   out/target/product/d855/obj/STATIC_LIBRARIES/libvold_intermediates/libvold.a out/target/product/d855/obj/STATIC_LIBRARIES/libbootloader_message_intermediates/libbootloader_message.a out/target/product/d855/obj/STATIC_LIBRARIES/libfs_mgr_intermediates/libfs_mgr.a out/target/product/d855/obj/STATIC_LIBRARIES/libfec_intermediates/libfec.a out/target/product/d855/obj/STATIC_LIBRARIES/libfec_rs_intermediates/libfec_rs.a out/target/product/d855/obj/STATIC_LIBRARIES/libext4_utils_static_intermediates/libext4_utils_static.a out/target/product/d855/obj/STATIC_LIBRARIES/libsparse_static_intermediates/libsparse_static.a out/target/product/d855/obj/STATIC_LIBRARIES/libsquashfs_utils_intermediates/libsquashfs_utils.a out/target/product/d855/obj/STATIC_LIBRARIES/libscrypt_static_intermediates/libscrypt_static.a out/target/product/d855/obj/STATIC_LIBRARIES/libbatteryservice_intermediates/libbatteryservice.a out/target/product/d855/obj/STATIC_LIBRARIES/libext2_blkid_intermediates/libext2_blkid.a out/target/product/d855/obj/STATIC_LIBRARIES/libext2_uuid_static_intermediates/libext2_uuid_static.a out/target/product/d855/obj/STATIC_LIBRARIES/libmincrypt_intermediates/libmincrypt.a out/target/product/d855/obj/STATIC_LIBRARIES/libz_intermediates/libz.a out/target/product/d855/obj/STATIC_LIBRARIES/libunwind_llvm_intermediates/libunwind_llvm.a out/target/product/d855/obj/STATIC_LIBRARIES/libcompiler_rt-extras_intermediates/libcompiler_rt-extras.a   prebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/bin/../lib/gcc/arm-linux-androideabi/4.9/../../../../arm-linux-androideabi/lib/libatomic.a prebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/bin/../lib/gcc/arm-linux-androideabi/4.9/libgcc.a -lsysutils -lbinder -lcutils -llog -ldiskconfig -llogwrap -lf2fs_sparseblock -lselinux -lutils -lhardware_legacy -lext4_utils -lcrypto -lhardware -lsoftkeymaster -lbase -lkeymaster_messages -lext2_blkid -lcryptfs_hw -lc++ -ldl -lc -lm  -o out/target/product/d855/obj/EXECUTABLES/vold_intermediates/LINKED/vold   -Wl,-z,noexecstack -Wl,-z,relro -Wl,-z,now -Wl,--build-id=md5 -Wl,--warn-shared-textrel -Wl,--fatal-warnings -Wl,--icf=safe -Wl,--hash-style=gnu -Wl,--no-undefined-version -Wl,--no-fix-cortex-a8    -target arm-linux-androideabi -Bprebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/arm-linux-androideabi/bin   -Wl,--exclude-libs,libunwind_llvm.a -Wl,--no-undefined out/target/product/d855/obj/lib/crtend_android.o"
system/vold/cryptfs.c:3507: error: undefined reference to 'clear_hw_device_encryption_key'
system/vold/cryptfs.c:154: error: undefined reference to 'should_use_keymaster'
clang++: error: linker command failed with exit code 1 (use -v to see invocation)
Solution:
in file: device/qcom/common/cryptfs_hw/cryptfs_hw.c and device/lge/g3-common/cryptfs_hw/cryptfs_hw.c
add following function
```c
unsigned int clear_hw_device_encryption_key()
{
    int err = -1;
    int rc = 0;
    SLOGI("Clear HW disk encryption key");
    if (load_qseecom_library()) {
        err = qseecom_wipe_key(1);
    }
    if (!err) rc = 1;
    return rc;
}
int should_use_keymaster()
{
    /* HW FDE key would be tied to keymaster only if:
     * New Keymaster is available
     * keymaster partition exists on the device
     */
    int rc = 0;
    if (get_keymaster_version() != KEYMASTER_MODULE_API_VERSION_1_0) {
        SLOGI("Keymaster version is not 1.0");
        return rc;
    }

    if (access(KEYMASTER_PARTITION_NAME, F_OK) == -1) {
        SLOGI("Keymaster partition does not exists");
        return rc;
    }

    return 1;
}
```
Edit makefile: device/lge/g3-common/cryptfs_hw/Android.mk
with new lines'+'
```make
commonSharedLibraries := \
                        libcutils \
                        libutils \
                        libdl    \
+                       libhardware
+ commonIncludes := \
+                 hardware/libhardware/include/hardware/
```

9. [  0% 53/10416] target thumb C: libcry.../lge/g3-common/cryptfs_hw/cryptfs_hw.c
FAILED: /bin/bash -c "(PWD=/proc/self/cwd prebuilts/misc/linux-x86/ccache/ccache prebuilts/clang/host/linux-x86/clang-2690385/bin/clang -I hardware/libhardware/include/hardware/ -I device/lge/g3-common/cryptfs_hw -I out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates -I out/target/product/d855/gen/SHARED_LIBRARIES/libcryptfs_hw_intermediates -I libnativehelper/include/nativehelper \$(cat out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates/import_includes) -isystem system/core/include -isystem system/media/audio/include -isystem hardware/libhardware/include -isystem hardware/libhardware_legacy/include -isystem hardware/ril/include -isystem libnativehelper/include -isystem frameworks/native/include -isystem frameworks/native/opengl/include -isystem frameworks/av/include -isystem frameworks/base/include -isystem out/target/product/d855/obj/include -isystem bionic/libc/arch-arm/include -isystem bionic/libc/include -isystem bionic/libc/kernel/uapi -isystem bionic/libc/kernel/common -isystem bionic/libc/kernel/uapi/asm-arm -isystem bionic/libm/include -isystem bionic/libm/include/arm -c    -fno-exceptions -Wno-multichar -msoft-float -ffunction-sections -fdata-sections -funwind-tables -fstack-protector-strong -Wa,--noexecstack -Werror=format-security -D_FORTIFY_SOURCE=2 -fno-short-enums -no-canonical-prefixes -mcpu=cortex-a15 -D__ARM_FEATURE_LPAE=1 -mfloat-abi=softfp -mfpu=neon -DANDROID -fmessage-length=0 -W -Wall -Wno-unused -Winit-self -Wpointer-arith -Werror=return-type -Werror=non-virtual-dtor -Werror=address -Werror=sequence-point -Werror=date-time -DNDEBUG -g -Wstrict-aliasing=2 -DNDEBUG -UDEBUG  -D__compiler_offsetof=__builtin_offsetof -Werror=int-conversion -Wno-reserved-id-macro -Wno-format-pedantic -Wno-unused-command-line-argument -fcolor-diagnostics -nostdlibinc  -mcpu=krait -mfpu=neon-vfpv4 -target arm-linux-androideabi    -target arm-linux-androideabi -Bprebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/arm-linux-androideabi/bin    -std=gnu99     -mthumb -Os -fomit-frame-pointer -fno-strict-aliasing   -fPIC -D_USING_LIBCXX   -Werror=int-to-pointer-cast -Werror=pointer-to-int-cast  -Werror=address-of-temporary -Werror=null-dereference -Werror=return-type  -MD -MF out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates/cryptfs_hw.d -o out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates/cryptfs_hw.o device/lge/g3-common/cryptfs_hw/cryptfs_hw.c ) && (cp out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates/cryptfs_hw.d out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates/cryptfs_hw.P; sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\\\\$//' -e '/^\$/ d' -e 's/\$/ :/' < out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates/cryptfs_hw.d >> out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates/cryptfs_hw.P; rm -f out/target/product/d855/obj/SHARED_LIBRARIES/libcryptfs_hw_intermediates/cryptfs_hw.d )"
device/lge/g3-common/cryptfs_hw/cryptfs_hw.c:226:5: error: conflicting types for 'update_hw_device_encryption_key'
int update_hw_device_encryption_key(const char* oldpw, const char* newpw, const char* enc_mode)
    ^
device/lge/g3-common/cryptfs_hw/cryptfs_hw.h:37:5: note: previous declaration is here
int update_hw_device_encryption_key(const char*, const char*);
    ^
device/lge/g3-common/cryptfs_hw/cryptfs_hw.c:350:5: error: redefinition of 'should_use_keymaster'
int should_use_keymaster()
    ^
device/lge/g3-common/cryptfs_hw/cryptfs_hw.c:336:5: note: previous definition is here
int should_use_keymaster()
    ^
device/lge/g3-common/cryptfs_hw/cryptfs_hw.c:362:16: error: use of undeclared identifier 'KEYMASTER_PARTITION_NAME'
    if (access(KEYMASTER_PARTITION_NAME, F_OK) == -1) {
               ^
3 errors generated.
 

Solution:
in file device/lge/g3-common/cryptfs_hw/cryptfs_hw.h 
change line:
```cpp
- int update_hw_device_encryption_key(const char*, const char*);
+ int update_hw_device_encryption_key(const char*, const char*, const char*);
+ int clear_hw_device_encryption_key();
+ unsigned int is_hw_fde_enabled(void);
+ int should_use_keymaster();
```
in file device/lge/g3-common/cryptfs_hw/cryptfs_hw.c
change lines:
```cpp
+ #define KEYMASTER_PARTITION_NAME "/dev/block/bootdevice/by-name/keymaster"
- int should_use_keymaster()
- {
-    /* HW FDE key would be tied to keymaster only if:
-     * New Keymaster is available
-     * keymaster partition exists on the device
-     */
-    int rc = 0;
-    if (get_keymaster_version() != KEYMASTER_MODULE_API_VERSION_1_0) {
-        SLOGI("Keymaster version is not 1.0");
-        return rc;
-    }
-    return 1;
-}
```
10. [  0% 40/10389] target thumb C++: came...lge/g3-common/camera/CameraWrapper.cpp
FAILED: /bin/bash -c "(PWD=/proc/self/cwd prebuilts/misc/linux-x86/ccache/ccache prebuilts/clang/host/linux-x86/clang-2690385/bin/clang++ -I system/media/camera/include -I device/lge/g3-common/camera -I out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates -I out/target/product/d855/gen/SHARED_LIBRARIES/camera.msm8974_intermediates -I libnativehelper/include/nativehelper \$(cat out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/import_includes) -isystem system/core/include -isystem system/media/audio/include -isystem hardware/libhardware/include -isystem hardware/libhardware_legacy/include -isystem hardware/ril/include -isystem libnativehelper/include -isystem frameworks/native/include -isystem frameworks/native/opengl/include -isystem frameworks/av/include -isystem frameworks/base/include -isystem out/target/product/d855/obj/include -isystem bionic/libc/arch-arm/include -isystem bionic/libc/include -isystem bionic/libc/kernel/uapi -isystem bionic/libc/kernel/common -isystem bionic/libc/kernel/uapi/asm-arm -isystem bionic/libm/include -isystem bionic/libm/include/arm -c    -fno-exceptions -Wno-multichar -msoft-float -ffunction-sections -fdata-sections -funwind-tables -fstack-protector-strong -Wa,--noexecstack -Werror=format-security -D_FORTIFY_SOURCE=2 -fno-short-enums -no-canonical-prefixes -mcpu=cortex-a15 -D__ARM_FEATURE_LPAE=1 -mfloat-abi=softfp -mfpu=neon -DANDROID -fmessage-length=0 -W -Wall -Wno-unused -Winit-self -Wpointer-arith -Werror=return-type -Werror=non-virtual-dtor -Werror=address -Werror=sequence-point -Werror=date-time -DNDEBUG -g -Wstrict-aliasing=2 -DNDEBUG -UDEBUG  -D__compiler_offsetof=__builtin_offsetof -Werror=int-conversion -Wno-reserved-id-macro -Wno-format-pedantic -Wno-unused-command-line-argument -fcolor-diagnostics -nostdlibinc  -mcpu=krait -mfpu=neon-vfpv4 -target arm-linux-androideabi    -target arm-linux-androideabi -Bprebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/arm-linux-androideabi/bin    -fvisibility-inlines-hidden -Wsign-promo  -Wno-inconsistent-missing-override -nostdlibinc  -target arm-linux-androideabi   -mthumb -Os -fomit-frame-pointer -fno-strict-aliasing  -fno-rtti -fPIC -D_USING_LIBCXX -std=gnu++14  -Werror=int-to-pointer-cast -Werror=pointer-to-int-cast  -Werror=address-of-temporary -Werror=null-dereference -Werror=return-type    -MD -MF out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.d -o out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.o device/lge/g3-common/camera/CameraWrapper.cpp ) && (cp out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.d out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.P; sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\\\\$//' -e '/^\$/ d' -e 's/\$/ :/' < out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.d >> out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.P; rm -f out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.d )"
device/lge/g3-common/camera/CameraWrapper.cpp:150:43: error: no member named 'KEY_SUPPORTED_ISO_MODES' in 'android::CameraParameters'; did you mean 'KEY_SUPPORTED_FLASH_MODES'?
    params.set(android::CameraParameters::KEY_SUPPORTED_ISO_MODES, iso_values[id]);
               ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~
                                          KEY_SUPPORTED_FLASH_MODES
frameworks/av/include/camera/CameraParameters.h:251:23: note: 'KEY_SUPPORTED_FLASH_MODES' declared here
    static const char KEY_SUPPORTED_FLASH_MODES[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:153:46: error: no member named 'KEY_LGE_ISO_MODE' in 'android::CameraParameters'
    if(params.get(android::CameraParameters::KEY_LGE_ISO_MODE)) {
                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~^
device/lge/g3-common/camera/CameraWrapper.cpp:154:57: error: no member named 'KEY_LGE_ISO_MODE' in 'android::CameraParameters'
        isoMode = params.get(android::CameraParameters::KEY_LGE_ISO_MODE);
                             ~~~~~~~~~~~~~~~~~~~~~~~~~~~^
device/lge/g3-common/camera/CameraWrapper.cpp:158:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "auto");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:160:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO50");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:162:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO100");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:164:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO150");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:166:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO200");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:168:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO250");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:170:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO300");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:172:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO350");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:174:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO400");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:176:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO450");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:178:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO500");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:180:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO600");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:182:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO700");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:184:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO800");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:186:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO1000");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
device/lge/g3-common/camera/CameraWrapper.cpp:188:51: error: no member named 'KEY_ISO_MODE' in 'android::CameraParameters'; did you mean 'KEY_FLASH_MODE'?
            params.set(android::CameraParameters::KEY_ISO_MODE, "ISO1500");
                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~
                                                  KEY_FLASH_MODE
frameworks/av/include/camera/CameraParameters.h:248:23: note: 'KEY_FLASH_MODE' declared here
    static const char KEY_FLASH_MODE[];
                      ^
fatal error: too many errors emitted, stopping now [-ferror-limit=]
20 errors generated.
[  0% 40/10389] Building with Jack: ou...nts-archive_intermediates/classes.jack
ninja: build stopped: subcommand failed.
build/core/ninja.mk:148: recipe for target 'ninja_wrapper' failed
make: *** [ninja_wrapper] Error 1

#### make failed to build some targets (36 seconds) ####

Solution: frameworks/av/include/camera/CameraParameters.h
In file:
Add lines
```cpp
+ static const char KEY_SUPPORTED_ISO_MODES[];
+ static const char KEY_LGE_ISO_MODE[];
+ static const char KEY_ISO_MODE[];
```
11. [  2% 278/10407] target thumb C++: camera.msm89...= device/lge/g3-common/camera/CameraWrapper.cpp
FAILED: /bin/bash -c "(PWD=/proc/self/cwd prebuilts/misc/linux-x86/ccache/ccache prebuilts/clang/host/linux-x86/clang-2690385/bin/clang++ -I system/media/camera/include -I device/lge/g3-common/camera -I out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates -I out/target/product/d855/gen/SHARED_LIBRARIES/camera.msm8974_intermediates -I libnativehelper/include/nativehelper \$(cat out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/import_includes) -isystem system/core/include -isystem system/media/audio/include -isystem hardware/libhardware/include -isystem hardware/libhardware_legacy/include -isystem hardware/ril/include -isystem libnativehelper/include -isystem frameworks/native/include -isystem frameworks/native/opengl/include -isystem frameworks/av/include -isystem frameworks/base/include -isystem out/target/product/d855/obj/include -isystem bionic/libc/arch-arm/include -isystem bionic/libc/include -isystem bionic/libc/kernel/uapi -isystem bionic/libc/kernel/common -isystem bionic/libc/kernel/uapi/asm-arm -isystem bionic/libm/include -isystem bionic/libm/include/arm -c    -fno-exceptions -Wno-multichar -msoft-float -ffunction-sections -fdata-sections -funwind-tables -fstack-protector-strong -Wa,--noexecstack -Werror=format-security -D_FORTIFY_SOURCE=2 -fno-short-enums -no-canonical-prefixes -mcpu=cortex-a15 -D__ARM_FEATURE_LPAE=1 -mfloat-abi=softfp -mfpu=neon -DANDROID -fmessage-length=0 -W -Wall -Wno-unused -Winit-self -Wpointer-arith -Werror=return-type -Werror=non-virtual-dtor -Werror=address -Werror=sequence-point -Werror=date-time -DNDEBUG -g -Wstrict-aliasing=2 -DNDEBUG -UDEBUG  -D__compiler_offsetof=__builtin_offsetof -Werror=int-conversion -Wno-reserved-id-macro -Wno-format-pedantic -Wno-unused-command-line-argument -fcolor-diagnostics -nostdlibinc  -mcpu=krait -mfpu=neon-vfpv4 -target arm-linux-androideabi    -target arm-linux-androideabi -Bprebuilts/gcc/linux-x86/arm/arm-linux-androideabi-4.9/arm-linux-androideabi/bin    -fvisibility-inlines-hidden -Wsign-promo  -Wno-inconsistent-missing-override -nostdlibinc  -target arm-linux-androideabi   -mthumb -Os -fomit-frame-pointer -fno-strict-aliasing  -fno-rtti -fPIC -D_USING_LIBCXX -std=gnu++14  -Werror=int-to-pointer-cast -Werror=pointer-to-int-cast  -Werror=address-of-temporary -Werror=null-dereference -Werror=return-type    -MD -MF out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.d -o out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.o device/lge/g3-common/camera/CameraWrapper.cpp ) && (cp out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.d out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.P; sed -e 's/#.*//' -e 's/^[^:]*: *//' -e 's/ *\\\\\$//' -e '/^\$/ d' -e 's/\$/ :/' < out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.d >> out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.P; rm -f out/target/product/d855/obj/SHARED_LIBRARIES/camera.msm8974_intermediates/CameraWrapper.d )"

device/lge/g3-common/camera/CameraWrapper.cpp:242:43: error: no member named 'KEY_LGE_CAMERA' in 'android::CameraParameters'
    params.set(android::CameraParameters::KEY_LGE_CAMERA, (id == 0 && is4k(params)) ? "1" : "0");
               ~~~~~~~~~~~~~~~~~~~~~~~~~~~^
1 error generated.
[  2% 278/10407] Building with Jack: out/target...RARIES/services.core_intermediates/classes.jack
ninja: build stopped: subcommand failed.
build/core/ninja.mk:148: recipe for target 'ninja_wrapper' failed
make: *** [ninja_wrapper] Error 1

Solution:
Add line to file : frameworks/av/include/Camera/CameraParameters.h
```cpp
+ static const char KEY_LGE_CAMERA[];
```
