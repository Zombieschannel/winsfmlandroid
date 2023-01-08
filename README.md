Here are the files you'll need to either make a Visual Studio project without building SFML yourself or extra files needed to build SFML 2.5.1 yourself (ARM and ARM64, x86 is not included).


Since this tutorial was made for both creating and running an SFML app on Android and building SFML for Android on Windows, it will be divided into a few sections.
## Note:
The end goal of both tutorials is the same, however, the building SFML part starts earlier so every step that is needed only for building SFML will be marked as Italics.
If your goal is only to make a functional SFML app on your Android device, skip the text marked in Italics.
# Part 1: OpenGL ES for Android on Windows using Visual Studio:
I'll be using Visual Studio 2019, Ninja v1.11.1, and CMake 3.19.8.
First of all, you'll need to modify your Visual Studio installation and include Mobile development with C++. This should be a few GB.
 
After that is installed, create a new project and select the type as Native-Activity Application (Android):
 
You can leave the default name as Android1. Click on create.
In your Solution Explorer, you should see these files:
  
The default build configuration is Debug + ARM, however, I recommend changing this to either Debug or Release + ARM64 since most Android devices today are ARM64. 
Build the solution (Ctrl + Shift + B). It should succeed.
# Part 2: Setting up your Android device
Go to settings and scroll down until you see Developer options (if you don't see the option, find on Google how to enable the option). 
Scroll down and enable USB debugging. This will allow you to test your app on your device directly.
Connect your device to your computer and you should see a pop-up on your Android device asking for USB debugging permission. Click Allow.
In Visual Studio it should no longer say "No devices available", but instead, your device should be listed:
 
Click the button, then it should say Deploy started and the app should run on your Android device.
# Part 3: Building SFML for Android
In the C:\Microsoft directory, you should see 2 directories: AndroidNDK64 and AndroidSDK. 
There were installed here by Visual Studio.
If you plan to skip building SFML for Android, download the whole sfml-2.5.1 directory from my GitHub (from here). 
Then copy the folder into C:\Microsoft\AndroidNDK64\android-ndk-r16b\sources\third_party
The end directory should look like this:
 
The next part of the tutorial is for building SFML for Android.
You'll need to download Ninja, CMake, and SFML source code. Here are the links:
Ninja: https://github.com/ninja-build/ninja/releases
CMake: https://cmake.org/download/
SFML: https://www.sfml-dev.org/download/sfml/2.5.1/
After unzipping, you’ll need to add both CMake and Ninja to your path.
 
In my case that is: C:\Users\cutel\cmake-3.19.8-win64-x64\cmake-3.19.8-win64-x64\bin
and C:\Users\cutel\ninja
After that, create 2 folders inside the SFML source code directory for ARM and ARM64. I’ll call them android and android64.
  
Then open the command prompt and “cd” into the first folder which we’ll use for ARM. Make sure that the path works by typing out cmake and ninja (if the path does not work, remember to restart cmd after messing with the environment variables).
 
*We’ll now run the main command
`cmake -DCMAKE_SYSTEM_NAME=Android -DCMAKE_ANDROID_NDK=XYZXYZXYZ -DCMAKE_ANDROID_ARCH_ABI=armeabi-v7a -DCMAKE_ANDROID_STL_TYPE=c++_static -DCMAKE_BUILD_TYPE=Debug -G Ninja ..`
Replace the XYZXYZXYZ with the path to your AndroidNDK64 by Visual Studio. In my case that is: C:\Microsoft\AndroidNDK64\android-ndk-r16b 
Also remember to change all of the \ to / otherwise you'll get an error.
Then pray.
If all goes well, you should get a bunch of text and something like this at the end:
```
-- Configuring done
-- Generating done
-- Build files have been written to: C:/Users/cutel/Downloads/SFML-2.5.1-sources/SFML-2.5.1/android
```
If something goes wrong, always make sure that you delete everything from the folder before running the command again.
Then type the following:
`cmake --build . --target install`
If all goes well, you’ll get a bunch of text which all has “–Installing:” in it.
The ARM Debug mode is done.
To switch to ARM Release mode type: `cmake -DCMAKE_BUILD_TYPE=Release .`
Then run the `cmake --build . --target install` command again.
The ARM Release mode is done.
Now “cd” to the second folder you created (in my case that is android64). Here we’ll do the same thing but for ARM64. So that would be “cd ..” and “cd android64”.
Then run this command again:
`cmake -DCMAKE_SYSTEM_NAME=Android -DCMAKE_ANDROID_NDK=XYZXYZXYZ -DCMAKE_ANDROID_ARCH_ABI=arm64-v8a -DCMAKE_ANDROID_STL_TYPE=c++_static -DCMAKE_BUILD_TYPE=Debug -G Ninja ..`
Note that the “-DCMAKE_ANDROID_ARCH_ABI=armeabi-v7a” has been replaced by
“-DCMAKE_ANDROID_ARCH_ABI=arm64-v8a”.
This time you’ll get an error saying: Missing item in FREETYPE_LIBRARY
For unknown reasons, if you go to the extlibs/libs-android/ folder in the SFML source code you downloaded, you’ll see 4 folders, none of which is called arm64-v8a.
I have no idea why is it not there however you can download the contents from my GitHub again (from here).
Under FilesForBuildingYourself, there should be a arm64-v8a folder that you need to download and add to extlibs/libs-android/ folder.
After you’ve done that make sure that you deleted everything from the second folder (in my case android64), and run the command again.
This time everything should work and you can run the same commands that were used for ARM. 
`cmake --build . --target install`
then: `cmake -DCMAKE_BUILD_TYPE=Release .`
and then again: `cmake --build . --target install`*

# Part 4: Getting the file structure correct
This is going to be a little bit hacky, however for now it is the only way.
Inside C:\Users\cutel\source\repos\Android1 you should see your .sln file as well as the Android1 folder.
Inside the Android1 folder, there should be Android1.NativeActivity and Android1.Packaging.
Go into Android1.Packaging. You should see this (ignore ARM and ARM64 if you have those):
  
Here we'll need to mirror the following structure:
Add the "libs" folder as well as the "assets" folder.
In the libs folder, there will be all of the SFML's .so files for both arm and arm64 and both release and debug modes, however, you'll have to only use the ones you need for the build, otherwise, your app will probably crash.
The assets folder is where you'll put all of your assets. For this tutorial we'll only make the classic SFML circle, so we do not need any assets.
The end result should look like this:
 
Now inside the "libs" folder add the following:
if you plan to compile for ARM then add a folder called "arm", if you plan to compile for ARM64 then add a folder called "arm64-v8a". I will add both folders.
 
Now go back to the C:\Microsoft\AndroidNDK64\android-ndk-r16b\sources\third_party\sfml-2.5.1\lib folder. There you will see the arm64-v8a and armeabi-v7a folders. Inside each one you should find this: 
 
For the folder armeabi-v7a copy all of the ".so" files to the "libs/arm/" folder back in your Visual Studio project.
Also, go to C:\Microsoft\AndroidNDK64\android-ndk-r16b\sources\third_party\sfml-2.5.1\extlibs\lib and from the folder armeabi-v7a copy the libopenal.so back to the "libs/arm/" folder in your Visual Studio project. 
For the folder arm64-v8a copy all of the ".so" files to the "libs/arm64-v8a/" folder back in your Visual Studio project.
Also, go to C:\Microsoft\AndroidNDK64\android-ndk-r16b\sources\third_party\sfml-2.5.1\extlibs\lib and from the folder arm64-v8a copy the libopenal.so back to the "libs/arm64-v8a/" folder in your Visual Studio project.
In both "libs/arm" and "libs/arm64-v8a" you should now see this:
 
# Part 5: Back in Visual Studio
In the Solution Explorer click on Show all files and then click on Refresh.
 
You should now see the assets folder as well as the libs folder:
 
Next up, delete the android_native_app_glue.c and android_native_app_glue.h.
Delete all of the code from pch.h. Do not delete the file since that will later throw an error.
Replace all of the code from the main.cpp file with the following code:
```
#include <SFML/Graphics.hpp>
int main()
{
    sf::RenderWindow window(sf::VideoMode(200, 200), "SFML works!");
    sf::CircleShape shape(100.f);
    shape.setFillColor(sf::Color::Green);
    while (window.isOpen())
    {
         sf::Event event;
         while (window.pollEvent(event))
         {             
            if (event.type == sf::Event::Closed)
                 window.close();
         }          
         window.clear();
         window.draw(shape);
         window.display();
    }      
    return 0;
}
```
The Solution Explorer should look like this now:
 
Now we need to include libs for compilation.
In this case, I'm going to build for ARM64 in Release mode. Because of that, I'll include the following files (click on include in the project):
 
The whole point of adding these .so files in Visual Studio is for them to get added to the final .apk. Otherwise, the app would crash.
We're still getting errors for the SFML circle example because we still need to include the library.
 
So open project properties. Go to tab C/C++ and General.
Then under Additional Include Directories add the path to the SFML include folder followed by a semi colon: in my case that is 
`C:\Microsoft\AndroidNDK64\android-ndk-r16b\sources\third_party\sfml-2.5.1\include;`
Also make sure that both configuration and platform are correctly set (in my case: Release and ARM64)
 
Next go to Linker -> General.
Here under Additional Library Directories add the path to the SFML lib (this is different between arm and arm64) folder followed by a semi colon: in my case:
`C:\Microsoft\AndroidNDK64\android-ndk-r16b\sources\third_party\sfml-2.5.1\lib\arm64-v8a;`
 
The final step in properties is to go Linker -> Input and here under Library Dependencies you need to add names of the libraries followed by a semi colon (if you are in debug mode, be sure to add the “-d” after each file name: 
in my case this is `sfml-activity;sfml-audio;sfml-network;sfml-system;sfml-window;sfml-graphics`
 
If you now exit the properties window and click on Rebuild solution, everything should work, right?
Well, not yet. There are a few more steps. Check your Output window.
*If you built SFML for Android yourself, you might see that some of the .hpp files are missing. I have no idea why is that however, what you should do is just copy those files to the sfml include directory in the ndk (the file structure must be the same as mentioned down below). Files that are missing are: 
System/Android/Activity.hpp
Window/EGLCheck.hpp
Window/EglContext.hpp
Window/GlContext.hpp*

You might see an error saying that some things are undefined:
 
I have no idea why is that but to fix it, once again go into Properties, under General you’ll see the Target API Level.
For ARM by default it is set to KitKat 4.4 – 4.4.4 (android-19).
Change it to Lollipop 5.0 – 5.0.2 (android-21).
For ARM64 by default it is set to Lollipop 5.0 – 5.0.2 (android-21).
Change it to Android-26.
If you now rebuild the solution once again, the build should succeed.
However, the app will still crash. But we are almost there. There are 2 more steps.
First you’ll need to download the SFML’s MainAndroid.cpp (you can find it on my GitHub again).
Copy and paste that file next to where your main.cpp is (in my case that is here: C:\Users\cutel\source\repos\Android1\Android1\Android1.NativeActivity).
The final step is to open AndroidManifest.xml from Visual Studio:
 
Here you’ll see this piece of code where it says “Tell NativeActivity the name of our .so”
 
You’ll need to replace that piece of code with the following:
 
And that should be it! If you build the project now and deploy it to your Android device, you should see a big green stretched circle.
 

