---
published: true
layout: post
title: WSL2 관련 정리
date: 2020-07-30T00:00:00.000Z
author: Bongjun Lee
categories: wsl
tags: wsl2 linux
cover: /assets/instacode.png
---
## WSL2 설치 관련
### WSL 최신 커널 설치
[CUDA on WSL User Guide](https://docs.nvidia.com/cuda/wsl-user-guide/index.html)를 적용해 보기 위해 시도 하던중 WSL2 Kernel 4.19.121+ 조건이 있어서 적용을 해보려고 했으나 [WSL 2 Linux 커널 업데이트](https://docs.microsoft.com/ko-kr/windows/wsl/wsl2-kernel)에서 다운받을 수 있는 최신 WSL2 Linux 커널 업데이트 패키지는 4.19.104 버전임.

업데이트를 하기 위해서는 두가지 방법이 있음

Windows Update를 통한 방법
- 이 방법으로 WSL2 Kernel을 업데이트 하기 위해서는 윈도우 업데이트 설정이 필요함
- 설정 - 업데이트 및 보안 - Windows 업데이트 - 고급 옵션 - "Windows를 업데이트 할 때 다른 Microsoft 제품에 대한 업데이트 받기"설정을 켬

wsl --update를 통한 방법
- 이 방법을 위해서는 Windows 참가자 프로그램을 통해 [윈도우 10 Build 20150](https://blogs.windows.com/windowsexperience/2020/06/17/announcing-windows-10-insider-preview-build-20150/) 이상을 설치 해야 함
- 콘솔 창에서 'wsl --update'를 실행하면 wsl2 kernel 최신 버전을 자동으로 다운로드 받아 설치 됨
- 참고 : [GPU Compute, WSL Install and WSL Update arrive in the latest Insider build for the Windows Subsystem for Linux](https://devblogs.microsoft.com/commandline/gpu-compute-wsl-install-and-wsl-update-arrive-in-the-windows-insiders-fast-ring-for-the-windows-subsystem-for-linux/)



## WSL2 성능을 위한 몇가지 설정


## 더 빠른 성능을 위해 Linux 파일 시스템 사용
그동안 wsl을 통해 cross compile 환경을 구축해 개발을 해오고 있었는데, wsl2에서 file i/o의 성능이 훨신 좋아졌다는 소식을 듣고 wsl2에 개발 환경을 셋팅 후 빌드 해보았는데 오히려 더 느려짐.

그 이유는 아래 [WSL 1과 WSL 2 비교](https://docs.microsoft.com/ko-KR/windows/wsl/compare-versions#use-the-linux-file-system-for-faster-performance)를 통해 확인 할수 있는데, 환경에 따라 WSL1, WSL2 성능이 달라 질수 있음

* WSL1이 더 빠른 경우
 * wsl에서 윈도우의 파일 시스템에 있는 파일들을 접근하고 빌드 하는 경우
 * /mnt/c 로 시작하는 폴더의 파일들이 이에 해당됨
 * 지금까지 이 환경에서 개발을 해왔는데, 윈도우에서 바로 프로젝트 폴더로 접근 가능해서 vscode, beyond compare등으로 열어서 편집등 편리해서 임.
 * wsl2에서는 이런 환경에서 오히려 속도가 더 느림. (wsl2에서 /mnt/c로 된 경로를 접근하면 wsl 보다 더 느림)

* WSL2가 더 빠른 경우
 * wsl2의 루트 디렉토리( ~/ 폴더)에서 작업 할 경우 wsl1 보다 file i/o가 훨신 빨라짐
 * wsl2의 루트 디렉토리는 윈도우의 파일 탐색기에 Linux로 마운드 되어 있음
 * 혹은 \\wsl$\Ubuntu\home 와 같이 접근 가능함


## . Wslconfig를 사용 하 여 전역 옵션 구성
wsl2의 경우 완전한 VM으로 실행되기 때문에 성능적인 측면에서 몇가지 제약이 기본적으로 셋팅되는데, .wslconfig를 통해 이런 성능적인 옵션을 구성할 수 있다. ([참고](https://docs.microsoft.com/ko-kr/windows/wsl/wsl-config#configure-global-options-with-wslconfig))

C:\Users\<yourUserName>\.wslconfig 파일을 만들고 아래와 같이 설정하면 됨.


```
[wsl2]
kernel=
memory=
processors=
```