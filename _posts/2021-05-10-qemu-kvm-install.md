---
layout: post
title: Qemu-KVM install
tags: [qemu, kvm, install]
---

  qemu와 kvm 설치하는 법에 대해 알아본다.
  
### KVM 설치
```
$ sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst virt-manager
```
  qemu-kvm: KVM hypervisor를 위해 hardware emulation을 제공한다.
  libvirt-daemon-system: libvirt daemon을 system service로 동작시키기 위한 파일
  libvirt-cients: virtualization platform을 관리하기 위한 소프트웨어
  bridge-utils: ethernet bridges를 구성하기 위한 command 모음
  virtinst: virtual machine을 생성하기 위한 command 모음
  virt-manager: virtual machine을 관리하기 위한 GUI interface와 command 모음
  
### KVM 실행
```
sudo virt-manager
```
  
출처: https://marcokhan.tistory.com/259
  
  
