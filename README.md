#Table of Contents
- [1. System Requirements](#user-content-1-system-requirements)
- [2. SDK Initialization](#user-content-2-sdk-initialization)
	- [a. Import the Library](#user-content-a-import-the-library-to-your-project)
	- [b. Initialize the SDK ](#user-content-b-initialize-the-sdk)
	- [c. Adding R1Connect.properties](#user-content-c-adding-r1connect-properties)
	- [d. Update the manifest](#user-content-d-update-the-manifest)
	- [e. Advance Settings](#user-content-e—advance-settings)
- [3. Feature Activation](#user-content-3-feature-activation)
	- [a. Engage Activation](#user-content-a-engage-activation)
		- [i. Ad Mediation SDKs Setup (Optional)](#user-content-i-ad-mediation-sdks-setup)
		- [ii. Integration of Offerwall, Interstitial and Video](#user-content-ii-integration-of-offerwall-,-interstitial-and-video)
		- [iii. Integration of Banner](#user-content-iii-integration-of-banner)
		- [iv. Ad Listener Events](#user-content-iv-ad-listener-events)
		- [v. Ad Check Completion](#user-content-v-ad-check-completion)
	- [b. Analytics Activation](#user-content-b-analytics-activation)
		- [i. Automatic Events](#user-content-i-automatic-events)
		- [ii. Standard Events](#user-content-ii-standard-events)
		- [iii. Custom Events](#user-content-iii-custom-events)
		- [iv. Best Practices](#user-content-iv-best-practices)
	- [c. Push Notification Activation](#user-content-c-push-notification-activation)
	 	- [i. Setup Google Cloud Messaging](#user-content-i-setup-google-cloud-messaging)
		- [ii. Initialization](#user-content-ii-initialization)
		- [iii. Rich Push Initialization](#user-content-iii-rich-push-initialization)
		- [iv. Rich Push Customization](#user-content-iv-rich-push-customization)
		- [v. Inbox Initialization](#user-content-v-inbox-initialization)
		- [vi. Deep Link Initialization](#user-content-vi-deep-link-initialization)
		- [vii. Segment your Audience](#user-content-vii-segment-your-audience)
	- [d. Attribution Tracking Activation](#user-content-d-attribution-tracking-activation)
		- [i. Track RadiumOne Campaigns](#user-content-i-track-radiumone-campaigns)
		- [ii. Track 3rd party Campaigns](#user-content-ii-track-3rd-party-campaigns)
	- [e. Geofencing Activation](#user-content-e-geofencing-activation)
		- [i. SDK Initialization](#user-content-i-sdk-initialization)
		

#1. System Requirements
The R1 Connect SDK supports all mobile and tablet devices running Android 2.3 and above. Google Play Services must be 4.0 or above. 

**Note:** We assume you have already set up Google Play Services in your project as it’s needed for using the Advertising ID.


#2. SDK Initialization

## a. Import the library to your project

If you don’t have a RadiumOne Engage account, go to http://control.radiumone.com/gwallet-network-admin/register/ and create a Publisher account by selecting the “I want to monetize my app/site” or “Both” option (tools for both publishing and advertising).

If you already have an Advertiser account at RadiumOne Engage, just add “Account type: Publisher” option on the account page.

The login page is available at: http://control.radiumone.com/gwallet-network-admin/.

Drag the “LibConnectEngage.jar” files into your project's libs directory. 

For Eclipse, add the lib to the build path. 

This document assumes you have added the Google Play Services project in your eclipse project. If you have not, add the Google Play Services project and reference it in your application. 

<img src="https://raw.github.com/radiumone/r1-connect-demo-Android/master/readme-images/image1.png"></img>
 
In Android studio, the build.gradle should include the library and Google Play Services as a dependency.

```java
dependencies {
   compile files('libs/LibConnectEngage.jar')
   compile ‘com.google.android.gms:play-services:4.3.+'
}
```


##b. Initialize the SDK

Add Google Play Services to the application manifest:

```java
<meta-data
	android:name="com.google.android.gms.version"
	android:value="@integer/google_play_services_version" />
```

In the top level activity of your application, initialize the R1Emitter with the Application Context in onCreate() method.

```java
package com.example.yourpackagename; 
import com.radiumone.emitter.R1Emitter; 
import android.app.Application;

public class TopLevelActivity extends Activity{
@Override
	public void onCreate() {
		super.onCreate();
		R1Emitter.getInstance().connect(getApplicationContext()); 
	}
}
```

i. **Initializing when Engage Enabled**  

Add R1Publisher activity in AndroidManifest.xml

```java
<activity
	android:name="com.radiumone.engage.publisher.R1Publisher" 
	android:windowSoftInputMode="stateHidden" 
	android:configChanges="orientation|keyboardHidden|screenSize"/>
```
Override onDestroy method for the activities which show Ads. 
```java

@Override
protected void onDestroy() {
	super.onDestroy();
	R1AdServer.getInstance().onDestroy(this);
}
```
ii. **Initializing when Analytics Enabled**  

 Override onStart and onStop methods for each activity:

```java
@Override
protected void onStart() {
	super.onStart();
	R1Emitter.getInstance().onStart(this);
}

@Override
protected void onStop() {
	super.onStop();
	R1Emitter.getInstance().onStop(this);
}
```
Set the user Id with your own user Id. It can be a social network id like Facebook, Google, Twitter or unique user Id.

```java
R1AdServer.getInstance(this).setUserId("< User ID>"); 
```

##c. Adding R1Connect.properties

To configure how the library will be used in your project you will need to create a file called r1connect.properties in the assets directory of your project.

<img src="https://raw.github.com/radiumone/r1-connect-demo-Android/master/readme-images/image2.png"/></img>

If you are using Android studio, assets directory resides in Application/src/main.

Here is the list of ids that has to be specified in r1connect.properties


```ruby

app_id=<YOUR APPLICATION_ID> 
push.client_key=<YOUR CLIENT KEY>

# default value for analytics when application starts -- set to false to disable analytics in SDK
analytics.enable=true

# default value for push when application starts -- set to false to use the SDK without push
push.enable=true

# default value for geofencing when application starts -- set to false to use the SDK without geofencing
geofencing.enable=true

# default value for engage when application starts  — set to false to use SDK without engage
engage.enable=true

# default value is set to false for engage mediation when application start -  set to true to use SDK with mediation
mediation.enable = true

#allow location tracking by gps
analytics.enable_gps=false

#location update timeout for analytics sdk. Default 600 sec
analytics.location_update_time=600

#location update distance for analytics sdk. Default 100 meters
analytics.location_update_distance=100

#allow location tracking in background for analytics sdk. Default false
analytics.location_in_background=false

#prevent location tracking for analytics sdk
analytics.disable_sdk_location=false

#allow installed applications tracking. Default true
analytics.enable_apps_list=true

```

As you can see in the example above, it will contain the following:

• 	app_id – You will need to enter the App ID you received when creating your app on R1 Connect (found under Dev Tools -> Keys & Secrets).

• 	analytics.enable - set to "true" enables analytics in Engage SDK and otherwise disables it.

• 	engage.enable – when set to “true” it enables the use of Engage SDK and when set to “false” is disabled.

•       mediation.enable - when set to "true" it enables ad mediation. The sdk mediates among selected ad networks to serve the best possible ad When set to false it disables ad mediation and fetch the ad from Engage network.

• 	push.client_key – You will need to enter the App Key you received when creating your app on R1 Connect (found under Dev Tools -> Keys & Secrets).

• 	analytics.disable_sdk_location - when set to “true” it disables the use of analytics sdk tracking location. It is useful if you want to use your own tracking location. You can pass a location object like so:
	R1Emitter.getInstance().trackLocation(location);

• 	analytics.enable_gps – when set to “true” it enables the use of device GPS to get location (only if GPS is enabled in device settings), when “false” only the network is used for analytics sdk.

• 	geofencing.enable – when set to “true” Geofencing SDK is enabled otherwise disabled.

• 	analytics.location_update_time – set the timeout between location updates for analytics sdk.

• 	analytics.location_update_distance – set the change in distance for location updates for analytics sdk.

• 	analytics.location_in_background – set whether or not the location is allowed in analytics sdk to be sent while the app is in the background.

• 	analytics.enable_apps_list – set whether or not collecting of the installed apps is enabled.  By default, this property is set to **true**.  You MUST change this to false if you do not want to collect the list of installed apps.  You must also inform your users that you will be collecting this information in your store listing.  An example disclaimer is below:

“This app facilitates the transmission of a list of installed apps on your device to a third party's server. This information will be used to provide you with advertisements that are better suited to your interests. If you do not agree to the transmission of a list of your installed apps to a third party, please do not install this app.”

##d. Update the manifest
In your manifest, add the permissions below:

```java
	<uses-permission android:name="android.permission.INTERNET" /> 
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> 
	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" /> 
	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

Properly declare your application class:

```java
<application
	android:name="com.example.yourpackagename.TestApplication" 
	android:allowBackup="true"
	…
	<service android:name="com.radiumone.emitter.location.LocationService"/>
</application>
```


##e. Advance Settings

###i. Configuration Parameters in R1Engage

Prior to showing ads you can add these optional parameters in the code:

```java
R1AdServer.getInstance(this).setAge("<Optional Age>"); 
```

```java
R1AdServer.getInstance(this).setGender("<Optional Gender>"); 
```

where:

**```<Optional Age>```** is user's age 

**```<Optional Gender>```** is user's gender

These parametes are helpful  to serve more finely targeted ads.



###ii. Configuration Parameters in R1Emitter

The following is a list of configuration parameters for the R1 Connect SDK, most of these contain values that are sent to the tracking server to help identify your app on our platform and to provide analytics on sessions and location.

**appName**

The application name associated with emitter. By default, this property is populated with the package name of the application. If you wish to override this property, you must do so before making any tracking calls.

```java
R1Emitter.getInstance().setApplicationName("customApplicationName");
```

**appId**

The application identifier associated with this emitter. By default, this property is null. If you wish to set this property, you must do so before making any tracking calls. Note that this is not your app's bundle id (e.g. com.example.appname).

```java
R1Emitter.getInstance().setApplicationUserId("12345");
```

**appVersion**

The application version associated with this emitter. By default, this property is populated with the android:versionName= string from the application AndroidManifest.xml. If you wish to override this property, you must do so before making any tracking calls.

```java
R1Emitter.getInstance().setAppVersion("1.0");
```

**sessionTimeout**

Indicates how long, in seconds, the application must transition to the inactive or background state before the tracker automatically indicates the start of a new session. When this happens and the app becomes active again it will set sessionStart to true. For example, if this is set to 30 seconds, and the user receives a phone call that lasts for 45 seconds while using the app, upon returning to the app the sessionStart parameter will be set to true. If, on the other hand, the phone call lasts 10 seconds, sessionStart will not be modified. By default, this is 30 seconds.

```java
R1Emitter.getInstance().setSessionTimeout(30);
```

**applicationUserID**

Optional current user identifier.

```java
R1Emitter.getInstance().setApplicationUserId("12345");
```

###iii. Obfuscation of the code

To avoid issues after obfuscating, you must add the following lines to your proguard-rules.txt file:

```ruby
-keepclassmembers class com.radiumone.engage.publisher.R1AndroidNativeInterface { public *;
}
```

#3. Feature Activation

##a. Engage Activation

### i. Ad Mediation SDKs Setup 

**Adding Admob SDK and its adapter**

Make sure you have your account setup in Admob website and have created all the ad placements required by your application. Admob SDK is supported by Google play services. So it does not require you to download the SDK. Add the proper version of Google play services in build.gradle as explained above. 

Here is the startup guide for Admob Integration

https://developers.google.com/mobile-ads-sdk/docs/admob/android/quick-start


As explained in Admob Quick start guide, add the Ad view Activity in the manifest.
```java
<activity android:name="com.google.android.gms.ads.AdActivity"
            android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"
            android:theme="@android:style/Theme.Translucent" />
```

Don't add the **com.google.android.gms.ads.AdView** in the resource file as mentioned in the guide.


Add the AdmobAdapter.jar library to the libs directory and refer it to your application.


**Adding Mopub SDK and its adapter**

Make sure you have your Mopub account setup in Mopub website and have created all the ad placements required by your application. Here is the link to integrate Mopub SDK with your application,

https://github.com/mopub/mopub-android-sdk/wiki/Getting-Started

After integrating Mopub SDK with your application, add the MopubAdapter.jar library to the libs directory and refer it to your application.



### ii.  Integration of Offerwall, Interstitial and Video


Import the following header files.
```java
import com.radiumone.engage.advertiser.R1AdvertiserClient;
import com.radiumone.engage.mediation.R1AdServer;
```
To show a full-screen product you must add the following (where context is your Activity):

#### a. Engage Only

For Engage Ad network,  set the track Id(unique string) per ad placement which will be used to check for ad completion.

```java

Bundle adUnitIds = new Bundle();
adUnitIds.put(R1AdServer.ADAPTER_ENGAGE, "Add Engage Track Id for Offerwall"); // can be any unique string to track if offerwall ad  completion served by Engage.

R1AdServer.getInstance(context).showOfferwall(adUnitIds); 


Bundle adUnitIds = new Bundle();
adUnitIds.put(R1AdServer.ADAPTER_ENGAGE, "Add Track Id for Engage");  // can be any unique string to track if interstitial ad  completion served by Engage.


R1AdServer.getInstance(context).showInterstitial(adUnitIds); 

Bundle adUnitIds = new Bundle();
adUnitIds.put(R1AdServer.ADAPTER_ENGAGE, "Add Track Id for Engage");  // can be any unique string to track if interstitial ad  completion served by Engage.

R1AdServer.getInstance(context).playVideo(adUnitIds);
```
#### b. Engage With Mediation

If mediation is turned on, add the ad placement keys to the bundle and pass it along when showing the ad type. Some ad networks support particular ad types. If the ad network does not support particular ad type or you have not create placement key then don't insert in the bundle.

```java

Bundle adUnitIds = new Bundle();
adUnitIds.put(R1AdServer.ADAPTER_ADMOB, "Add Admob placement Id for Offerwall if available"); // if mediation is turned on and Admob SDK is integrated
adUnitIds.put(R1AdServer.ADAPTER_MOPUB, "Add Mopub placement Id for Offerwall if available");  // if mediation is turned on and Mopub SDK is integrated
adUnitIds.put(R1AdServer.ADAPTER_ENGAGE, "Add Engage Track Id for Offerwall"); // can be any unique string to track if offerwall ad  completion served by Engage.

R1AdServer.getInstance(context).showOfferwall(adUnitIds); 


Bundle adUnitIds = new Bundle();
adUnitIds.put(R1AdServer.ADAPTER_ADMOB, "Add Admob placement Id for Interstitial if available"); // if mediation is turned on and Admob SDK is integrated
adUnitIds.put(R1AdServer.ADAPTER_MOPUB, "Add Mopub placement Id for Interstitial if available");  // if mediation is turned on and Mopub SDK is integrated
adUnitIds.put(R1AdServer.ADAPTER_ENGAGE, "Add Track Id for Engage");  // can be any unique string to track if interstitial ad  completion served by Engage.


R1AdServer.getInstance(context).showInterstitial(adUnitIds); 

Bundle adUnitIds = new Bundle();
adUnitIds.put(R1AdServer.ADAPTER_ADMOB, "Add Admob placement Id for Video if available");  // if mediation is turned on and Admob SDK is integrated
adUnitIds.put(R1AdServer.ADAPTER_MOPUB, "Add Mopub placement Id for Video if available");  // if mediation is turned on and Mopub SDK is integrated
adUnitIds.put(R1AdServer.ADAPTER_ENGAGE, "Add Track Id for Engage");  // can be any unique string to track if interstitial ad  completion served by Engage.

R1AdServer.getInstance(context).playVideo(adUnitIds);
```

### iii.  Integration of Banner

Import the following header files that integrates banners

```java
import com.radiumone.engage.mediation.R1AdServer;
import com.radiumone.engage.publisher.R1EngageBannerSize;
import com.radiumone.engage.publisher.R1EngageBannerView;

```

The SDK supports several predefined banner types:

```ruby
R1EngageBannerSize.BANNER_300_50
R1EngageBannerSize.BANNER_320_50
R1EngageBannerSize.BANNER_300_250
R1EngageBannerSize.BANNER_480_50
R1EngageBannerSize.BANNER_728_90
R1EngageBannerSize.BANNER_1024_90
```

#### a. Engage Only 

To add a banner of a predefined type you must add the following (where type is a predefined value from the above list):
```java
 Bundle adUnitBannerIds = new Bundle();
adUnitBannerIds.putString(R1AdServer.ADAPTER_ENGAGE, "Add Track Id for Engage");  // can be any unique string to track  banner ad  completion served by Engage.
R1AdServer.getInstance(this).showBanner(bannerContainer, R1EngageBannerSize.BANNER_320_50, adUnitBannerIds);
              
  ```    
  where bannerContainer is Viewgroup to hold the banner ad. You can create banner Viewgroup programmatically or in xml layout of the view hierarchy.    
```java
<FrameLayout
 android:layout_width="match_parent"
 android:layout_height="wrap_content"
 android:id="@+id/banner_container"/>
                 
  ``` 
#### b. Engage with Ad Mediation 
To add a banner when ad mediation is enabled. Get the placement Ids for all the ads from ad networks.
```java
 Bundle adUnitBannerIds = new Bundle();
 adUnitBannerIds.putString(AdServer.ADAPTER_ADMOB,  "Add Admob placement Id for Banner if available"); // if 	  mediation is turned on and Admob SDK is integrated);
 adUnitBannerIds.putString(R1AdServer.ADAPTER_MOPUB, "Add Mopub placement Id for Banner if available"); // if mediation is turned on and Mopub SDK is integrated);
adUnitBannerIds.putString(R1AdServer.ADAPTER_ENGAGE, "Add Track Id for Engage");  // can be any unique string to track  banner ad  completion served by Engage.
R1AdServer.getInstance(this).showBanner(bannerContainer, R1EngageBannerSize.BANNER_320_50, adUnitBannerIds);
              
  ```    
  where bannerContainer is Viewgroup to hold the banner ad. You can create banner Viewgroup programmatically or in xml layout of the view hierarchy.    
```java
<FrameLayout
 android:layout_width="match_parent"
 android:layout_height="wrap_content"
 android:id="@+id/banner_container"/>
                 
  ```                  

### iv. Ad Listener Events
You can listen to panels using the R1EngageNotifier object. Add it before the start of any full-screen product or banner:

Import the header file
```java
import com.radiumone.engage.publisher.R1EngageNotifier;
```

Add r1engageNotifier listener that will listen to ad events.

Here is an example:

```java
private R1EngageNotifier r1engageNotifier = new R1EngageNotifier() {
        @Override
        public void didAdReceiveNewReward(int rewards) {
           
        }

        @Override
        public void didAdClosed() {
            Toast.makeText(R1EngageDemo.this, "Closed", Toast.LENGTH_SHORT).show();
        }

        @Override
        public void didAdHasOffers(final String adNetwork) {
            

        }

        @Override
        public void didAdError() {
              }

        @Override
        public void didAdHasNoOffers() {
             }
    };
```

```java
R1AdServer.getInstance(this).setR1EngageNotifier(r1engageNotifier);
```

**didAdReceiveNewReward** - This method is invoked when Engage offer is completed. Since each offer has its own virtual currency, it will be pass down to this method after its completion.

**didAdClosed** - This method is invoked when the ad is closed and control is back to main application.

**didAdHasOffers** - This method is invoked when ad with offers is loaded. The ad Network name is passed to this method which loads up the ad. 

**didAdError** - This method is invoked when ad network fails to load the ad.

**didAdHasNoOffers** - This method is invoked when no ad network is available to fill the ad.

### v. Ad Check Completion

Import the header file. 

```java
import com.radiumone.engage.publisher.R1CheckAdCompletion;
```

Add check Completion listener. This listener will listen to the completed events when checkCompletion method is invoked.
```java
 R1AdServer.getInstance(this).setCheckCompletion(new R1CheckAdCompletion() {
            @Override
            public void getCompletedTrackIds(ArrayList<String> completedList) {

            }
        });
```

Invoke the checkCompletion method to get the completed Ad trackIds.

```java
 R1AdServer.getInstance(this).checkCompletions(this);
 ```
 
##b. Analytics Activation
### i. Automatic Events

The R1 Connect SDK will automatically capture some generic events in order to get the most meaningful data on how users interact with your application. These events are triggered when the state of the application is changed, and therefore do not require any additional code to work out of the box:

**Launch** - emitted when the app starts

**First Launch** - emitted when the app starts for the first time

**First Launch After Update** - emitted when the app starts for the first time after a version upgrade

**Application Opened** - emitted when your app is opened after a message is sent

**Session Start** - emitted when a new session starts

**Session End** - emitted when a session ends; includes a Session Length attribute with the session length in seconds


### ii. Standard Events

Standard Events cover all the main user flows (login, register, share, purchase...) in a standardized format for optimized reporting on the portal, providing a great foundation for your analytics strategy. They unlock great insights, particularly with respect to user lifetime value.

*Note: The last argument in all of the following emitter callbacks, otherInfo, is a HashMap of “key”,”value” pairs or null, which enables you to customize these events as much as you want.*

**Login**

Tracks a user login within the app

```java
R1Emitter.getInstance().emitLogin("userId", "userName", parameters);
// where parameters is a HashMap. Example:
private HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("key","value");
```

**Registration**

Records a user registration within the app

```java
R1Emitter.getInstance().emitRegistration("userId", "userName", "country", "city", "state", parameters);
// where parameters is a HashMap. Example:
private HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("key","value");
```

**Facebook connect**

Allows access to Facebook services

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");
R1Emitter.getInstance().emitFBConnect(socialPermissions, parameters);
```

Where permissions is a List of R1SocialPermissions:

```java
ArrayList<R1SocialPermission> socialPermissions = new ArrayList<R1SocialPermission>();
socialPermissions.add( new R1SocialPermission("permission", true));
```

**Twitter connect**

Allows access to Twitter services

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");
R1Emitter.getInstance().emitTConnect("userId", "username", socialPermissions, parameters);
```

Where socialPermissions is a list of R1SocialPermissions:

```java
ArrayList<R1SocialPermission> socialPermissions = new ArrayList<R1SocialPermission>();
socialPermissions.add( new R1SocialPermission("permission", true));
```

**User Info**

This event enables you to send user profiles to the backend.

```java
UserItem userItem = new UserItem();
userItem.userId = "userId";
userItem.userName = "userName";
userItem.firstName = "firstName";
userItem.lastName = "lastName";
userItem.email = "user@email.net";
userItem.streetAddress = "address";
userItem.phone = "123456";
userItem.zip = "111111";
userItem.city = "City";
userItem.state = "State";

HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitUserInfo(userItem, parameters);
```

**Upgrade**

Tracks an application version upgrade

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitUpgrade(parameters);
```

**Trial Upgrade**

Tracks an application upgrade from a trial version to a full version

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitTrialUpgrade(parameters);
```

**Screen View**

Basically, a page view, it provides info about that screen

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitAppScreen("title","description","http://www.example.com/path","example.com","path",parameters);
```

**Transaction**

```java
EmitItem purchaseItem = new EmitItem();
purchaseItem.storeId = "storeId";
purchaseItem.storeName = "name";
purchaseItem.transactionId = "AE3237DAA";
purchaseItem.cartId = "ABBCCD";
purchaseItem.orderId = "ABCDEF";
purchaseItem.totalSale = 3.2f;
purchaseItem.currency = "EUR";
purchaseItem.shippingCosts = 1.8f;
purchaseItem.transactionTax = 2.5f;

HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitTransaction(purchaseItem, parameters);
```

**TransactionItem**

```java
R1EmitterLineItem lineItem = new R1EmitterLineItem();
lineItem.productId = "productId";
lineItem.productName = "productName";
lineItem.quantity = 5;
lineItem.unitOfMeasure = "parts";
lineItem.msrPrice = 1.3f;
lineItem.pricePaid = 3.4f;
lineItem.currency = "EUR";
lineItem.itemCategory = "items";

HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitTransactionItem("transactionItemId", lineItem,  parameters);
```

**Create Cart**

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitCartCreate("cartId", parameters);
```

**Delete Cart**

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitCartDelete("cartId", parameters);
```

**Add To Cart**

```java
R1EmitterLineItem lineItem = new R1EmitterLineItem();
lineItem.productId = "productId";
lineItem.productName = "productName";
lineItem.quantity = 5;
lineItem.unitOfMeasure = "parts";
lineItem.msrPrice = 1.3f;
lineItem.pricePaid = 3.4f;
lineItem.currency = "EUR";
lineItem.itemCategory = "items";

HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitAddToCart("cartId", lineItem, parameters);
```

**Delete From Cart**

```java
R1EmitterLineItem lineItem = new R1EmitterLineItem();
lineItem.productId = "productId";
lineItem.productName = "productName";
lineItem.quantity = 5;
lineItem.unitOfMeasure = "parts";
lineItem.msrPrice = 1.3f;
lineItem.pricePaid = 3.4f;
lineItem.currency = "EUR";
lineItem.itemCategory = "items";

HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("custom_key","value");

R1Emitter.getInstance().emitRemoveFromCart("cartId", lineItem, parameters);
```


###iii. Custom Events



Custom events enable you to create and track specific events that are more closely aligned with your app. If planned and structured correctly, custom events can be strong indicators of user intent and behavior. Some examples include pressing the “like” button, playing a song, changing the screen mode from portrait to landscape, and zooming in or out of an image. These are all actions by the user that could be tracked with events.

To include tracking of custom events for the mobile app, the following callbacks need to be included in the application code:

```java
// Emits a custom event without parameters
R1Emitter.getInstance().emitEvent("Your custom event name");
// Emits a custom event with parameters
private HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("key","value");
R1Emitter.getInstance().emitEvent("Your custom event name", parameters);
```




###iv. Best Practices
####Event Naming Convention
One common mistake is to parameterize event names (with user data for example). Event names should be hard-coded values that you use to segment data on a specific category of event. 

Example: "ProfileViewing"

Avoid: "Profile Viewing - Lady Gaga's profile"

As you may have thousands of user profiles in your database, it is preferable to keep the event name high-level ("ProfileViewing") so you can run interesting analytics on it. A high-level event name will help answer a question like: How many profiles does a user visit every day on average? 

####Parameter Variance

Another common mistake is to add parameters to the event that have too many possible values. To follow up on the previous example, one may decide to add the number of profile followers as an event parameter:

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("profileFollowers",profileFollowers);
R1Emitter.getInstance().emitEvent("ProfileViewing", parameters);
```
			  			   
Again, the problem here is that each profile may have any number of followers. This will fragment data too much to extract any valuable information.

Instead, a good strategy would be to define relevant buckets that replace the need for high variance parameters. In this case, separate traffic on profiles with a high follower count from traffic on profiles with a low count. You could define 3 categories: 

- "VERY_INFLUENTIAL" for profiles > 100,000 
- "INFLUENTIAL" for profile > 10,000 and <= 100,000
- "NON_INFLUENTIAL" for profile <= 10,000

In this case, a proper event could be: 

```java
HashMap<String, Object> parameters = new HashMap<String, Object>();
parameters.put("profileFollowersBucket","VERY_INFLUENTIAL");
R1Emitter.getInstance().emitEvent("ProfileViewing", parameters);
```
			  			   
This will enable you to create more insightful reports.

##c. Push Notification Activation

###i. Setup Google Cloud Messaging

####Prerequisites for Google Cloud Messaging Setup
This doc assumes you have already set up Google Play Services in your application project. Google Play Services is needed to use Google Cloud Messaging (GCM), the notification gateway RadiumOne Connect will use for your Android app. Also if you have not done so already, you will need to add the app you will be using to the RadiumOne Connect portal in order to add the Google API Key to it once you have generated the API Key following the steps below.

####Configuring your App for GCM
######Obtain an API key from Google
In order to use RadiumOne Connect with your application you will need an API key from Google. We will summarize those steps here, but for more info on this process please visit “GCM Getting Started” [here](http://developer.android.com/google/gcm/gs.html).

1\. Create a Google API project in Google APIs Console (take note of your project number which is the value after #project: it will be used later as your GCM sender ID)

<img src="https://raw.github.com/radiumone/r1-connect-demo-Android/master/readme-images/image101.jpg"></img>

2\. Enable the GCM Service

<img src="https://raw.github.com/radiumone/r1-connect-demo-Android/master/readme-images/image102.jpg"></img>

3\. Create a new Server key in the Google APIs Console page under API Access. when prompted, allow access to ip 0.0.0.0/0 

<img src="https://raw.github.com/radiumone/r1-connect-demo-Android/master/readme-images/image103.jpg"></img>

<img src="https://raw.github.com/radiumone/r1-connect-demo-Android/master/readme-images/image104.jpg"></img>

4\. Copy the key, it is used for GCM Connection Servers and for RadiumOne Connect setup

######Setting up the API Key on R1 Connect

1\. Please make sure you are signed into your account on RadiumOne Connect and go to the application you want to add this API key to.

2\. Next, in the side menu go to Dev Tools > Push Services > Google Cloud Messaging.

<img src="https://raw.github.com/radiumone/r1-connect-demo-Android/master/readme-images/image105.jpg"></img>

3\. Add your API Key to the API Key field and click Save.

<img src="https://raw.github.com/radiumone/r1-connect-demo-Android/master/readme-images/image106.jpg"></img>

If it saved correctly you will see a green badge with a white checkmark in it. Now your app is setup with GCM.



###ii. Initialization

To make sure push notifications work correctly, please follow these steps:

Configure the r1connect.properties file in your project:

• push.sender_id – You will need to enter the project number you received when creating your Google API project.

• push.enable – This defaults to “true” and will enable or disable push notifications after you start your application


Create a class that inherits from the class Application (or you can use an existing one in the project)

To enable an action such as opening the app when a notification is clicked, create a class that inherits from BroadcastReceiver and add the necessary logic to it. If you are okay with the default, which closes the notification upon pressing it, then no further coding is required.

```java
public class TestPushReceiver extends BroadcastReceiver {

    public void onReceive(Context context, Intent intent) {
        try{
            Context applicationContex = context.getApplicationContext();
            if ( intent != null && intent.getAction() != null){
                if (intent.getAction().equals(R1Push.OPENED_NOTIFICATION)){
                    Intent openIntent = new Intent(context, ShowNotificationActivity.class);
                    openIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK|Intent.FLAG_ACTIVITY_CLEAR_TOP|Intent.FLAG_ACTIVITY_SINGLE_TOP);
                        if ( intent.getExtras() != null ){
                            openIntent.putExtras(intent.getExtras());
                        }
                        applicationContex.startActivity(openIntent);
                    }
                }
            } catch (Exception ex){
        }
    }
}
```


Then update the application class:

```java
public class MyApplication extends Application{
    @Override
    public void onCreate() {
        super.onCreate();
        // drawable in notification bar
        R1Emitter.getInstance().setNotificationIconResourceId(this, R.drawable.notification_icon);
        // custom BroadcastReceiver
        R1Emitter.getInstance().setIntentReceiver(this, TestPushReceiver.class);
        R1Emitter.getInstance().connect(this);
    }
}
```

If you don't have an application class yet, create it and don't forget to reference it inside the Manifest:

```java
<!-- android:name of application tag must be full application name that was created in first step.-->
<application
    android:name="com.example.r1connecttestapplication.MyApplication"
    android:allowBackup="true"
    android:icon="@drawable/ic_launcher"
    android:label="@string/app_name"
    android:theme="@style/AppTheme" >
```

If you want to create your own notifications you have to create a class that implements R1NotificationBuilder interface and write your notification builder like in the example below:

```java
public class CustomNotificationBuilder implements R1NotificationBuilder {

    private int lastId;

    @Override
    public Notification createNotification(Context context, Intent intent) {
        if ( context == null || intent == null){
            return null;
        }
        String text = intent.getStringExtra(R1PushNotificationManager.NOTIFICATION_KEY);
        String title = intent.getStringExtra(R1PushNotificationManager.NOTIFICATION_TITLE);
        if ( text != null ){
            NotificationCompat.Builder nb = new NotificationCompat.Builder(context)
                .setAutoCancel(true)
                .setTicker(text)
                .setContentText(text)
                .setWhen(System.currentTimeMillis());
            if (!TextUtils.isEmpty(title)){
                nb.setContentTitle(title);
            } else {
                nb.setContentTitle("My Application Name");
            }
            nb.setDefaults(Notification.DEFAULT_ALL);
            nb.setSmallIcon(R.drawable.ic_launcher);
            Notification notification = nb.build();
            lastId++;
            return notification;
        }
        return null;
    }

    @Override
    public int getLastNotificationId() {
        return lastId;
    }
}
```

After that add this line just before R1Emitter.getInstance().connect(this) in your application class:
```java
R1Emitter.getInstance().setNotificationBuilder(new CustomNotificationBuilder());
```

Update the manifest as shown below.

Add the following inside the application tag:

```java
<!-- PUSH -->
<receiver android:name="com.radiumone.emitter.gcm.R1GCMPushReceiver" android:exported="true"
      android:permission="com.google.android.c2dm.permission.SEND" >
<intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <!-- name must be your applicationPackage -->
    <category android:name="###YOUR APPLICATION PACKAGE###" />
</intent-filter>
</receiver>
<receiver android:name="com.radiumone.emitter.push.R1PushBroadcastReceiver"
      android:exported="false">
<intent-filter>
    <action android:name="com.radiumone.r1push.OPENED_INTERNAL"/>
</intent-filter>
</receiver>
<service android:name="com.radiumone.emitter.push.R1ConnectService"/>
<service android:name="com.radiumone.emitter.location.LocationService"/>
//Using the class created in Step 2 (cont…):
<receiver android:name=".testpush.TestPushReceiver"
      android:exported="false">
<intent-filter>
    <action android:name="com.radiumone.r1push.OPENED"/>
</intent-filter>
</receiver>

<meta-data
    android:name="com.google.android.gms.version"
    android:value="@integer/google_play_services_version" />
<!-- END OF PUSH -->
```
Add the following  inside the manifest tag:

```java
<!-- PUSH -->
<!-- Permissions that are necessary for the library to work: -->
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<!-- Permission to get location when using the network -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<!-- Permission to get location when using GPS --> 
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.WAKE_LOCK"></uses-permission>
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<uses-permission android:name="android.permission.VIBRATE"/>
<permission android:name="###YOUR APPLICATION PACKAGE###.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
<uses-permission android:name="###YOUR APPLICATION PACKAGE###.permission.C2D_MESSAGE"></uses-permission>
<!-- END OF PUSH -->
```


###iii. Rich Push Initialization

Rich push notifications send a URL that opens upon user response to a system notification.  In addition to the general push setup requirements, if you would like to enable rich push notifications that include HTML/CSS/JavaScript you have to add the following data in the AndroidManifest.xml:

```java
<service android:name="com.radiumone.emitter.richpush.R1RichPushService" />
<activity
        android:name="com.radiumone.emitter.richpush.activity.R1RichPushActivity"
        android:configChanges="orientation|keyboardHidden|screenSize"
        android:label="@string/title_activity_rich_push"
        android:launchMode="singleTask"
        android:parentActivityName=".MainActivity"
        android:theme="@style/AppThemeCustomNoTitle"
        android:windowSoftInputMode="stateHidden">
        <meta-data
            android:name="android.support.PARENT_ACTIVITY"
            android:value=".MainActivity" />
</activity>
```

The parent activity for rich activity is the view accessed when selecting the back button.  Older platforms may require the meta-data attributes above.

@style/AppThemeCustomNoTitle is the window style.  If you would like to present the view without a title, use the following style:

```java
	<style name="AppThemeCustomNoTitle" parent="AppTheme">
        <item name="android:windowNoTitle">true</item>
    </style>
```        

Users that select rich push notifications will by default start com.radiumone.emitter.richpush.activity.R1RichPushActivity and start the HTML content.

###iv. Rich Push Customization

If you want to customize Rich Push then update your custom BroadcastReceiver ( TestPushReceiver ):

```java
Bundle extras = intent.getExtras();

if (extras != null) {
        // getting rich push ID - no rich push id means that push is simple and no rich content for it
        String richPushId = extras.getString(R1PushNotificationManager.RICH_PUSH);
        if (TextUtils.isEmpty(richPushId)) {
        openIntent = new Intent(applicationContext, ShowNotificationActivity.class);
        } else {
                openIntent = new Intent(applicationContext, R1RichPushActivity.class);
                openIntent.putExtra(R1RichPush.R1_RICH_PUSH_ID, richPushId);
        }
}

```

In this scenario, we check for rich push id in extras and start different activities for rich and regular push.

You can change the activity for Rich Push if you want to use fragments, toolbar, or another activity that you want to use as the Rich Push Activity. See the code below:

```java

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.text.TextUtils;
import android.util.Log;
import android.view.ViewGroup;

import com.radiumone.emitter.richpush.R1RichPush;
import com.radiumone.emitter.richpush.R1RichPushManager;
import com.radiumone.emitter.richpush.R1RichPushMessage;
import com.radiumone.emitter.richpush.R1RichPushWebView;

public class CustomPushActivity extends Activity implements R1RichPushManager.OnMessageReceivedListener{

    private String richPushId;
    private R1RichPushWebView webView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Bundle extras = getIntent().getExtras();
        if ( extras == null ){
            Log.i(CustomPushActivity.class.getSimpleName(), "Wrong parameters for " + R1RichPushActivity.class.getSimpleName());
            finish();
            return;
        }
        richPushId = extras.getString(R1RichPush.R1_RICH_PUSH_ID);
    }

    @Override
    protected void onNewIntent(Intent intent) {
        setIntent(intent);
        Bundle extras = getIntent().getExtras();
        if ( extras == null ){
            Log.i(CustomPushActivity.class.getSimpleName(), "Wrong parameters for " + R1RichPushActivity.class.getSimpleName());
            finish();
            return;
        }
        richPushId = extras.getString(R1RichPush.R1_RICH_PUSH_ID);
        loadRichPush();
    }

    private void loadRichPush() {
        if (TextUtils.isEmpty(richPushId)){
            Log.i(CustomPushActivity.class.getSimpleName(), "No rich_push_id in extras");
            finish();
            return;
        }
        R1RichPushMessage message = R1RichPushManager.getR1RichPushManager().getR1RichPushMessage(this, richPushId, this);
        if ( message != null ) {
            webView = new R1RichPushWebView(this);
            webView.setLayoutParams( new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
            setContentView(webView);
            if ( message.getStatus() == R1RichPushMessage.RESULT_OK ) {
                webView.loadR1RichPush(message);
            } else {
                Log.i(CustomPushActivity.class.getSimpleName(), "Error while getting rich push message");
                finish();
            }
        }
    }

    @Override
    protected void onPause() {
        super.onPause();
        if ( webView != null ){
            webView.onPause();
        }
        if ( !TextUtils.isEmpty(richPushId)) {
            R1RichPushManager.getR1RichPushManager().removeListener(richPushId, this);
        }
    }

    @Override
    protected void onResume() {
        super.onResume();
        if ( webView != null ){
            webView.onResume();
        } else {
            loadRichPush();
        }
    }

    @Override
    public void onResult(R1RichPushMessage message) {
        if ( message != null && message.getStatus() == R1RichPushMessage.RESULT_OK) {
            if (richPushId != null && richPushId.equals(message.getId())) {
                runOnUiThread( new Runnable() {
                    @Override
                    public void run() {
                        loadRichPush();
                    }
                });
            }
        } else {
            Log.i(CustomPushActivity.class.getSimpleName(), "No Connection while getting rich push message");
            finish();
        }
    }

    @Override
    public void onError(R1RichPushMessage message) {
        Log.i(CustomPushActivity.class.getSimpleName(), "Error while getting rich push message");
        finish();
    }
}

```

The important line in this class is

```java
//get R1RichPushMessage from R1RichPushManager 
R1RichPushMessage message = R1RichPushManager.getR1RichPushManager().getR1RichPushMessage(this, richPushId, this);
```

where

```java
R1RichPushManager.getR1RichPushManager().getR1RichPushMessage(Context context, String richPushId, R1RichPushManager.OnMessageReceivedListener receiveListener);
```

To make Rich Push work you have to use the following method of R1RichPushWebView:

```java
	webView.loadR1RichPush(message);
```

Next register your activity in AndroidManifest.xml (do not forget service android:name="com.radiumone.emitter.richpush.R1RichPushService"  because it necessary for both custom and default rich push activity).

```java
<service android:name="com.radiumone.emitter.richpush.R1RichPushService" />


<activity
            android:name="###YOUR APPLICATION PACKAGE###.CustomRichPushActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/title_activity_rich_push"
            android:launchMode="singleTask"
            android:parentActivityName=".MainActivity"
            android:windowSoftInputMode="stateHidden">
            <meta-data
                android:name="android.support.PARENT_ACTIVITY"
                android:value=".MainActivity" />
        </activity>
```

Then add this line just before R1Emitter.getInstance().connect(this) in your application class:

```java
R1Emitter.getInstance().setRichPushActivity(CustomRichPushActivity.class);
```

Now rich push will be opened in your custom activity.

###v. Inbox Initialization

If you want to enable inbox functionality you need to create a fragment or an activity that shows a list of Inbox messages and listen for messages updates in the Inbox. 
This reference source code shows how to implement Activity in which you can show and delete messages from Inbox (this activity must be registered in your AndroidManifest.xml). This also assumes that Rich Push functionality mentioned above is already integrated.

```java
public class R1InboxActivity extends Activity implements R1InboxManager.OnInboxUpdateListener, AdapterView.OnItemClickListener {

	// ListView for inbox messages
    private ListView listView;
    
    // Adapter for showing messages in listview
    private R1InboxMessageAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        // creating ListView programmatically, but it can be layout from xml
        listView = new ListView(this);
        listView.setLayoutParams(new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
        listView.setId(android.R.id.list);
        
        // creating Inbox adapter
        adapter = new R1InboxMessageAdapter(this);
        listView.setAdapter(adapter);
        
        // sets click listener for this Activity
        listView.setOnItemClickListener(this);
        
        // this line is for correct working delete buttons in list
        listView.setDescendantFocusability(ViewGroup.FOCUS_BLOCK_DESCENDANTS);
        
        setContentView(listView);
    }


	// method gets inbox messages and set to adapter for showing in list
    private void loadMessages() {
        final R1InboxMessage[] messages = R1InboxManager.getInboxManager().getR1InboxMessages(this);
        runOnUiThread( new Runnable() {
            @Override
            public void run() {
                if ( adapter != null ){
                    adapter.setInboxMessages(messages);
                }
            }
        });

    }

    @Override
    protected void onPause() {
        super.onPause();
        
        // remove inbox update listener when Activity goes to pause state
        R1InboxManager.getInboxManager().setInboxUpdateListener(null);
    }

    @Override
    protected void onStart() {
        super.onStart();
        
        // calls SDK onStart method
        R1Emitter.getInstance().onStart(this);
    }

    @Override
    protected void onStop() {
        super.onStart();
        
        // calls SDK onStop method
        R1Emitter.getInstance().onStop(this);
    }

    @Override
    protected void onResume() {
        super.onResume();
        
        // set inbox update listener when Activity resumes. After that all updates in inbox will invoke callback inboxUpdated() in this Activity
        R1InboxManager.getInboxManager().setInboxUpdateListener(this);
        
        // get actual messages after pause
        loadMessages();
    }


    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        if ( adapter != null ) {
        	// get R1InboxMessage object for pressed listview position
            R1InboxMessage inboxMessage = adapter.getItem(position);
            
            // call inboxMessageOpen() method for correct opening and showing inbox message  
            R1InboxManager.getInboxManager().inboxMessageOpen(this, inboxMessage);
        }
    }

    @Override
    public void inboxUpdated() {
    	// get actual messages after inbox updated
        loadMessages();
    }

    @Override
    public void inboxUpdateError() {
        Log.i(getClass().getSimpleName(),"Inbox update error");
    }


	// adapter for showing Inbox messages
    private class R1InboxMessageAdapter extends BaseAdapter {

        private final LayoutInflater inflater;
        
        // array of R1InboxMessage
        private R1InboxMessage[] listOfMessages;

        public R1InboxMessageAdapter(Context context){
            listOfMessages = new R1InboxMessage[0];
            inflater = (LayoutInflater)context.getSystemService(LAYOUT_INFLATER_SERVICE);
        }

        @Override
        public int getCount() {
            if ( listOfMessages == null ){
                return 0;
            }
            return listOfMessages.length;
        }

        @Override
        public R1InboxMessage getItem(int position) {
            return listOfMessages[position];
        }

        @Override
        public long getItemId(int position) {
            return position;
        }

        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            View view = convertView;
            ViewHolder viewHolder = null;
            if (view == null) {
            
            	// Layout for inbox item. Will be shown further.
                view = inflater.inflate(R.layout.r1_inbox_item, null);
                viewHolder = new ViewHolder();
                viewHolder.alert = (TextView)view.findViewById(R.id.alert);
                viewHolder.title = (TextView)view.findViewById(R.id.title);
                viewHolder.dateTime = (TextView)view.findViewById(R.id.datetime);
                viewHolder.readIndicator = view.findViewById(R.id.read_indicator);

                viewHolder.delete = (Button)view.findViewById(R.id.delete_button);
                
                // set OnClickListener for delete button
                viewHolder.delete.setOnClickListener( new View.OnClickListener() {
                    @Override
                    public void onClick(View v) {
                    	// if tag for pressed button is an instance of R1InboxMessage you have to call inboxMessageDelete method in R1InboxManager for correct deletion of message
                        Object tag = v.getTag();
                        if ( tag instanceof R1InboxMessage ) {
                            R1InboxManager.getInboxManager().inboxMessageDelete(R1InboxActivity.this, (R1InboxMessage) tag);
                        }
                    }
                });
                view.setTag(viewHolder);
            } else {
                viewHolder = (ViewHolder)view.getTag();
            }

            R1InboxMessage message = getItem(position);

			// set Tag for delete button. This needs for knowing what message must be deleted after button pressed.
            viewHolder.delete.setTag(message);
            
            viewHolder.alert.setText(message.getAlert());
            
            viewHolder.title.setText(message.getTitle());
            
            viewHolder.dateTime.setText( dateTimeLocalised( message.getDateTime() ));

			// checks that message is not readed and show it 
            if ( message.getReadState() == R1RichPushMessage.READ ){
                viewHolder.readIndicator.setVisibility(View.INVISIBLE);
            } else {
                viewHolder.readIndicator.setVisibility(View.VISIBLE);
            }

            return view;
        }

        public void setInboxMessages(R1InboxMessage[] inboxMessages){
            this.listOfMessages = inboxMessages;
            notifyDataSetChanged();
        }
    }

    private static class ViewHolder {
        TextView title;
        TextView alert;
        TextView dateTime;
        Button delete;
        View readIndicator;
    }

    private String dateTimeLocalised(long dateTime){
        dateTime -= TimeZone.getDefault().getOffset(dateTime);
        return DateFormat.getDateTimeInstance().format(dateTime);
    }

}
```

Layout R.layout.r1_inbox_item.xml from source code above:

```java
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="?listPreferredItemHeight"
    android:orientation="horizontal"
    android:padding="8dp">

    <View
        android:id="@+id/read_indicator"
        android:layout_width="10dp"
        android:layout_height="10dp"
        android:layout_alignParentLeft="true"
        android:layout_alignParentStart="true"
        android:layout_gravity="center_vertical"
        android:layout_margin="8dp"
        android:background="@drawable/shape" />

    <LinearLayout
        android:id="@+id/notification_content"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_toEndOf="@+id/read_indicator"
        android:layout_toLeftOf="@+id/delete_button"
        android:layout_toRightOf="@+id/read_indicator"
        android:layout_toStartOf="@+id/delete_button"
        android:orientation="vertical">

        <TextView
            android:id="@+id/title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ellipsize="marquee"
            android:maxLines="1" />

        <TextView
            android:id="@+id/alert"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ellipsize="marquee"
            android:maxLines="2" />
    </LinearLayout>

    <Button
        android:id="@+id/delete_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentEnd="true"
        android:layout_alignParentRight="true"
        android:focusable="false"
        android:text="Delete" />

    <TextView
        android:id="@+id/datetime"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/notification_content"
        android:layout_gravity="bottom"
        android:layout_toLeftOf="@+id/delete_button"
        android:layout_toStartOf="@+id/delete_button" />


</RelativeLayout>
```

R1InboxMessage is an object that has the following methods to display details of inbox messages:

***datetime***

```java
	inboxMessage.getDateTime()
```
Returns inbox message timestamp as epoch time in milliseconds

***title***

```java
	inboxMessage.getTitle()
```
Returns inbox message title text 

***alert***

```java
	inboxMessage.getAlert()
```
Returns inbox message alert text

***readState***

```java
	inboxMessage.getReadState()
```
Returns the read state of an inbox message, values are R1InboxMessage.READ/ R1InboxMessage.UNREAD

To display a number of unread messages use the following code:

```java
int unread = R1InboxManager.getInboxManager().getInboxUnreadCount(context);
```

To be notified for realtime updates of the number of unread messages you need to register R1InboxManager.OnInboxUpdateListener. To stop this updates you need to unregister it:

```java
R1InboxManager.getInboxManager().setInboxUpdateListener( new R1InboxManager.OnInboxUpdateListener() {
            @Override
            public void inboxUpdated() {
                // inbox updated
            }

            @Override
            public void inboxUpdateError() {
				// error occurred while inbox updated
            }
        });
```

###vi. Deep Link Initialization

Deep linking push messages open up a designated view in an application upon user response to a system notification.  To properly handle deep link push receipts, please read Android’s documentation on registering a custom URL scheme here:  http://developer.android.com/guide/topics/manifest/data-element.html
 

###vii. Segment your Audience    

You can specify Tags for *R1 Connect SDK* to send *Push Notifications* for certain groups of users.  You can then send *Push Notifications* to users with specific tags.

The maximum length of a Tag is 128 characters.

*R1 Connect SDK* saves Tags. You do not have to add Tags every time the application is launched.

***Add a new Tag***

```java
R1Push.getInstance(context).addTag("tag");
```

***Add multiple Tags***
	
```java
for ( String tag: tags) {
    R1Push.getInstance(this).addTag(tag);
}
```

***Remove existing Tag***

```java
R1Push.getInstance(context).removeTag("tag");
```

***Remove multiple Tags***

```java
for ( String tag: tags) {
    R1Push.getInstance(this).removeTag(tag);
}
```

***Replace all existing Tags***

```java
R1Push.getInstance(context).setTags(Arrays.asList("tag1","tag2"));
```

***Get all Tags***
	
```java
String[] allTags = R1Push.getInstance(context).getTags(context);
```

##c. Attribution Tracking Activation
###i. Track RadiumOne Campaigns
Please contact your Account Manager to setup R1 ad campaigns as well as tracking campaigns.  If you don't have one, please contact us [here](http://radiumone.com/contact-mobile-team.html) and one of our Account Managers will assist you.

Once your Account Manager has set up tracking, you will start receiving attribution tracking report automatically!

###ii. Track 3rd party Campaigns
1. Please contact your Account Manager to setup tracking URLs for your 3rd party campaigns.  If you don't have one, please contact us [here](http://radiumone.com/contact-mobile-team.html) and one of our Account Managers will assist you.
2. Send the list of all your media suppliers (anyone you run a mobile advertising campaign with).
3. For each media supplier, your account manager will send you 2 tracking URLs (one impression tracking URL, one click tracking URL).
4. Send each pair of URLs to the relevant Media Supplier so they can set these tracking URLs on the creatives
5. You're all set and will start having access to Attribution Tracking Reports



##e. Geofencing Activation
###i. SDK Initialization

Configure the r1connect.properties file in your project:

• geofencing.enable – This defaults to “true” and will enable or disable geofencing after you start your application


Update the Manifest as shown below.

```java
 	<service
            android:name="com.radiumone.beacon.service.BeaconService"
            android:enabled="true"
            android:exported="true"
            android:isolatedProcess="false"
            android:label="iBeacon" >
        </service>
        <service
            android:name="com.radiumone.beacon.BeaconIntentProcessor"
            android:enabled="true" >
        </service>

        <receiver
            android:name="com.radiumone.geofence_sdk.geofence.ReceiveTransitionsIntentReceiver">
        
            <intent-filter>
                <action android:name="com.radiumone.geofence_sdk.geofence.ACTION_RECEIVE_GEOFENCE" />
            </intent-filter>
             <intent-filter>
                <action android:name="com.radiumone.geofence_sdk.geofence.ACTION_RECEIVE_LOCATION" />
            </intent-filter>
        </receiver>
        
        <receiver android:name="com.radiumone.geofence_sdk.R1WakefulReceiver">
                <intent-filter>
                          <action android:name="android.intent.action.BOOT_COMPLETED"/>
                </intent-filter>
         </receiver>

    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
```






