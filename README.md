# cmi-p404-docker-enabled
xiaomi 10 pro rom with docker enabled

# 中文:

Goto: https://blog.csdn.net/whgjjim/article/details/134992239

# English:

## 0. background

This article provides a kernel compilation method for the Xiaomi 10 Pro phone(cmi).

I have uploaded a kernel compilation docker image (17380582683/wh: v1.1.0) created from the P404-project. You can manually modify and configure the kernel in the docker. I try to build a complete ROM zip package , but failed. You can continue to delve deeper into it.

P404-project：https://github.com/P-404/android_manifest  
P404 cmi rom build：https://github.com/P404-Devices/device_xiaomi_cmi  
P404  origin cmi rom：https://github.com/P404-Devices/device_xiaomi_cmi/releases/download/5.0/project-404-5.0-20220813-104358-SHINKA-cmi.zip  

## 1. Environment preparation

A x64 computer with at least 300GB of free disk rom and 32GB of RAM is required, and dockerd should be installed before.

## 2. Pull Docker Image

pull command：`docker pull  17380582683/wh:v.1.0`  
image size：35.52GB，size after decompressed：78GB  
image id：c250097005b8  
v.1.0: digest: sha256:57cc3e41436e523551a0855f3c3d755176578e6fc423ea8b36519e7905e14367  

## 3. Create the container and then enter into it

```sh
~:/ docker  run  -itd  --name cmi  17380582683/wh:v.1.0  bash
WARNING: The requested image's platform (linux/arm64/v8) does not match the detected host platform 
    (linux/amd64) and no specific platform was requested
362b7cad732c9471350d697eb5137f00a8108ed7b69741bd0ba749764f0bfca3
~:/ docker exec -it cmi bash
```

The warning above can be ignored directly. It is because my image was uploaded in the environment of the Xiaomi 10 Pro phone, so It considered to be an arm64 platform image. In fact, this image can only run on x64 machines.

## 4. Modify Android kernel parameters

tips. You can skip this step and start compiling the Android kernel directly, If you don't want to mdify the kernel.

Method 1: modify the kernel configuration file cmi_defconfig

tips. It's best to synchronize the configuration files of two kernels and modify them together

```sh
vi  /root/p-404/kernel/xiaomi/sm8250/arch/arm64/configs/cmi_defconfig
vi  /root/p-404/kernel/xiaomi/sm8250-bak/arch/arm64/configs/cmi_defconfig
```

Method 2: Modify a kernel configuration file in a graphical manner firstly, and then synchronously overwrite it with another.

1. use this commands to create a new config from cmi_defconfig.

```sh
export ARCH=arm64
cd  /root/p-404/kernel/xiaomi/sm8250;
make cmi_defconfig
```

2. Enter the kernel parameter menu and start modifying the kernel configuration file. Use the up, down and enter keyboard to move, select(remember to select Save when exiting)

```sh
make menuconfig
```

3. After modification, replace the original cmi_defconfig with .config file.

```sh
cp .config arch/arm64/configs/cmi_defconfig
make clean
```

4. Synchronize cmi_defconfig to another kernel.

```sh
cp cmi_defconfig /root/p-404/kernel/xiaomi/sm8250-bak/arch/arm64/configs/cmi_defconfig
```

## 5. Here is the modification of kernel config  to support docker

