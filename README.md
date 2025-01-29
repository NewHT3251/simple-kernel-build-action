# Android Kernel Builder Action

A GitHub Action to automate the building of Android kernels.

## Features

- Setup build environment
- Supports GCC and Clang compilers
- KernelSU integration
- ccache support
- AnyKernel3 packaging
- Customizable kernel configuration

## Inputs

### Required Inputs

- `KERNEL_CLONE_CMD`: Command to clone the kernel source code.
- `KERNEL_BRANCH`: Git branch of the kernel source.
- `KERNEL_CONFIG`: Kernel configuration name (e.g., defconfig).

### Optional Inputs

- `ARCH`: Target architecture (default: `arm64`).
- `COMPILER`: Compiler to use (gcc|clang) (default: `gcc`).
- `TOOLCHAIN_CLONE_CMD`: Link to clone the compiler (default: `git clone https://github.com/djb77/aarch64-linux-android-4.9 ./toolchain/gcc`).
- `OTHER_CLANG_REPO`: Clang compiler repository (default: `https://github.com/android-linux-globals/rules_clang`).
- `ENABLE_KSU`: Enable KernelSU support (default: `false`).
- `KSU_CURL_COMMAND`: KernelSU curl command (default: `curl -LSs "https://raw.githubusercontent.com/mlm-games/KernelSU-Non-GKI/main/kernel/setup-subm.sh" | bash -s`).
- `ENABLE_CCACHE`: Enable ccache support (default: `true`).
- `OUTPUT_PACKAGE_FORMAT`: Output package format (anykernel3|boot.img) (default: `anykernel3`).
- `ANYKERNEL_URL`: URL for AnyKernel3 repository (default: `https://github.com/mlm-games/AnyKernel3`).

## Outputs

- `kernel_build`: Outputs the path to the built kernel package.

## Example Workflow

```yaml
name: Build Kernel

on:
  push:
    branches: [ main ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Build Kernel
      uses: mlm-games/simple-kernel-build-action@v1
      with:
        KERNEL_CLONE_CMD: 'git clone https://github.com/lineageos/android_kernel_xiaomi_msm8953 -b android 13.0' # PSA: directory and depth=1 are added automatically!
        KERNEL_CONFIG: 'msm8953_defconfig'
        ARCH: 'arm64'
        COMPILER: 'gcc'
        TOOLCHAIN_CLONE_CMD: 'git clone https://github.com/djb77/aarch64-linux-android-4.9 ./toolchain/gcc'
        ENABLE_KSU: 'true'
        ENABLE_CCACHE: 'true'
        OUTPUT_PACKAGE_FORMAT: 'anykernel3'

    - name: Upload build artifacts
      uses: actions/upload-artifact@v3
      with:
        name: kernel-build
        path: 'build/kernel-*'
