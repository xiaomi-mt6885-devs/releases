Copyright (C) 2023 [AngelaCooljx](https://github.com/AngelaCooljx)

# 如何为 Redmi K30 Ultra 编译 Android 14 ROM (以 lineage-21 为例)

## 准备环境
- 最低配置 16G 物理内存 + 设置至少 32G SWAP ，至少 350G 空闲 SSD 容量，CPU 无要求
- 环境搭建依赖安装参考 lineage wiki 如 https://wiki.lineageos.org/devices/rosemary/build/variant1/ [Ubuntu/debian系]  
- Fedora/redhat系依赖安装参考 https://gist.github.com/axxx007xxxz/60fea50f4b123e0163f972d1709068c2 或 https://github.com/tripLr/Fedora-Server-Tools/blob/master/fedora_setup_aosp.sh  
- Arch Linux 用户参考 archlinux wiki: https://wiki.archlinux.org/title/Android#Building

## 如何编译
1. 拉源码/Pull source code
- ROM:  
`mkdir [YOUR ROM SOURCE] && cd [YOUR ROM SOURCE]`  
`repo init -u https://github.com/lineage/android.git -b lineage-21 --git-lfs --depth=1`  
`repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags`  
- Device Configuration:  
`git clone https://github.com/xiaomi-mt6885-devs/android_device_xiaomi_cezanne-old -b qpr3 --single-branch --depth=1 device/xiaomi/cezanne`
- Kernel Source:  
`git clone https://github.com/xiaomi-mt6885-devs/android_kernel_xiaomi_mt6885.git -b cgroup-v2 --single-branch --depth=1 kernel/xiaomi/mt6885`
- Mtk Hardware:  
`git clone https://github.com/xiaomi-mt6885-devs/android_hardware_mediatek -b fourteen hardware/mediatek`
- Sepolicy_vndr:  
`git clone https://github.com/xiaomi-mt6885-devs/android_device_mediatek_sepolicy_vndr -b arrow-14.0 --single-branch device/mediatek/sepolicy_vndr`

2. 手动下载 MiuiCamera.apk 替换 device/xiaomi/cezanne/MiuiCamera/system/priv-app/MiuiCamera.apk/Replace MiuiCamera.apk  
https://onedrive.live.com/?cid=5A43C412343CDA85&id=5A43C412343CDA85%21se8ce2449639945618af8d0d0106cd011&parId=root&o=OneUp

3. 使用 [Dumpyara](https://github.com/AndroidDumps/dumpyara) 等工具得到 [12.5.11.0 DUMP DIR] 目录或从本机 MIUI 12.5.11.0 提取 Vendor Blob ，使用 extract-files.sh 脚本生成 vendor tree/Extract Vendor Blob  
`cd [YOUR ROM SOURCE]`  
以下方式二选一：  
- 从 dump 目录：`bash device/xiaomi/cezanne/extract-files.sh [12.5.11.0 DUMP DIR]`
- 连接本机 MIUI 12.5.11.0 使用 adb：`bash device/xiaomi/cezanne/extract-files.sh`

4. 修补源码/Patch source  
  device configuration 目录下的 patches/frameworks_base/ 内所有 patch 拷贝到源码对应目录 frameworks/base，在对应目录使用 `git am 000*.patch` 打补丁

5. 初始化环境/Initialize environment  
`cd [YOUR ROM SOURCE]`  
`source build/envsetup.sh`  

6. 开始编译/Start building  
`cd [YOUR ROM SOURCE]`  
`lunch lineage_cezanne-ap2a-user`  
`make bacon`
