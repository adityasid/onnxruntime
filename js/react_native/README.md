# onnxruntime-react-native

ONNX Runtime React Native provides a JavaScript library for running ONNX models on React Native app.

### Why ONNX models

The [Open Neural Network Exchange](http://onnx.ai/) (ONNX) is an open standard for representing machine learning models. The biggest advantage of ONNX is that it allows interoperability across different open source AI frameworks, which itself offers more flexibility for AI frameworks adoption.

### Why ONNX Runtime React Native

With ONNX Runtime React Native, React Native developers can score pre-trained ONNX models directy on React Native apps by leveraging [ONNX Runtime Mobile](https://www.onnxruntime.ai/docs/reference/mobile/prebuilt-package/), so it provides a light-weight inference solution for Android and iOS.

### Installation

```sh
yarn add onnxruntime-react-native
```

### Usage

```js
import { InferenceSession } from "onnxruntime-react-native";

// load a model
const session: InferenceSession = await InferenceSession.create(modelPath);
// input as InferenceSession.OnnxValueMapType
const result = session.run(input, ['num_detection:0', 'detection_classes:0'])
```

Refer to [ONNX Runtime JavaScript examples](https://github.com/microsoft/onnxruntime-inference-examples/tree/main/js) for samples and tutorials. Different from other JavaScript frameworks like node.js and web, React Native library doesn't support these features.

- Unsigned data type at Tensor
- Model loading using ArrayBuffer


# Run onnxruntime-react-native example

1.  Install NPM packages for ONNX Runtime common JavaScript library and required React Native JavaScript libraries
    
    -   in  `<ORT_ROOT>/js/`, run  `npm ci`.
    -   in  `<ORT_ROOT>/js/common/`, run  `npm ci`.
    -   in  `<ORT_ROOT>/js/react_native/`, run  `yarn`.
2.  Build Android ONNX Runtime package
    
    1.  To use a published Android ONNX Runtime Mobile package from Maven, go to step 5.
        
    2.  Set up an Android build environment referring to  [instruction](https://www.onnxruntime.ai/docs/how-to/build/android-ios.html#android)
        
    3.  In  `<ORT_ROOT>`, run this python script to build ONNX Runtime Android archive file. In windows, this requires an admin account to build. To build a model specific package with reduced size, refer to  [instruction](https://www.onnxruntime.ai/docs/how-to/build/reduced.html#build-ort-with-reduced-size).
        
        python tools/ci_build/github/android/build_aar_package.py tools/ci_build/github/android/default_mobile_aar_build_settings.json --config MinSizeRel --android_sdk_path <ANDROID_SDK_PATH> --android_ndk_path <ANDROID_NDK_PATH> --build_dir <BUILD_DIRECTORY> --include_ops_by_config tools/ci_build/github/android/mobile_package.required_operators.config
        
    4.  Copy  `<BUILD_DIRECTORY>/aar_out/MinSizeRel/com/microsoft/onnxruntime/onnxruntime-mobile/<version>/onnxruntime-mobile-<version>.aar`  into  `<ORT_ROOT>/js/react_native/android/libs`  directory.
        
    5.  Modify  `Onnxruntime_mobileVersion`  property in  `<ORT_ROOT>/js/react_native/android/build.properties`  to consume a locally built package or a newly published package from Maven.
        
    6.  To verify, open Android Emulator and run this command from  `<ORT_ROOT>/js/react_native/android`
        
        adb shell am instrument -w ai.onnxruntime.react_native.test/androidx.test.runner.AndroidJUnitRunner
        
3.  Build iOS ONNX Runtime package
    
    1.  To use a published c/c++ ONNX Runtime Mobile package from CocoaPods, skip all steps below.
        
    2.  Set up iOS build environment referring to  [instruction](https://www.onnxruntime.ai/docs/how-to/build/android-ios.html#ios).
        
    3.  Build a fat ONNX Runtime Mobile Framework for iOS and iOS simulator from  `<ORT_ROOT>`  using this command,
        
        python tools/ci_build/github/apple/build_ios_framework.py tools/ci_build/github/apple/default_mobile_ios_framework_build_settings.json --config MinSizeRel --include_ops_by_config tools/ci_build/github/android/mobile_package.required_operators.config
        
        It creates  `onnxruntime.framework`  in  `build/iOS_framework/framework_out`  directory. Create an archive file of  `onnxruntime.framework`  directory as follows and copy to  `<ORT_ROOT>/js/react_native/local_pods`  directory.
        
        zip -r onnxruntime-mobile-c.zip onnxruntime.framework
        
    4.  To verify, open iOS Simulator and run this command from  `<ORT_ROOT>/js/react_native/ios`. Change a destination to specify a running iOS Simulator.
        
        pod install
        xcodebuild test -workspace OnnxruntimeModule.xcworkspace -scheme OnnxruntimeModuleTest -destination 'platform=iOS Simulator,name=iPhone 11,OS=14.5'
        
4.  Test an example for Android and iOS. In Windows, open Android Emulator first.
    
    `debug.keystore`  must be generated ahead for Android example.
    
    keytool -genkey -v -keystore <ORT_ROOT>/js/react_native/example/android/app/debug.keystore -alias androiddebugkey -storepass android -keypass android -keyalg RSA -keysize 2048 -validity 999999 -dname "CN=Android Debug,O=Android,C=US"
    
    From `<ORT_ROOT>/js/react_native,
    
    yarn bootstrap
    yarn example ios
    yarn example android

### Operator and type support

ONNX Runtime React Native currently supports most operators used by popular models. Refer to [ONNX Runtime Mobile Pacakge Operator and Type](https://www.onnxruntime.ai/docs/reference/mobile/prebuilt-package/1.8%20ORTMobilePackageOperatorTypeSupport).

### License

License information can be found [here](https://github.com/microsoft/onnxruntime/blob/master/README.md#license).
