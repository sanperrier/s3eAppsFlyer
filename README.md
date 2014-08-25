s3eAppsFlyer
============

Marmalade extension for http://appsflyer.com SDK.  
Supports Android and iOS platforms.


On iOS used AppsFlyer iOS SDK v2.5.3.8  
On Android used AppsFlyer Android SDK v1.5.2  



HOW TO USE
---------

Add the following to the MKB file for your project:

    options 
    {
        #Adjust path as necessary:
        module_path="../s3eAppsFlyer" 
    }

    subprojects
    {
        s3eAppsFlyer
    }

Then add to your `app.icf`

    [APPSFLYER]
    appleAppId="Add your App Store application id here (for android this not needed)"
    devKey="Add your AppsFlyer dev key here"
    isHTTPS=true
    autoStart=true
    currencyCode="ISO currency code (optional)"

Where  
`isHTTPS` key is optional. Default is false.  
`autoStart` key is optional. Default is false.  


If you set `autoStart` to true then this is all you need to start tracking installs, updates and sessions. 
(For Android you must also make sure you using right AndroidManifest.xml).  
If you wish track additional in-app events beyond app installs (including in-app purchases, game levels, etc.) 
then you must use [API](#api-reference).

If you don't want to use .icf file or you want to set your own custom user ID then you must call `s3eAppsFlyerStartSession`.

Android notes
-------------
The AndroidManifest.xml should include the following permissions:
```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
```

* READ_PHONE_STATE permission is optional.
Adding this permission will enable carrier tracking of Android_id and IMEI (required for tracking out of Google Play).

This three permission is automatically included if you use this extension. If you want to remove some of them then edit file s3eAppsFlyer/appsflyer_perm.xml.

Also for AppsFlyer to work there must be AppsFlyer receiver section in AndroidManifest.xml and it must be first.  
There is example AndroidManifest.xml that you can use in s3eAppsFlyerExample folder.  
Just copy it into you project's folder and add to deployments section of you project's mkb:


    android-manifest=AndroidManifest.xml


### Google Advertiser Id
Google Play Services library .jar included in extension (s3eAppsFlyer/lib/android/google-play-services.jar)
If you want to replace it with newer version don't forget to put version metadata
```xml
<meta-data android:name="com.google.android.gms.version" android:value="5089000" />
```
to _appsflyer_app.xml_, taken from _android-sdk\extras\google\google_play_services\libproject\google-play-services_lib\res\values\version.xml_.



API Reference
-------------

### Data Structures


```c++
typedef struct s3eAppsFlyerConversionData
{
    /// Keys from dictionary returned to onConversionDataReceived. The pointer is valid until your callback returns.
    char** m_Keys;

    /// Values from dictionary returned to onConversionDataReceived. The pointer is valid until your callback returns.
    char** m_Values;

    /// Number of keys and values in m_Keys and m_Values arrays. Will be zero if there was error.
    int m_Num;
};
```

This struct stores conversion data. This data kept valid until your callback returns.


### Methods


```c++
s3eBool s3eAppsFlyerAvailable();
```
Returns S3E_TRUE if the AppsFlyer extension is available.

<br/>


```c++
s3eResult s3eAppsFlyerStartSession(const char* appleAppId S3E_DEFAULT(NULL), const char* devKey S3E_DEFAULT(NULL), const char* currencyCode S3E_DEFAULT(NULL), s3eBool isHTTPS S3E_DEFAULT(S3E_FALSE), const char* customerUserID S3E_DEFAULT(NULL) );
```
Must be first method called. Initializes SDK. Called automatically if `autoStart` set to `true`.


<br/>


```c++
void s3eAppsFlyerTrackEvent(const char* eventName, const char* value S3E_DEFAULT(NULL));
```
Allows you to send in-app events to AppsFlyer analytics. This method allows you to add events dynamically 
by adding them directly to the application code.  
Params:

- eventName is any string to define the event name . For example: "registration" or "purchase"
- value is the sales value. For example: "0.99" or "0.79"


<br/>

```c++
void s3eAppsFlyerSetHTTPS(s3eBool isHTTPS);
```
If you wish to force the SDK to use HTTPS at all times, please call s3eAppsFlyerSetHTTPS(S3E_FALSE)


<br/>


```c++
void s3eAppsFlyerSetTrackingDisable(s3eBool _disable);
```
AppsFlyer provides you a method to opt-out specific users from AppsFlyer analytics.  
This method complies with the latest privacy requirements and complies with Facebook data and privacy policies.


<br/>


```c++
void s3eAppsFlyerSetCurrencyCode(const char* currencyCode);

```
Set currency code:  
USD is default value. Acceptable ISO Currency codes [here](http://www.xe.com/iso4217.php). Examples:  
British Pound:
`s3eAppsFlyerSetCurrencyCode("GBP");`  
US Dollar:  
`s3eAppsFlyerSetCurrencyCode("USD");`  


<br/>

```c++
void s3eAppsFlyerSetCustomerUserID(const char* customerUserID);
```
Set customer user ID with the SDK and reporting (used to match with the client internal ID's).  
See section 8 of [doc](http://support.appsflyer.com/attachments/token/ornpe0dk5bwye1f/?name=AF-iOS-Integration-Guide-v2.5.3.2-New-API.pdf) for more details.


```c++
const char * getAppsFlyerUID();
```
Get AppsFlyer's proprietary device ID. AppsFlyer device ID is the main ID used by AppsFlyer in the Reports and API's.


<br/>


```c++
void s3eAppsFlyerLoadConversionData();
```
Gets conversion data using loadConversionDataWithDelegate method of AppsFlyer SDK.  
Data returned in callback S3E_APPSFLYER_GOT_CONVERSION_DATA.


<br/>

