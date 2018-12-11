# projector-camera calibration using Graycode pattern
[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Please refer to the original project (in Japanese) from [@kamino410](https://github.com/kamino410/cv-snippets/blob/master/graycode/main.cpp). 

![projection-mapping-calibration](https://cdn.instructables.com/FMA/K94O/G3KYBTX1/FMAK94OG3KYBTX1.LARGE.jpg)

Similar process with the 3D reconsturction with Structured Light 3D Scanning ([see this page](https://www.instructables.com/id/Structured-Light-3D-Scanning/)).

This git provides the sourcecode regards the projection mapping technique using [Gray Code pattern](https://en.wikipedia.org/wiki/Gray_code/). Technically, in order to project an image on the correct geometry of the projection target, the projector and the object surface should know the exactly corresponding location between each other. Unfortunately, the projector itself does not have eye. The only (easy) method to due with this is to add the camera into system. Therefore, instead of finding the corresponding between object and projector directly, in this case, we can find the relationship between projector-camera and object-camera. Precisely, find the intrinsic and extensic parateters of camera and projector. 

In this case, we looking at the graycode method, which is the way to obtain the target surface information by projecting the pattern contain special code to the surface and capture it with the camera. Assumed the projector and camera setup is identical and sharing the axis, it is automatically obtain intrinsic and extensic including the mapping points between surface-camera-projector with a single shot. 

__Some related work__
- [Projector Optical Distortion Calibration Using Gray Code Patterns](https://ieeexplore.ieee.org/document/5543487)
- [Shape Disparity Inspection of the Textured Object and Its Notification by Overlay Projection](https://www.researchgate.net/publication/221096805_Shape_Disparity_Inspection_of_the_Textured_Object_and_Its_Notification_by_Overlay_Projection)
- [Structured-light 3D surface imaging: a tutorial](https://www.osapublishing.org/aop/fulltext.cfm?uri=aop-3-2-128&id=211561)

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

Type the command ```touch install.sh``` to create the install command file. The file 'install.sh' will display in the folder. 
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

The line 'git checkout refs/tags/3.4.1' indicate the version of OpenCV. In this case, we will use the 3.4.1 version (you can change the version by going to the [*tag*](https://github.com/opencv/opencv/tags) and checking the right version you need). 

THe line 'CMAKE_CONFIG_GENERATOR="Visual Studio 15 2017 Win64' indicate the complier, make sure that you are install the 'Visuali Studio 2017 (VC15)' with 64x version. You can change to the different version based on your Visual Studio version. 

**Step 5**
Then run the ```install.sh``` by typing the command ```./install.sh``` in Git Bash. 
The build process will run approximately 10 minutes to install and build OpenCV and Contribute.

Once the build finish, please check ```install/opencv/x64/vc15``` has folder ```bin``` and ```lib```, and inside those folder has ```opencv_world400.lib``` (They should also has ```opencv_world400d.lib``` as the debug version). 

Please also add ```C:\opencv\Install\opencv\x64\vc15\bin``` to the PATH of operation system.

### Run
You then can ```pull``` the source code from this git and setup the Visual Studio software. 
The current version setup the webcamera (any USB camera that connected to the computer) as the camera for calibration with projector. 

Once setup the Visual Studio project, setup and link the library. One the file main.cpp, and setup the projection resolution:

```sh
#define WINDOWWIDTH 1920
#define WINDOWHEIGHT 1080
```

This TWO parameters should similar to the size of the projector resolution (the display from projector). 
The current code provides the standard camera (e.g., webcam) via this line of code ```initializeCamera() ```: 

```sh
void initializeCamera() 
{
	camera = cv::VideoCapture(0);
}
```

Please modify to the different setting in the case you are using different type of camera. 
Please also change the ```SCREENPOSX``` and ```SCREENPOSY``` related to your second display (the display that displayed by projection). Otherwise, the image should display on your first screen (the computer screen). 

```sh
#define SCREENPOSX 3440
#define SCREENPOSY 0
```

For example, in this case the ```x = 3440 pixel location``` and ```y = 0 pixel location``` it is located at the end of first screen position. Then, run the code, the gray image should appears on the projector. In the case, it is stop running, enter the ```spacebar```. 

![capture graycode with projection setup](https://cdn-ak.f.st-hatena.com/images/fotolife/k/kamino-dev/20180416/20180416174936.png)

Once the captured completed the captured image will stored in the folder ```captured``` and the mapping between camera-projector pixel will save as an ```c2p.csv``` file.

### How to use the calibration file
The file ```c2p.csv``` contains the coordinate between camera-projector. It is easy to use as an lookup table where the user select the object position in the camera captured image, read that location, and translate to projector pixel for the projection mapping. 

![sample projection mapping](https://blogs.panasonic.com.au/business/media/2017/11/Panasonic-FINA-Opening-Ceremony-Projector-Mapping-04.jpg)


License
----

MIT
