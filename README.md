## Prerequisites

* Download the [**JDK 7**](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) or [**JDK 8**](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) from Oracle and install it.
* Download & install the Android SDK, **Android 4.4W (API 20)** (for example through Android Studio’s **Android SDK Manager**)
* _Also_ download & install the Android SDK, **Android 4.4.2 (API 20)** (for example through Android Studio’s **Android SDK Manager**)
* Download the **Android NDK (= r10d)**. For example, on Mac OS you can do this with [Homebrew](http://brew.sh) with `brew install android-ndk`.

## Make standalone toolchain

    export ANDROID_NDK=/path_to_ndk_root
    export ANDROID_TOOLCHAIN=/path_to_toolchain
    $ANDROID_NDK/build/tools/make-standalone-toolchain.sh --platform=android-14 --install-dir=$ANDROID_TOOLCHAIN --toolchain=arm-linux-androideabi-clang3.5


## Build ICU

    mkdir -p linux
    cd linux
    ../source/runConfigureICU Linux CXXFLAGS='--std=c++11'
    make -j5

    export CROSS_BUILD_DIR=/path_to_linux_dir_above


    cd ../
    mkdir android-armeabi-v7a
    cd android-armeabi-v7a

    ../source/configure --prefix=$(pwd)/build \
        --host=arm-linux-androideabi \
        --enable-static --disable-shared \
        -with-cross-build=$CROSS_BUILD_DIR \
        CFLAGS='-march=armv7-a -mfloat-abi=softfp -mfpu=neon' \
        CXXFLAGS='--std=c++11 -march=armv7-a -mfloat-abi=softfp -mfpu=neon' \
        LDFLAGS='-march=armv7-a -Wl,--fix-cortex-a8' \
        CC=arm-linux-androideabi-gcc \
        CXX=arm-linux-androideabi-g++ \
        AR=arm-linux-androideabi-ar \
        --with-data-packaging=archive
    make -j5
    make install

### All is successful