```diff
diff --git a/arch/arm64/configs/cmi_defconfig b/arch/arm64/configs/cmi_defconfig
index a0a7ffd4a906..d7efc88aedb9 100644
--- a/arch/arm64/configs/cmi_defconfig
+++ b/arch/arm64/configs/cmi_defconfig
@@ -1,3 +1,82 @@
+CONFIG_NAMESPACES=y
+CONFIG_NET_NS=y
+CONFIG_PID_NS=y
+CONFIG_IPC_NS=y
+CONFIG_UTS_NS=y
+CONFIG_CGROUPS=y
+CONFIG_CGROUP_CPUACCT=y
+CONFIG_CGROUP_DEVICE=y
+CONFIG_CGROUP_FREEZER=y
+CONFIG_CGROUP_SCHED=y
+CONFIG_CPUSETS=y
+CONFIG_MEMCG=y
+CONFIG_KEYS=y
+CONFIG_VETH=y
+CONFIG_BRIDGE=y
+CONFIG_BRIDGE_NETFILTER=y
+CONFIG_IP_NF_FILTER=y
+CONFIG_IP_NF_TARGET_MASQUERADE=y
+CONFIG_NETFILTER_XT_MATCH_ADDRTYPE=y
+CONFIG_NETFILTER_XT_MATCH_CONNTRACK=y
+CONFIG_NETFILTER_XT_MATCH_IPVS=y
+CONFIG_NETFILTER_XT_MARK=y
+CONFIG_NETFILTER_XT_SET=y
+CONFIG_IP_NF_NAT=y
+CONFIG_NF_NAT=y
+CONFIG_POSIX_MQUEUE=y
+CONFIG_NF_NAT_IPV4=y
+CONFIG_NF_NAT_NEEDED=y
+CONFIG_CGROUP_BPF=y
+CONFIG_USER_NS=y
+CONFIG_SECCOMP=y
+CONFIG_SECCOMP_FILTER=y
+CONFIG_CGROUP_PIDS=y
+CONFIG_MEMCG_SWAP=y
+CONFIG_MEMCG_SWAP_ENABLED=y
+CONFIG_IOSCHED_CFQ=y
+CONFIG_CFQ_GROUP_IOSCHED=y
+CONFIG_BLK_CGROUP=y
+CONFIG_BLK_DEV_THROTTLING=y
+CONFIG_CGROUP_PERF=y
+CONFIG_CGROUP_HUGETLB=y
+CONFIG_NET_CLS_CGROUP=y
+CONFIG_CGROUP_NET_PRIO=y
+CONFIG_CFS_BANDWIDTH=y
+CONFIG_FAIR_GROUP_SCHED=y
+CONFIG_RT_GROUP_SCHED=y
+CONFIG_IP_NF_TARGET_REDIRECT=y
+CONFIG_IP_VS=y
+CONFIG_IP_VS_NFCT=y
+CONFIG_IP_VS_PROTO_TCP=y
+CONFIG_IP_VS_PROTO_UDP=y
+CONFIG_IP_VS_RR=y
+CONFIG_SECURITY_SELINUX=y
+CONFIG_SECURITY_APPARMOR=y
+CONFIG_EXT4_FS=y
+CONFIG_EXT4_FS_POSIX_ACL=y
+CONFIG_EXT4_FS_SECURITY=y
+CONFIG_VXLAN=y CONFIG_BRIDGE_VLAN_FILTERING=y
+CONFIG_CRYPTO=y CONFIG_CRYPTO_AEAD=y
+CONFIG_CRYPTO_GCM=y
+CONFIG_CRYPTO_SEQIV=y
+CONFIG_CRYPTO_GHASH=y CONFIG_XFRM=y
+CONFIG_XFRM_USER=y
+CONFIG_XFRM_ALGO=y
+CONFIG_INET_ESP=y
+CONFIG_INET_XFRM_MODE_TRANSPORT=y
+CONFIG_IPVLAN=y
+CONFIG_MACVLAN=y
+CONFIG_DUMMY=y
+CONFIG_NF_NAT_FTP=y
+CONFIG_NF_CONNTRACK_FTP=y
+CONFIG_NF_NAT_TFTP=y
+CONFIG_NF_CONNTRACK_TFTP=y
+CONFIG_AUFS_FS=y
+CONFIG_BTRFS_FS=y
+CONFIG_BTRFS_FS_POSIX_ACL=y
+CONFIG_BLK_DEV_DM=y
+CONFIG_DM_THIN_PROVISIONING=y
+CONFIG_OVERLAY_FS=y
 CONFIG_LOCALVERSION="-perf"
 # CONFIG_LOCALVERSION_AUTO is not set
 CONFIG_AUDIT=y
@@ -236,6 +315,24 @@ CONFIG_NETFILTER_XT_MATCH_STATISTIC=y
 CONFIG_NETFILTER_XT_MATCH_STRING=y
 CONFIG_NETFILTER_XT_MATCH_TIME=y
 CONFIG_NETFILTER_XT_MATCH_U32=y
+CONFIG_IP_SET=y
+CONFIG_IP_SET_MAX=256
+CONFIG_IP_SET_BITMAP_IP=y
+CONFIG_IP_SET_BITMAP_IPMAC=y
+CONFIG_IP_SET_BITMAP_PORT=y
+CONFIG_IP_SET_HASH_IP=y
+CONFIG_IP_SET_HASH_IPMARK=y
+CONFIG_IP_SET_HASH_IPPORT=y
+CONFIG_IP_SET_HASH_IPPORTIP=y
+CONFIG_IP_SET_HASH_IPPORTNET=y
+CONFIG_IP_SET_HASH_IPMAC=y
+CONFIG_IP_SET_HASH_MAC=y
+CONFIG_IP_SET_HASH_NETPORTNET=y
+CONFIG_IP_SET_HASH_NET=y
+CONFIG_IP_SET_HASH_NETNET=y
+CONFIG_IP_SET_HASH_NETPORT=y
+CONFIG_IP_SET_HASH_NETIFACE=y
+CONFIG_IP_SET_LIST_SET=y
 CONFIG_IP_NF_IPTABLES=y
 CONFIG_IP_NF_MATCH_AH=y
 CONFIG_IP_NF_MATCH_ECN=y
@@ -777,3 +874,5 @@ CONFIG_SCHEDSTATS=y
 CONFIG_DEBUG_LIST=y
 CONFIG_IPC_LOGGING=y
 CONFIG_DEBUG_ALIGN_RODATA=y
+CONFIG_NAMESPACES=y
+CONFIG_PID_NS=y
```

