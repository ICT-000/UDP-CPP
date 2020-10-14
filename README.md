# Unknown Detection Party V2.0
### A Minecraft Cheat in C++

### To Do:
  - add CreateRemoteThread(it shares all methods, fields, ... from JNI in main instance) which will replace CreateThread in dllmain
    - will need to be placed before JNI is loaded
    - cheat activation via GUI in DLL
    - every active cheat = one thread
    - thread for checking is cheat active and start thread or kill it every 1.5s   
    - make threads share one console
   
  - load JVM and JNI before RemoteThread is created.
  - add optional config.yml loader where you can specify version
  - add mapping loader from resources - mappings.yml
  - JNI reference/s, logs and register cleanup 
  - DLL Loader with DLL as reasource which will copy it as resource on drive and delete it when loaded
  
### Getting Started With a Fresh Project

You can either follow the text instructions below or [follow them via video](https://www.youtube.com/watch?v=rr_QV16jktw).

  - If you haven't already, install Visual Studio (If you already have a x64 C++ compile setup, just use that, but the next few steps will be for VS)
  - Start VS, and create a new project. Make sure to select `Visual C++ >> Windows Desktop >> Dynamic-Link Library (DLL)` as the project type. Give it a name and location, and create it
  - You can probably just delete the file it creates named `<your_project_name>.cpp` as you'll either create another later, or do everything in the `dllmain.cpp` file
  - Now that you have a base location for your project, locate your JDK installation (Somewhere around `C:\Program Files\Java\jdk1.8.0_xxx\`)
  - Locate the `jvm.lib` library inside the JDK installation (`.\lib\jvm.lib`)
  - Locate the `jvm.dll` dll inside the JDK installation (`.\jre\bin\jvm.dll`)
  - Copy the two located files into your project's code directory (`.\Project Name\Project Name\`, it should be the place where all the `.cpp` and `.h` files are)
  - Head back to VS, click `Build >> Properties` in the toolbar at the top
  - Switch the `"Platform"` to (if it's not already) `x64` since Minecraft runs in 64bit, and your DLL just won't work otherwise
  - In the left panel, open `VC++ Directories`. It should be the third one down (unless I'm just special)
  - Click in the first entry field: `Executable Directories`, and click the dropdown on the right, then `<Edit...>`
  - Click the first icon in the top left to create a new entry (do this twice, once after the first)
  - For the first one, put the following: `<JDK install>\include`, and for the second: `<JDK install>\include\win32`
  - Now, in the panel on the left, open the `Linker` node, and open `Input`.
  - Just like the dependencies, click the `Additional Dependencies` entry field, click the dropdown, edit, and then enter `jvm.lib` and click ok
  - Next, in the `dllmain.cpp` file, put the following:
    ```cpp
    //Required libraries
    #include <Windows.h>
    #include <iostream>
    
    /*
    	Handle the base injection in the newly created thread (if injection was successful)
    	Allocates a new console window to the application and routes stdin, stdout, and
    	stderr to the console. This prevents any debugging being mixed in with Minecraft's
    	output window
    */
    void inject() {
    	AllocConsole();
    	FILE* fIn;
    	FILE* fOut;
    	freopen_s(&fIn, "conin$", "r", stdin);
    	freopen_s(&fOut, "conout$", "w", stdout);
    	freopen_s(&fOut, "conout$", "w", stderr);
    
    	std::cout << "Injection Successful!" << std::endl;
    }
    
    /*
    	Entry point function for the DLL
    	If it has just been attached, create a new process thread and run the "inject" function
    */
    BOOL APIENTRY DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpvReserved) {
    	if (fdwReason == DLL_PROCESS_ATTACH)
    		CreateThread(nullptr, 0, (LPTHREAD_START_ROUTINE)inject, nullptr, 0, nullptr);
    	return TRUE;
    }
    ```
  - Change the compile mode at the top of Visual Studio to x64, and build the DLL (`Ctrl + Shift + B`)

You should now have a built DLL to inject straight into Minecraft, and watch the magic unfold!

### Disclaimer

The code is shit, there's countless things I could do to improve the functionality (including toggle for the aimbot), but I really couldn't care less, so don't bother saying it's shit cause I'll just screenshot this disclaimer and link you to Chum Drum Bedrum
