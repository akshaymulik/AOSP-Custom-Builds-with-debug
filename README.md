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
+ appdomain -shell userdebug_or_eng(`-su -sudaemon -qti-testscripts')
```
