## Building Filament

### Prerequisites

To build Filament, you must first install the following tools:

- CMake 3.10 (or more recent)
- clang 7.0 (or more recent)
- [ninja 1.8](https://github.com/ninja-build/ninja/wiki/Pre-built-Ninja-packages) (or more recent)

To build the Java based components of the project you can optionally install (recommended):

- OpenJDK 1.8 (or more recent)

Additional dependencies may be required for your operating system. Please refer to the appropriate
section below.

Building the `rays` library (used for light baking) is optional and requires the following packages:

- embree 3.0+
- libtbb-dev

To build Filament for Android you must also install the following:

- Android Studio 3.6 or more recent
- Android SDK
- Android NDK "side-by-side" 20 or higher

### Environment variables

Make sure the environment variable `ANDROID_HOME` points to the location of your Android SDK.

By default our build system will attempt to compile the Java bindings. To do so, the environment
variable `JAVA_HOME` should point to the location of your JDK.

When building for WebGL, you'll also need to set `EMSDK`. See [WebAssembly](#webassembly).

### IDE

We recommend using CLion to develop for Filament. Simply open the root directory's CMakeLists.txt
in CLion to obtain a usable project.

### Easy build

Once the required OS specific dependencies listed below are installed, you can use the script
located in `build.sh` to build Filament easily on macOS and Linux.

This script can be invoked from anywhere and will produce build artifacts in the `out/` directory
inside the Filament source tree.

To trigger an incremental debug build:

```
$ ./build.sh debug
```

To trigger an incremental release build:

```
$ ./build.sh release
```

To trigger both incremental debug and release builds:

```
$ ./build.sh debug release
```

To install the libraries and executables in `out/debug/` and `out/release/`, add the `-i` flag.
You can force a clean build by adding the `-c` flag. The script offers more features described
by executing `build.sh -h`.

### Disabling Java builds

By default our build system will attempt to compile the Java bindings. If you wish to skip this
compilation step simply pass the `-j` flag to `build.sh`:

```
$ ./build.sh -j release
```

If you use CMake directly instead of the build script, pass `-DFILAMENT_ENABLE_JAVA=OFF`
to CMake instead.

### Filament-specific CMake Options

The following CMake options are boolean options specific to Filament:

- `FILAMENT_ENABLE_JAVA`:          Compile Java projects: requires a JDK and the JAVA_HOME env var
- `FILAMENT_ENABLE_LTO`:           Enable link-time optimizations if supported by the compiler
- `FILAMENT_BUILD_FILAMAT`:        Build filamat and JNI buildings
- `FILAMENT_SUPPORTS_METAL`:       Include the Metal backend
- `FILAMENT_SUPPORTS_VULKAN`:      Include the Vulkan backend
- `FILAMENT_GENERATE_JS_DOCS`:     Build WebGL documentation and tutorials
- `FILAMENT_INSTALL_BACKEND_TEST`: Install the backend test library so it can be consumed on iOS
- `FILAMENT_USE_EXTERNAL_GLES3`:   Experimental: Compile Filament against OpenGL ES 3
- `FILAMENT_SKIP_SAMPLES`:         Don't build sample apps

To turn an option on or off:

```
$ cd <cmake-build-directory>
$ cmake . -DOPTION=ON       # Relace OPTION with the option name, set to ON / OFF
```

Options can also be set with the CMake GUI.

### Linux

Make sure you've installed the following dependencies:

- `clang-7` or higher
- `libglu1-mesa-dev`
- `libc++-7-dev` (`libcxx-devel` and `libcxx-static` on Fedora) or higher
- `libc++abi-7-dev` (`libcxxabi-static` on Fedora) or higher
- `ninja-build`
- `libxi-dev`

After dependencies have been installed, we highly recommend using the [easy build](#easy-build)
script.

If you'd like to run `cmake` directly rather than using the build script, it can be invoked as
follows, with some caveats that are explained further down.

```
$ mkdir out/cmake-release
$ cd out/cmake-release
$ cmake -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=../release/filament ../..
```

Your Linux distribution might default to `gcc` instead of `clang`, if that's the case invoke
`cmake` with the following command:

```
$ mkdir out/cmake-release
$ cd out/cmake-release
# Or use a specific version of clang, for instance /usr/bin/clang-7
$ CC=/usr/bin/clang CXX=/usr/bin/clang++ CXXFLAGS=-stdlib=libc++ \
    cmake -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=../release/filament ../..
```

You can also export the `CC` and `CXX` environment variables to always point to `clang`. Another
solution is to use `update-alternatives` to both change the default compiler, and point to a
specific version of clang:

```
$ update-alternatives --install /usr/bin/clang clang /usr/bin/clang-7 100
$ update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-7 100
$ update-alternatives --install /usr/bin/cc cc /usr/bin/clang 100
$ update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++ 100
```

Finally, invoke `ninja`:

```
$ ninja
```

This will build Filament, its tests and samples, and various host tools.

### macOS

To compile Filament you must have the most recent version of Xcode installed and you need to
make sure the command line tools are setup by running:

```
$ xcode-select --install
```

After installing Java 1.8 you must also ensure that your `JAVA_HOME` environment variable is
properly set. If it doesn't already point to the appropriate JDK, you can simply add the following
to your `.profile`:

```
export JAVA_HOME="$(/usr/libexec/java_home)"
```

Then run `cmake` and `ninja` to trigger a build:

```
$ mkdir out/cmake-release
$ cd out/cmake-release
$ cmake -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=../release/filament ../..
$ ninja
```

### iOS

The easiest way to build Filament for iOS is to use `build.sh` and the
`-p ios` flag. For instance to build the debug target:

```
$ ./build.sh -p ios debug
```

See [ios/samples/README.md](./ios/samples/README.md) for more information.

### Windows

#### Building on Windows with Visual Studio 2019

Install the following components:

- [Visual Studio 2019](https://www.visualstudio.com/downloads)
- [Windows 10 SDK](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk)
- [Python 3.7](https://www.python.org/ftp/python/3.7.0/python-3.7.0.exe)
- [CMake 3.14 or later](https://github.com/Kitware/CMake/releases/download/v3.14.7/cmake-3.14.7-win64-x64.msi)

The latest Windows SDK can also by installed by opening Visual Studio and selecting _Get Tools and
Features..._ under the _Tools_ menu.

Open the `x64 Native Tools Command Prompt for VS 2019`.

Create a working directory, and run cmake in it:

```
> mkdir out
> cd out
> cmake ..
```

Open the generated solution file `TNT.sln` in Visual Studio.

To build all targets, run _Build Solution_ from the _Build_ menu. Alternatively, right click on a
target in the _Solution Explorer_ and choose _Build_ to build a specific target.

For example, build the `material_sandbox` sample and run it from the `out` directory with:

```
> samples\Debug\material_sandbox.exe ..\assets\models\monkey\monkey.obj
```

### Android

Before building Filament for Android, make sure to build Filament for your host. Some of the
host tools are required to successfully build for Android.

Filament can be built for the following architectures:

- ARM 64-bit (`arm64-v8a`)
- ARM 32-bit (`armeabi-v7a`)
- Intel 64-bit (`x86_64`)
- Intel 32-bit (`x86`)

Note that the main target is the ARM 64-bit target. Our implementation is optimized first and
foremost for `arm64-v8a`.

To build Android on Windows machines, see [android/Windows.md](android/Windows.md).

#### Easy Android build

The easiest way to build Filament for Android is to use `build.sh` and the
`-p android` flag. For instance to build the release target:

```
$ ./build.sh -p android release
```

Run `build.sh -h` for more information.

#### Manual builds

Invoke CMake in a build directory of your choice, inside of filament's directory. The commands
below show how to build Filament for ARM 64-bit (`aarch64`).

```
$ mkdir out/android-build-release-aarch64
$ cd out/android-build-release-aarch64
$ cmake -G Ninja -DCMAKE_TOOLCHAIN_FILE=../../build/toolchain-aarch64-linux-android.cmake \
        -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=../android-release/filament ../..
```

And then invoke `ninja`:

```
$ ninja install
```

or

```
$ ninja install/strip
```

This will generate Filament's Android binaries in `out/android-release`. This location is important
to build the Android Studio projects located in `filament/android`. After install, the library
binaries should be found in `out/android-release/filament/lib/arm64-v8a`.

#### AAR

Before you attempt to build the AAR, make sure you've compiled and installed the native libraries
as explained in the sections above. You must have the following ABIs built in
`out/android-release/filament/lib/`:

- `arm64-v8a`
- `armeabi-v7a`
- `x86_64`
- `x86`

To build Filament's AAR simply open the Android Studio project in `android/`. The
AAR is a universal AAR that contains all supported build targets:

- `arm64-v8a`
- `armeabi-v7a`
- `x86_64`
- `x86`

To filter out unneeded ABIs, rely on the `abiFilters` of the project that links against Filament's
AAR.

Alternatively you can build the AAR from the command line by executing the following in the
`android/` directory:

```
$ ./gradlew -Pfilament_dist_dir=../../out/android-release/filament assembleRelease
```

The `-Pfilament_dist_dir` can be used to specify a different installation directory (it must match
the CMake install prefix used in the previous steps).

#### Using Filament's AAR

Create a new module in your project and select _Import .JAR or .AAR Package_ when prompted. Make
sure to add the newly created module as a dependency to your application.

If you do not wish to include all supported ABIs, make sure to create the appropriate flavors in
your Gradle build file. For example:

```
flavorDimensions 'cpuArch'
productFlavors {
    arm8 {
        dimension 'cpuArch'
        ndk {
            abiFilters 'arm64-v8a'
        }
    }
    arm7 {
        dimension 'cpuArch'
        ndk {
            abiFilters 'armeabi-v7a'
        }
    }
    x86_64 {
        dimension 'cpuArch'
        ndk {
            abiFilters 'x86_64'.
        }
    }
    x86 {
        dimension 'cpuArch'
        ndk {
            abiFilters 'x86'
        }
    }
    universal {
        dimension 'cpuArch'
    }
}
```

### WebAssembly

The core Filament library can be cross-compiled to WebAssembly from either macOS or Linux. To get
started, follow the instructions for building Filament on your platform ([macOS](#macos) or
[linux](#linux)), which will ensure you have the proper dependencies installed.

Next, you need to install the Emscripten SDK. The following instructions show how to install the
same version that our continuous builds use.

```
cd <your chosen parent folder for the emscripten SDK>
curl -L https://github.com/emscripten-core/emsdk/archive/1b1f08f.zip > emsdk.zip
unzip emsdk.zip ; mv emsdk-* emsdk ; cd emsdk
python ./emsdk.py install latest
python ./emsdk.py activate latest
source ./emsdk_env.sh
```

After this you can invoke the [easy build](#easy-build) script as follows:

```
export EMSDK=<your chosen home for the emscripten SDK>
./build.sh -p webgl release
```

The EMSDK variable is required so that the build script can find the Emscripten SDK. The build
creates a `samples` folder that can be used as the root of a simple static web server. Note that you
cannot open the HTML directly from the filesystem due to CORS. One way to deal with this is to
use Python to create a quick localhost server:

```
cd out/cmake-webgl-release/web/samples
python3 -m http.server     # Python 3
python -m SimpleHTTPServer # Python 2.7
```

You can then open http://localhost:8000/suzanne.html in your web browser.

Alternatively, if you have node installed you can use the
[live-server](https://www.npmjs.com/package/live-server) package, which automatically refreshes the
web page when it detects a change.

Each sample app has its own handwritten html file. Additionally the server folder contains assets
such as meshes, textures, and materials.

## Running the native samples

The `samples/` directory contains several examples of how to use Filament with SDL2.

Some of the samples accept FBX/OBJ meshes while others rely on the `filamesh` file format. To
generate a `filamesh ` file from an FBX/OBJ asset, run the `filamesh` tool
(`./tools/filamesh/filamesh` in your build directory):

```
filamesh ./assets/models/monkey/monkey.obj monkey.filamesh
```

Most samples accept an IBL that must be generated using the `cmgen` tool (`./tools/filamesh/cmgen`
in your build directory). These sample apps expect a path to a directory containing the '.rgb32f'
files for the IBL (which are PNGs containing `R11F_G11F_B10F` data). To generate an IBL simply use
this command:

```
cmgen -x ./ibls/ my_ibl.exr
```

The source environment map can be a PNG (8 or 16 bit), a PSD (16 or 32 bit), an HDR or an OpenEXR
file. The environment map can be an equirectangular projection, a horizontal cross, a vertical
cross, or a list of cubemap faces (horizontal or vertical).

`cmgen` will automatically create a directory based on the name of the source environment map. In
the example above, the final directory will be `./ibls/my_ibl/`. This directory should contain the
pre-filtered environment map (one file per cubemap face and per mip level), the environment map
texture for the skybox and a text file containing the level harmonics for indirect diffuse
lighting.

If you prefer a blurred background, run `cmgen` with this flag: `--extract-blur=0.1`. The numerical
value is the desired roughness between 0 and 1.

## Generating C++ documentation

To generate the documentation you must first install `doxygen` and `graphviz`, then run the 
following commands:

```
$ cd filament/filament
$ doxygen docs/doxygen/filament.doxygen
```

Finally simply open `docs/html/index.html` in your web browser.
