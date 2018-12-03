# projector-camera calibration using Graycode pattern
[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Please refer to the original project (in Japanese) from [@kamino410](https://github.com/kamino410/cv-snippets/blob/master/graycode/main.cpp)

### Requirement
This code requires the following thirdparty
- Windows 10 (at least Home version)
- Visual Studio Community 2017
- Visual C++ SDK: VC15
- OpenCV 3.4.1
- OpenCV Contrib 3.4.1
- CMake 3.11.0 or newer
- Git for Windows 2.17.0

### Prerequires Installation (OpenCV+Contrib)
To install the OpenCV and OpenCV contributes, you can go through this automatic installation process.

**Step 1**

Install CMake for Windows. You can download CMake from **[this page]**(https://cmake.org/download). Please select the version (32 or 64 bit) depends on your operation system.

![Install CMake](https://cdn-ak.f.st-hatena.com/images/fotolife/k/kamino-dev/20180416/20180416174824.png)

Please make sure that the above selection is set when install the software. 
```sh
Add CMake to the system PATH for all users
```

**Step 2**

Install Git for Windows. You can download from **[this page]**(https://gitforwindows.org/). Please select the version (32 or 64 bit) depends on your operation system.

![Install Git for Windows](https://cdn-ak.f.st-hatena.com/images/fotolife/k/kamino-dev/20180416/20180416174706.png)

**Step 3**
We then have to install [OpenCV](https://opencv.org/). First create the empty folder in your ```C:\opencv```

![Folder for OpenCV](https://cdn-ak.f.st-hatena.com/images/fotolife/k/kamino-dev/20180416/20180416174936.png)

Click in to the folder, and then right click and select 'Git Bash Here' to open Git Bash command. 

![Git Bash](https://cdn-ak.f.st-hatena.com/images/fotolife/k/kamino-dev/20180416/20180416175242.png)

**Step 4**

Type the command
```sh
touch install.sh
```
to create the install command file. The file 'install.sh' will display in the folder. 
Right-click on the file and open it with Notepad. Then, copy the follows code to the file.
```sh
#!/bin/bash -e
myRepo=$(pwd)
CMAKE_CONFIG_GENERATOR="Visual Studio 15 2017 Win64"
if [  ! -d "$myRepo/opencv"  ]; then
    echo "clonning opencv"
    git clone https://github.com/opencv/opencv.git
    mkdir -p Build
    mkdir -p Build/opencv
    mkdir -p Install
    mkdir -p Install/opencv
else
    cd opencv
    git checkout master
    git pull --rebase
    git pull refs/tags/3.4.1
    cd ..
fi
if [  ! -d "$myRepo/opencv_contrib"  ]; then
    echo "clonning opencv_contrib"
    git clone https://github.com/opencv/opencv_contrib.git
    mkdir -p Build
    mkdir -p Build/opencv_contrib
else
    cd opencv_contrib
    git checkout master
    git pull --rebase
    git checkout refs/tags/3.4.1
    cd ..
fi
RepoSource=opencv
pushd Build/$RepoSource
CMAKE_OPTIONS='-DBUILD_PERF_TESTS:BOOL=OFF -DBUILD_TESTS:BOOL=OFF -DBUILD_DOCS:BOOL=OFF  -DWITH_CUDA:BOOL=OFF -DBUILD_EXAMPLES:BOOL=OFF -DINSTALL_CREATE_DISTRIB=ON'
cmake -G"$CMAKE_CONFIG_GENERATOR" $CMAKE_OPTIONS -DOPENCV_EXTRA_MODULES_PATH="$myRepo"/opencv_contrib/modules -DCMAKE_INSTALL_PREFIX="$myRepo"/install/"$RepoSource" "$myRepo/$RepoSource"
echo "************************* $Source_DIR -->debug"
cmake --build .  --config debug
echo "************************* $Source_DIR -->release"
cmake --build .  --config release
cmake --build .  --target install --config release
cmake --build .  --target install --config debug
popd
```
The line 'git checkout refs/tags/3.4.1' indicate the version of OpenCV. In this case, we will use the 3.4.1 version (you can change the version by going to the *tag*(https://github.com/opencv/opencv/tags) and checking the right version you need). 

THe line 'CMAKE_CONFIG_GENERATOR="Visual Studio 15 2017 Win64' indicate the complier, make sure that you are install the 'Visuali Studio 2017 (VC15)' with 64x version. You can change to the different version based on your Visual Studio version. 

**Step 5**
Then run the ```install.sh``` by typing the command ```./install.sh``` in Git Bash. 
The build process will run approximately 10 minutes to install and build OpenCV and Contribute.

Once the build finish, please check ```install/opencv/x64/vc15``` has folder ```bin``` and ```lib```, and inside those folder has ```opencv_world400.lib``` (They should also has ```opencv_world400d.lib``` as the debug version). 

Please also add ```C:\opencv\Install\opencv\x64\vc15\bin``` to the PATH of operation system.

### Run
You then can ```pull``` the source code from this git and setup the Visual Studio software. 
The current version setup the webcamera (any USB camera that connected to the computer) as the camera for calibration with projector. 



License
----

MIT
