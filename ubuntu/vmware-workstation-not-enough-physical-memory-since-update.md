# Linux内核更新后，VM14 “内存不足”问题的解决方案

- 操作系统：Ubuntu 16
- 系统版本：16.04.3 LTS
- 内核：4.13.0-26-generic

### 问题描述

系统内核更新到4.13.0-26-generic，启动虚拟机时出现如下提示信息：
```text
Not enough physical memory is available to power on this virtual machine with its configured settings.
To fix this problem, increase the amount of physical memory for all virtual machines to 2130 MB or adjust the additional memory settings to allow more virtual machine memory to be swapped.
It is possible that native applications and/or services have locked down memory which could be preventing the virtual machine from launching. Shutting down unnecessary applications or services may free enough memory to launch this virtual machine.
If you were able to power on this virtual machine on this host computer in the past, try rebooting the host computer. Rebooting may allow you to use slightly more host memory to run virtual machines.
```

### 原因分析
内核更新后，新的内核发生了一些变化，对于VMware vmmon模块，需要安装这个补丁：[https://github.com/mkubecek/vmware-host-modules/commit/770c7ffe611520ac96490d235399554c64e87d9f](https://github.com/mkubecek/vmware-host-modules/commit/770c7ffe611520ac96490d235399554c64e87d9f)

### 具体步骤

```console
cd /tmp
sudo cp /usr/lib/vmware/modules/source/vmmon.tar .
sudo tar xf vmmon.tar
sudo rm vmmon.tar
sudo wget https://raw.githubusercontent.com/mkubecek/vmware-host-modules/fadedd9c8a4dd23f74da2b448572df95666dfe12/vmmon-only/linux/hostif.c
sudo mv -f hostif.c vmmon-only/linux/hostif.c
sudo tar cf vmmon.tar vmmon-only
sudo rm -fr vmmon-only
sudo mv -f vmmon.tar /usr/lib/vmware/modules/source/vmmon.tar
sudo vmware-modconfig --console --install-all
```
