# Changes Log

- Error: cvc-complex-type.2.4.a: Invalid content was found starting with element 'base-extension'. One of '{layoutlib}' is expected.

  - From `Project Structure`, change `Gradle Version` to `7.5` and change `Android Gradle Plugin Version` to `7.4.2`.

- OnnxRuntimeAndorid/app/src/main/cpp/onnxruntime_inference.cpp:47:5: error: use of undeclared identifier 'assert'

  - comment out `assert` for now.

- OnnxRuntimeAndorid/app/src/main/java/com/play/onnxruntime/MainActivity.java:26: error: package android.support.annotation does not exist
  
  - Right click on `App`, `Refactor\Migrate to AndroidX...`.
  - Update those `import android.support.` to `import androidx.`

- Error: ADB server version (36) doesn't match this client (39)

  - For adb, make sure to use `Android\Sdk\platform-tools\adb`, 
  - Run `adb shell` to verify

# OnnxRuntimeAndorid

This app uses ONNXRuntime (with NNAPI enabled) for Android C/C++ library to run MobileNet-v2 ONNX model. Android camera pixels are passed to ONNXRuntime using JNI

  <table>
    <tr>
      <td>
        <img src="./imgs/laptop.png" width="300" height="500">
      </td>
      <td>
        <img src="./imgs/lamp.png" width="300" height="500">
      </td>
      <td>
        <img src="./imgs/bottle.png" width="300" height="500">
      </td>
    </tr>
  </table>


 #On Google pixel 3 ONNXRuntime (with NNAPI execution provider) took around 25 miliseconds to run MobileNetV2 model

# Implemtation Details

ONNXRuntime shared object (libonnxruntime.so) was created by following instructions in ONNXRuntime Docs.
https://github.com/microsoft/onnxruntime/blob/master/BUILD.md#android-build-instructions

    ./build.sh --android --android_sdk_path /Users/username/Library/Android/sdk --android_ndk_path /Users/username/Documents/Android/android-ndk-r21d --android_abi arm64-v8a --android_api 28 --use_nnapi --parallel --build_shared_lib --config MinSizeRel


libonnxruntime.so is copied to distribution/lib/arm64-v8a/libonnxruntime.so and ONNXRuntime headers are copied to distribution/include/onnxruntime

 - app/src/main/cpp path contains ONNXRuntime C++ inference, preprocessing input, postprocessing output, JNI wrappers code
 - app/src/main/java/com/play/onnxruntime contains java code to open camera, display image stream, access bitmap, UI, JNI calls to run inference..
 - app/CMakeLists.txt is the cmake file to compile the c++ code

# Run the prebuilt apk 
copy model/mobilenetv2-7.onnx and model/labels.txt to /data/local/tmp directory on device

    adb push model/mobilenetv2-7.onnx /data/local/tmp
    adb push model/labels.txt /data/local/tmp

Install the prebuilt apk file

    adb install apk/app-debug.apk


# Build Instructions

#Requirements
 - Android SDK 29
 - Android NDK r21d

Open the local.properties file and set ndk.dir to the path of Android NDK folder.

    sdk.dir = /Users/Name/Library/Android/sdk
    ndk.dir = /Users/Name/Documents/Android/android-ndk-r21d

Run the app after making the above changes.

#Note
This app was test on Google pixel3 and build for arm64-v8a.

