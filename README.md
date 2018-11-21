## QR Code Scanning using ZXing

In this tutorial, we will learn to integrate ZXing lib into an Android app. Let us first get some idea about ZXing.

## [ZXing](https://github.com/zxing/zxing)

ZXing ("zebra crossing") is an open-source, multi-format 1D/2D barcode image processing library implemented in Java, with ports to other languages. ZXing is also hosted under [Google Open Source Project](https://opensource.google.com/projects/zxing).

The latest version up to this date is [3.3.3](https://github.com/zxing/zxing/releases/tag/zxing-3.3.3). Modules in this release we need to focus on are -

| Modules               | Description
|-----------------      |-------------------
| `core`                | The core image decoding library, and test code (Java lib)
| `android`             | Android app also available on Play Store [Barcode Scanner](https://play.google.com/store/apps/details?id=com.google.zxing.client.android)
| `android-core`        | Android-related code shared among android, other Android apps
| `android-integration` | Supports integration with Barcode Scanner via Intent

All the modules are available on Maven Central -

[![core](https://img.shields.io/maven-central/v/com.google.zxing/core/3.3.3.svg?label=core)](https://search.maven.org/artifact/com.google.zxing/core/3.3.3/jar)
[![android-core](https://img.shields.io/maven-central/v/com.google.zxing/android-core/3.3.0.svg?label=android-core)](https://search.maven.org/artifact/com.google.zxing/android-core/3.3.0/jar)
[![android-integration](https://img.shields.io/maven-central/v/com.google.zxing/android-integration/3.3.0.svg?label=android-integration)](https://search.maven.org/artifact/com.google.zxing/android-integration/3.3.0/jar)

## Integration

Let's say we want to integrate ZXing in our app called **QR Code Scanner Android**. Now the problem is that `android` module is actually an Android application and not an Android library. So to use the ZXing lib as advocated by ZXing themselves, we have the following options -

- Install Barcode Scanner app first and then use `android-integration` and `android-core` for Integration.

  Or

- Integrate code using `android-integration` and `android-core`, if Barcode Scanner app is not installed then pop up would display to install the app first.

Obviously, both these options depend upon the Barcode Scanner app. So to overcome this dependency [JourneyApps](https://journeyapps.com/) has come up with [**ZXing Android Embedded**](https://github.com/journeyapps/zxing-android-embedded) which is loosely based on the ZXing Android Barcode Scanner application but is not affiliated with the official ZXing project.

ZXing Android Embedded is available on bintray - </br>
[![ZXing Android Embedded](https://img.shields.io/badge/ZXing%20Android%20Embedded-v3.6.0-0778B8.svg)](https://bintray.com/journeyapps/maven/zxing-android-embedded/3.6.0)

So this tutorial is based on integrating the ZXing Android Embedded library in our QR Code Scanner app. You can find the **source code** of this tutorial on [QR Code Scanner](https://github.com/codetoart/qr-code-scanner). Let's start the integration. From version 3.6.0, only Android SDK 19+ is supported by default.

#### Dependency

```groovy
repositories {
  jcenter()
}

dependencies {
  implementation 'com.journeyapps:zxing-android-embedded:3.6.0'
}
```

For Android 14+ support, see [this](https://github.com/journeyapps/zxing-android-embedded#adding-aar-dependency-with-gradle) link.

#### Code

MainActivity.kt

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        button_scan_qr_code.setOnClickListener {
            IntentIntegrator(this).initiateScan()
        }
    }

    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)

        val result = IntentIntegrator.parseActivityResult(requestCode, resultCode, data)
        if (result != null) {
            if (result.contents == null) {
                Toast.makeText(this, "Cancelled", Toast.LENGTH_LONG).show()
            } else {
                Toast.makeText(this, "Scanned: " + result.contents, Toast.LENGTH_LONG).show()
            }
        }
    }
}
```

On click of `button_scan_qr_code`, `CaptureActivity` will start scanning using default camera. Once it scans any QR code, it sends back the result to `onActivityResult` of `MainActivity`.

ZXing also provides online [QR Code Generator](https://zxing.appspot.com/generator). Enter the required fields, generate and scan it to get the results.

ZXing Android Embedded lib also provides various customization -

- [Customizing `IntentIntegrator` class](https://github.com/journeyapps/zxing-android-embedded#usage-with-intentintegrator)
- [Directly embedding custom components in app](https://github.com/journeyapps/zxing-android-embedded/blob/master/EMBEDDING.md)

For more advanced options, have a look at their [Sample application](https://github.com/journeyapps/zxing-android-embedded/blob/master/sample/src/main/java/example/zxing/MainActivity.java).

That's it for this tutorial. Thanks to the ZXing authors for Java library and ZXing Android Embedded to make this integration easy. Enjoy scanning QR code. 🙂
