Installing and using `winbgim` on Windows® 11
========================

# Notes
<!--
This is the ENGLISH version. For the ROMANIAN (ROMÂNĂ) version see <link>.
--->
Document revision `1.1.0`.

This work © 2023 by Andrei Florea (onefieryfern) is licensed under [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/).

The guide may also be used on older versions of Windows®, but this guide focuses on Windows® 11. YMMV.

# Common steps
These steps are the same regardless of whether you use Code::Blocks, Clion, or both.

## 1. Installing 32bit MinGW
MinGW (MinGW.OSDN)'s new home seems to be at <https://mingw.osdn.io/> and <https://osdn.net/projects/mingw/>.

To start, go to <https://osdn.net/projects/mingw/> and download `mingw-get-setup.exe` from the `Download` (no 's') section.

Next, run the downloaded file by right-clicking `mingw-get-setup.exe`, then clicking `Run as administrator`. (You may need to click `Yes` in the `User Account Control` window that may appear.) Click `Install` > `Continue`, then wait for the installation to finish. Click `Continue` when it becomes available.

In the new window that appears, "Mark for installation" `mingw32-base-bin` and `mingw32-gcc-g++-bin`. In the top bar, go to `Installation` > `Apply Changes`, then click `Apply`, then wait. Click `Close` after the installation completes.

Close the `MinGW Installation Manager` window. You can also delete `mingw-get-setup.exe` now.

## 2."Installing" `winbgim`
I have not been able to track down an "official" source for `winbgim` as of yet. Until then, you may get it at <https://profs.info.uaic.ro/~introp/curs2022-2023/winbgim.zip>.

To start, go to <https://profs.info.uaic.ro/~introp/curs2022-2023/winbgim.zip>. `winbgim.zip` should be downloaded automatically.

Next, unarchive the archive, making sure that you end up with a `winbgim` folder that contains the following files: `graphics.h`, `libbgi.a` and `winbgim.h`.

Next, copy the folder to a location of your choice. This guide recommends you put it in `C:\`, so that you end up with `C:\winbgim`.

You can delete the `winbgim.zip` archive now.

# Configuring for (modern) Code::Blocks
## 1. Installing Code::Blocks
This guide won't detail how to accomplish this, this step serves the sole purpose of reminding you to install (a modern version of) Code::Blocks.

## 2. Configuring a new compiler profile in Code::Blocks
In the top bar, go to `Settings`, then `Compiler...`.

In the window that appears, press the `Copy` button in the `Selected compiler` section. In the new window that appears, you may name this new profile to whatever you want. I recommend naming it something like `MinGW (32bit) with winbgim`. Press `OK` in the new window that appears.

Back in the `Compiler settings` window, go to the `Toolchain executables` tab under `Global compiler settings`. In the `Compiler's installation directory` section, press the `...` button. Select the folder where you installed MinGW. By default, it is `C:\MinGW`.

Now go to the `Linker settings` tab under `Global compiler settings`. In the `Other linker options` section, paste the following: `-lbgi -lgdi32 -lcomdlg32 -luuid -loleaut32 -lole32`.

Next, go to the `Search directories` tab under `Global compiler settings`. Go to the `Compiler` tab. Click the `Add` button at the bottom. Select the `winbgim` folder. If you followed the guide's defaults, choose `C:\winbgim`. Click `OK`. Go to the `Linker` tab. Click the `Add` button at the bottom. Select the same folder as before. Click `OK`.

