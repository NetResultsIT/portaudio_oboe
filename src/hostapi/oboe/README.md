
# Portaudio implementation for android using Oboe.

In order to use this implementation correctly, be sure to include the "portaudio.h" and "pa_oboe.h"  
headers in your project.

Building:
----  
To build portaudio with Oboe, there are some necessary steps:
1) An android NDK is needed to crosscompile it. I used the version 25.1.8937393, which I found at https://developer.android.com/ndk/downloads.
2) Clone the Oboe repository - just follow the steps detailed here: https://github.com/google/oboe/blob/main/docs/GettingStarted.md.
   Make sure to correctly link the NDK path in the Oboe build. Alternatively, you can use the prebuilt
   libraries and clone the include directory of said repository.
3) If you cloned the Oboe repository, set the CMake variable OBOE_DIRECTORY (used in cmake/modules/FindOboe.cmake)
   to the path of the cloned Oboe repository, and don't forget to build the Oboe libraries (you can use "build_all_android.sh").

   If you instead used the prebuilt libraries, set the following cmake variables:
   - OBOE_DIRECTORY set to TRUE;
   - OBOE_INCLUDE_DIR set to the path of the Oboe include directory;
   - OBOE_LINK_LIBRARIES set to the path of the prebuilt libraries.
4) Build Portaudio (you can use "build_all_PaOboe.sh" to build it for all supported Android ABIs).

TODOs:
----  
- Tests. This implementation was non-extensively tested for VoIP calls and blocking streams - for  
  everything else, it should have a decent structure, but needs to be tested.

- Add support for armeabi-v7a and x86 architectures.

- Implement onErrorAfterClose in a way that works, and checking the callback methods.

Miscellaneous
----  
### Latency and Sharing Mode:
Using LowLatency and SharingMode Exclusive is possible, but a function in  pa_oboe.h that sets said flags *is yet to be implemented*, so you'll have to manually set those properties in the  OboeEngine::OpenStream function.


### Audio Format:
If you need to select a specific audio format, you'll have to manually set it in PaOboe_OpenStream  by modifying the format selection marked with a *FIXME*.
I'm positive that automatic format selection is possible, but simply using  PaUtil_SelectClosestAvailableFormat will not get you anywhere.


### Buffer sizes:
Portaudio often tries to get approximately low buffer sizes, and if you need specific sizes for your  buffer you should manually modify it (or make a simple function that can set it). For your convenience,  there is a *FIXME* as a bookmark.


###  Device selection and/or switching mid-stream:
Device selection can be handled by a java/kotlin method that uses getDevices() in order to identify  which device to select. Switching mid-stream gives an oboe::ErrorDisconnected result, and you'll have  to stop, close and reopen the involved streams with an unspecified device (or a specific device if  you know its ID).
