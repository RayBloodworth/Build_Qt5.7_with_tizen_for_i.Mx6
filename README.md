This is document for building Qt5.7 with tizen sysroot for i.Mx6


Step 1) Prepare a tizen sysroot.
initialize build root with gbs:
cd dummy
git init
git add ./
gbs build -A armv7l --include-all -B ~/GBS_ROOT_armv7l/

Step 2) configure and build the Qt sourcecode.
cd ~/qt-everywhere-opensource-src-5.7.0
modify qtbase/mkspecs/devices/linux-arm-generic-g++/qmake.conf
+TMP_CFLAGS= -march=armv7-a -mfpu=neon -I/usr/arm-linux-gnueabi/include -I/usr/arm-linux-gnueabi/include/c++/4.7.3 -I/usr/arm-linux-gnueabi/include/c++/4.7.3/arm-linux-gnueabi/ -DWL_EGL_PLATFORM=1
+QMAKE_LIBS +=-L/usr/arm-linux-gnueabi/lib

./configure -prefix $PWD/deploy -device linux-arm-generic-g++ \
   -device-option CROSS_COMPILE=/usr/bin/arm-linux-gnueabi- \
   -sysroot ~/GBS_ROOT_armv7l/local/BUILD-ROOTS/scratch.armv7l.0 \
   -nomake examples -nomake tests -v -device-option -qpa=wayland -no-openvg -opengl es2 -opensource -confirm-license -no-gtk -no-eglfs
   
make
make install

note: I used the default cross-compiler in ubuntu, you can change it to other's.

Step 3) Build Qt wayland module
cd qtwayland
~/qt-everywhere-opensource-src-5.7.0/deploy/bin/qmake -r
make
make install

note: Make sure wayland-scanner is installed in your system $PATH before compiling qtwayland.

Step 4)Copy Qt components to board. Example location: /usr/lib/qt5.7.

Step 5)Setup Tool kit in Qtcreater.
In option->build&&run section:
Add Qt version ->/qt-everywhere-opensource-src-5.7.0/deploy/bin/qmake
Add compiler -> /usr/bin/arm-linux-gnueabi-gcc
Add a kit with Qt version and compiler above.

Now, you can build and run the Qt demos with tizen on i.Mx6 board.
How to run tizen on i.Mx6 board? Please refer https://github.com/RayBloodworth/porting_tizen_to_imx6q
