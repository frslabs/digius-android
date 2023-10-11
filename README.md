# DIGIUS SDK (ANDROID)
![android](https://img.shields.io/badge/Android-3DDC84?style=flat&logo=android&logoColor=white) ![version](https://img.shields.io/badge/version-v1.2.0-blue)


Digius SDK is a one-shot solution to securely fetch documents from DigiLocker. It helps to eliminate use of physical documents and enables sharing of e-documents which is stored in DigiLocker account.

You can find the latest changelog at [Changelog](CHANGELOG.md).

# Table Of Content

- [Prerequisite](#prerequisite)
- [Set Up Digius SDK](#set-up-digius-sdk)
    - [Requirements](#requirements)
    - [Download Digius SDK](#download-digius-sdk)
    - [Importing Digius SDK](#importing-digius-sdk)
- [Quick Start](#quick-start)
    - [Initialise and run the Digius SDK](#initialise-and-run-the-digius-sdk)
- [Digius Result](#digius-result)
- [Digius Error Codes](#digius-error-codes)
- [Digius SDK API](#digius-sdk-api)
- [Permissions](#permissions)
- [Help](#help)

## Prerequisite

You will need a valid license to use the Digius SDK, which can be obtained by contacting `support@frslabs.com` .

Depending on the license - offline or online - you have opted for, the ping functionality to billing servers will be disabled or enabled. For instance, if you have opted for the offline SDK model, then there will be no server ping needed to our billing server to bill you. However, if you have chosen a transaction based pricing, then after each transaction, a ping request will be made to our billing server. This cannot be overrided by the App. A point to note is that if the ping transaction fails for any reason, the whole transaction will be void without any results from the SDK.

Once you have the license , follow the below instructions for a successful integration of Digius SDK onto your Android Application.

## Set Up Digius SDK

#### Requirements
Make sure that your project meets these requirements:
- Uses API Level `21` or above as `Minimum SDK Version`
- Uses Gradle 4.1 or later
- Uses Jetpack (AndroidX), which includes meeting these version requirements:
  com.android.tools.build:gradle v3.2.1 or later

#### Download Digius SDK
Add the following code to your `project` level `build.gradle` file

```groovy
allprojects {
    repositories {

        //Maven credentials for the Digius SDK
        maven {
            url "https://digius-sdk-android.repo.frslabs.space/"
            credentials {
                   username '<YOUR_USERNAME>'
                   password '<YOUR_PASSOWRD>'
            }
        }

        /*
        *Include below code only for transaction based billing
        */
        //Maven credentials for the Torus SDK
        maven {
            url "https://torus-android.repo.frslabs.space/"
            credentials {
                username '<YOUR_USERNAME>'
                password '<YOUR_PASSOWRD>'
            }
        }

    }
}
```

#### Importing Digius SDK

Add the following code to your `app-level` `build.gradle` file

```groovy
android {

    // ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }

    // ...

}
```

And then, add the dependencies
```groovy

// ...

dependencies {
    
    /*
     * Android Dependencies used by Digius SDK
     */
    implementation 'androidx.appcompat:appcompat:<latest verison>'
    implementation 'com.google.android.material:material:<latest verison>' 
    implementation 'androidx.constraintlayout:constraintlayout:<latest verison>'

    /*
     * Digius SDK Core Dependency
     */
    implementation 'com.frslabs.android.sdk:digius:<latest version>'

    /*
     * Digius Billing Dependency (Include only if transaction based billing is enabled)
     */
    implementation'com.frslabs.android.sdk:torus:1.2.1'
}
```

## Quick Start

#### Initialise and run the Digius SDK

Initialize an instance of `Digius` with the appropriate parameters, and call `start()` to invoke the Digius SDK. Refer [Digius SDK API](#digius-sdk-api) for more information regarding the individual APIs.

Given below is the fully configured Digius SDK invokation.

```kotlin
  class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
      super.onCreate(savedInstanceState)
      setContentView(R.layout.activity_main)
      callDigiusSdk()
    }
  
    private fun callDigiusSdk() {
      val digiusConfig = DigiusConfig.Builder()
        .setLicenseKey(YOUR_LICENSE_KEY)
        .setShowInstruction(true)
        .build()
  
      val digius = Digius(digiusConfig)
  
      digius.start(this, object : DigiusResultCallback {
        override fun onSuccess(digiusResult: DigiusResult) {
          if (digiusResult.getDocumentType() == DigiusDocumentType.AADHAAR) {
            val data = digiusResult.getAadhaarData()
            Toast.makeText(this@MainActivity, data.toString(), Toast.LENGTH_SHORT).show()
          }
          if (digiusResult.getDocumentType() == DigiusDocumentType.PAN_CARD) {
             val data = digiusResult.getPanData()  //FOR PAN
            }
        }
  
        override fun onFailure(code: Int, message: String) {
          Toast.makeText(this@MainActivity, String.format("%s %s", code, message), Toast.LENGTH_SHORT).show()
        }
      })
    }
  }
```

For all `errorCode`'s and their meanings refer [Digius Error Codes](#digius-error-codes).

## Digius Result

You can use the following methods in the `DigiusResult` instance to parse the success result:

| Return Type              | Method                        | Usage                                                            |
| ------------------------ | ----------------------------- | ---------------------------------------------------------------- |
| DigiusDocumentType(Enum) | *getDocumentType()*           | Returns selected document type                                   |
| AadhaarResult            | *getAadhaarData()*            | Returns aadhaar data if document type is aadhaar                 |
| PanResult                | *getPanData()*                | Returns PAN data if document type is PAN                         |

`getAadhaarData()` returns `AadhaarResult` instance with following methods:

| Return Type | Method                               | Usage                            |
| ----------- | ------------------------------------ | ---------------------------------|
| String      | *getDocumentNumber()*                | Returns document number          |
| String      | *getName()*                          | Returns document's name          |
| String      | *getAge()*                           | Returns age                      |
| String      | *getDob()*                           | Returns date of birth            |
| String      | *getGender()*                        | Returns gender                   |
| String      | *getAddress()*                       | Returns full address             |
| String      | *getHouse()*                         | Returns house name               |
| String      | *getLandmark()*                      | Returns landmark                 |
| String      | *getLocation()*                      | Returns location                 |
| String      | *getDistrict()*                      | Returns district                 |
| String      | *getState()*                         | Returns state                    |
| String      | *getCountry()*                       | Returns country                  |
| String      | *getPincode()*                       | Returns pincode                  |
| String      | *getPhoto()*                         | Returns user's photo             |
| String      | *getAadhaarLastFourDigits()*         | Returns last four digits of aadhaar     |
| String      | *getAadhaarIssuedDate()*             | Returns aadhaar issued date and time     |

`getPanData()` returns `PanResult` instance with following methods:
| Return Type | Method                               | Usage                            |
| ----------- | ------------------------------------ | ---------------------------------|
| String      | *getNumber()*                        | Returns document number          |
| String      | *getName()*                          | Returns document's name          |
| String      | *getDateOfBirth()*                   | Returns date of birth            |
| String      | *getGender()*                        | Returns gender                   |

Available document types are

  | Value                       | Document            |
  | --------------------------- | ------------------- |
  | DigiusDocumentType.AADHAAR  | Aadhaar Card        |
  | DigiusDocumentType.PAN      | PAN Card            |


## Digius Error Codes

Following error codes will be returned on the `onError` method of the callback

| CODE | DESCRIPTION                                     |
| ---- | ------------------------------------------------|
| 401  | Invalid licence                                 |
| 402  | Licence expired                                 |
| 403  | User canceled                                   |
| 404  | Unable to ping                                  |
| 405  | Transaction limit exceeded                      |
| 406  | No internet connection                          |
| 407  | Document read permission denied                 |
| 408  | Invalid settings                                |
| 409  | Failed to retrieve data                         |

## Digius SDK API

See the below table for the public APIs of Digius SDK,

##### Digius
| Method/Constructor                                   | Comments    |
|:---------------------------------------------------- |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Digius(DigiusConfig digiusConfig)                                                 | Instantiates the Digius Object |    
| *start(Context activityContext, DigiusResultCallback digiusResultCallback)*   | Starts the Digius SDK |


##### DigiusConfig
`DigiusConfig.Builder()` allows to instantiate the `DigiusConfig` object with customisable features. `DigiusConfig` is to be set when instantiating `Digius` object , See [Digius](#digius)
| Method                                               | Default              | Required | Comments    |
|:---------------------------------------------------- |:-------------------- |:-------- |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| *setLicenceKey(String licenseKey)*                   | NULL                 | Yes      | Sets the License Key needed for Digius SDK                          |
| *setShowInstructions(boolean showInstructions)*      | false                | Optional      | If it is **true** then the instruction screen will be shown to the user before digilocker screen.                         |
| *build()*   | -               | -      | Builds DigiusConfig Instance  |

## Permissions

Digius SDK uses the following permissions, they will be automatically merged into the app manifest.

```
android.permission.INTERNET
android.permission.ACCESS_NETWORK_STATE
```

## Help
For any queries/feedback , contact us at `support@frslabs.com`