A note before ending: if you want this profile to be used by default (so you don't have to select it for every project you create), click the `Set as default` button in the `Selected compiler` section.

To finish and save your work, press the `OK` button in the bottom-right.

## 3. Using winbgim in your projects
Note that the `winbgim.h`and `graphics.h` header files are identical. You may include either or both. Including both won't cause any problems as they both the same header guard.

Go to `File` > `New` > `Project...` > `Console application` > `Go` > `Next>` > `Next>` > Name your project under `Project title` and select a save location under `Folder to create project in`, then click `Next>` > Under `Compiler` select your newly-created profile (this guide names it `MinGW (32bit) with winbgim`), then click `Finish`.

# Configuring for JetBrains CLion
## 1. Installing CLion
This guide won't detail how to accomplish this, this step serves the sole purpose of reminding you to install CLion.

## 2. Configuring a new toolchain in CLion (Old UI & New UI)
From CLion's "Welcome to CLion" window, go to `Customize` on the sidebar. Then, go to `All settings...`.

Next, go to `Build, Execution, Deployment` > `Toolchains`. Here, click the `+` button and choose `MinGW`.

In the `Name` field, I recommend writing something like `MinGW (32bit) with winbgim`. Next, click the folder icon from the `Toolset` field. Select the folder where you installed MinGW. By default, it is `C:\MinGW`. Then, press `OK` to save your changes.

## 3. Using `winbgim` in your projects
Note: you will have to follow the steps below for any projects you want to use the `winbgim` library with.

### 3.1. Using another toolchain in your projects
In a project, click the gear icon at the top-right of the screen, then click `Settings...`. Next, go to `Build, Execution, Deployment` > `CMake`. Choose the `Debug` profile if it is not already chosen. Then, under `Toolchain`, choose your newly-created toolchain (this guide names it `MinGW (32bit) with winbgim`), then click `OK`.

Note: the icon next to the `CMake` section under `Build, Execution, Deployment` means the settings modified in that section will only apply to the currently open project.

### 3.2. Including the `winbgim` library
This can only be accomplished on a project-by-project basis, by modifying the `CMakeLists.txt` file every CMake project contains.

You need to add the following lines to the `CMakeLists.txt` file, modifying placeholders accordingly:

```CMake
target_include_directories([your-project-name] PRIVATE "[winbgim-folder-location]")
target_link_directories([your-project-name] PRIVATE "[winbgim-folder-location]")
target_link_libraries([your-project-name] bgi gdi32 comdlg32 uuid oleaut32 ole32)
```

Replace the following placeholders as such:
* `[your-project-name]` **with** your project's name, which can be found in the same file, on a line like this: `project([your-project-name])`
* `[winbgim-folder-location]` **with** the location of the `winbgim` folder. If you followed the guide's defaults, choose `C:\winbgim`.

Example `CMakeLists.txt` file:

```CMake
cmake_minimum_required(VERSION 3.25)
project(exampleproject)

set(CMAKE_CXX_STANDARD 17)

add_executable(exampleproject main.cpp)

# winbgim-relatedend modifications start here
target_include_directories(exampleproject PRIVATE "C:/winbgim")
target_link_directories(exampleproject PRIVATE "C:/winbgim")
target_link_libraries(exampleproject bgi gdi32 comdlg32 uuid oleaut32 ole32)

```

# Testing `winbgim`

You may use the following code to test your new MinGW + winbgim installation:

```C++
// SPDX-License-Identifier: Unlicense
// This is free and unencumbered software released into the public domain.

#include <iostream>
#include <winbgim.h>

void drawSquare (int x, int y, int length)
{
    line(x, y, x + length, y);
    line(x + length, y, x + length, y + length);
    line(x + length, y + length, x, y + length);
    line(x, y + length, x, y);
}

int main()
{
    const int windowSide = 600;
    initwindow(windowSide, windowSide);

    std::cout << "Hello world!" << std::endl;

    const int step = 50;
    int length = windowSide - 2 * step;
    int x = step;
    int y = step;

    int stepCount = 0;

    while (length > 0)
    {
        drawSquare(x, y, length);

        length -= 2 * step;
        x += step;
        y += step;

        stepCount++;
    }

    std::cout << "Drew " << stepCount << " squares." << std::endl;

    getch();
    closegraph();

    return 0;
}
```

The program should draw 5 concentric squares, and output some things to the console. You may safely modify the values of the  `windowSide` and `step` variables in `main()` and see what happens.