## 6. Start  kernel compiling

The current environment for compiling the kernel has a slight issue and requires to compile the kernel twice(That's why we need to modify two kernel configs previous  step). When there is time later, I may fix it.

The compiled kernel has been confirmed can be running on the cmi phone. 

Enter the container and execute the following commands to compile the kernel.
```sh
cd  /root/p-404;
./mybuild.sh;
# Then wait for compilation to fail and change the kernel path

mv  /root/p-404/kernel/xiaomi/sm8250  /root/p-404/kernel/xiaomi/sm8250-bak2
mv  /root/p-404/kernel/xiaomi/sm8250-bak  /root/p-404/kernel/xiaomi/sm8250
# The above two actions are to replace the current LineageOS kernel with the P404 Devices kernel

# compile again
./mybuild.sh;
```

tips. How to confirm if the kernel PATH is LineageOS kernel or P404 Devices kernel?

We can enter the kernel directory and execute the command `git remote - v` to see the current source kernel branch. An example is as follows:

```sh
root@67ba4c3d3c19:~/p-404/kernel/xiaomi/sm8250# git remote -v
origin https://github.com/P404-Devices/kernel_xiaomi_sm8250.git (fetch)
origin https://github.com/P404-Devices/kernel_xiaomi_sm8250.git (push)
```

## 7. Provide root access
Use the [magisk-app v26.4](https://github.com/topjohnwu/Magisk/releases/tag/v26.4)(this version tested ok) to implant root access. There are many tutorials on the Internet, roughly as follows:

1. Copy the boot image to your device.
2. Press the **Install** button in the Magisk card.
3. Choose “**Select and Patch a File**” in method, and select the boot image.
4. Start the installation, and copy the patched image to your PC using ADB:  
`adb pull /sdcard/Download/magisk_patched_[random_strings].img`

## 8. Flashing new compiled kernel
0. It's best to flashed in this ROM([project-404-5.0-20220813-104358-SHINKA-cmi.zip](https://github.com/P404-Devices/device_xiaomi_cmi/releases/download/5.0/project-404-5.0-20220813-104358-SHINKA-cmi.zip)) before, into Xiaomi 10Pro phone. 

1. Restart your phone and enter fastboot mode. There are two ways to enter fastboot mode:

        A. Connect the phone to computer by adb, and then enter `reboot recovery` in adb. The phone will automatically restart into recovery mode.
        
        B. Turn off the phone normally, and then press the power button and volume up button together to power-on the phone. It will enter into recovery mode.

2. Connect to the computer, execute the command `fastboot flash boot new boot.img`, and flash into the new compiled kernel.

3. Execute the `fastboot reboot`, and the phone will restart normally.

## 9. Recompile the kernel

If you want to recompile the kernel again, you can delete this directory. It will clean up the previous compilation. The command is:

```sh
rm  -rf  /root/p-404/out
```

## 10. The rom that has been modified to support docker

Considering that someone only need the final result ROM package and won't compile kernel. The full rom that support docker has released:  
https://github.com/hengwu0/cmi-p404-docker-enabled/releases/  
You can use the full rom, or unzip it and get boot.img from it.

## 11. Steps to install docker on your phone

Goto: https://blog.csdn.net/whgjjim/article/details/134687390 

tips. If you want to use phone as an server, your device will connect the power adapter 7\*24 hours. Which may cause battery damaged or exploded. 
But, If we handle it properly, it's like giving our server a built-in UPS. If you need support, please email me: w._heng@163.com , $50 for beer.
