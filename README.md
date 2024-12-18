# Web SDK Setup Guide
 - **Basic Setup**
	 - [Intro](#basic-setup) 
	 - [Add Web SDK initialization script to your website](#initialization) 
	- [Tracking page visits](#track-visits)
	- [Targeting visitors by email](#targeting-visitors-by-email)
	- [Tracking new registrations](#tracking-new-registrations)
	- [Tracking login for existing users ](#tracking-login-for-existing-users)

  - **Advanced Setup**
	 - [Intro](#advanced-setup)
 	- [Reporting Custom Events](#reporting-custom-events) 
 
  - **Web Push Implementation**
	- [Pre-requisites](#web-push-prerequisites)
	- [Generating a Safari push certificate](#safari-push-certificate)
	- [Service Worker](#service-worker)
	- [Prompt Configuration](#web-push-prompts)
	- [Testing](#web-push-testing)

  - **Web Push on iOS**
    - [Requirements](#ios-web-push-gotchas)
----------

## <a id="basic-setup"></a>Basic Setup
Use the basic setup of the Web SDK in order to:

-   Implement  [Track & Trigger](https://academy.optimove.com/hc/en-us/categories/8357811674397-Track-and-Trigger)
-   Implement  [Google Display Network](https://github.com/optimove-tech/GDN/blob/newImplementation/README.md)  execution channel
- Implement [Web Push Notifications](#web-push-prerequisites)

### <a id="initialization"></a> Add the Web SDK initialization script to your website


The following code snippet must be added to your website on every page load, either by adding it into the relevant site template files/code or using a website tag manager (such as  [Google Tag Manager code snippet](https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/tree/main/Web-SDK-Basic-Code-Setup)) This code will load and initialize the SDK.
```html
<script async src="https://sdk.optimove.net/v2/websdk/?tenant_id=<YOUR_TENANT_ID>&tenant_token=<YOUR_TENANT_TOKEN>"</script>
```
> **Note:** 
>- Remember to replace  **<YOUR_TENANT_ID>** and **<YOUR_TENANT_TOKEN>** ,with the actual details that you receive from Optimove’s Integration Team.

For example:  
```html
<script async src="https://sdk.optimove.net/v2/websdk/?tenant_id=000&tenant_token=99999wwwwwwwAAAAABBB"></script>
```
## <a id="track-visits"></a>Tracking Page Visits 

In order to track page visits, call the setPageVisit() function on every page of the website to ensure that accurate user counts and session time metrics are collected.For customers, If a user is identified you can add the SDK_ID to the setPageVisit() function, see examples below

```javascript
// PageURL: The page URL (string, required)
// PageTitle: The page title (string, required)
// PageCategory: The page category (string, optional)
optimoveSDK.API.setPageVisit(PageURL, PageTitle, PageCategory);
```

**Example usage for Visitor:**

```javascript
// general shared function to captures page visits
function updateSDKPageVisit (PageURL, PageTitle, PageCategory) {
// SDK function
optimoveSDK.API.setPageVisit(PageURL, PageTitle, PageCategory);
}
// variables indicating the page info
var PageURL = 'https://www.myshop.com/clothes/unisex/shirts?item=123456';
var PageTitle = 'Some Shirt Name';
var PageCategory = 'Clothes Unisex Shirts';

// calling the shared function with the relavant variables
updateSDKPageVisit (PageURL, PageTitle, PageCategory);

```

**Example usage for Customer:**

```javascript

// PageURL: The page URL (string, required)
// PageTitle: The page title (string, required)
// PageCategory: The page category (string, optional)
// The SDK_ID refers to the unique/primary customer ID used by your website to identify registered customers/users.

var PageURL = 'https://www.myshop.com/clothes/unisex/shirts?item=123456';
var PageTitle = 'Some Shirt Name';
var PageCategory = 'Clothes Unisex Shirts';
var SDK_ID = '12345'

optimoveSDK.API.setPageVisit(PageURL, PageTitle, PageCategory, SDK_ID);

```
>**Note:**
>
>- Every page view is recorded, including repeated visits to the same page.
>
>- The Optimove SDK setPageVisit() function does not support callback functions.
>
>- "customURL", and "pageTitle" are mandatory parameters.
>
>- Make sure to send the full URL, e.g. 'https://www.example.com'
>
>- Call this function with the SDK_ID upon every page load/visit when you recognize the logged in user <br />

## <a id="targeting-visitors-by-email"></a> Targeting visitors by email

Whenever the website captures a visitor’s email address, such as when a visitor submits a newsletter, you can call a custom event that includes the ‘email’ parameter. After using a custom event including an email parameter, the visitor may be targeted by an email execution channel  

**Example usage:**  Targeting visitors after submitting a newsletter 
```javascript
var parameters = {
      email: 'john@gmail.com',
      first_name: 'John',
      optin: true,
      brand: 'example',
}
// passing the variables to the SDK function
optimoveSDK.API.reportEvent(‘newsletter_reg’,parameters)
```

> **Note:**
>-   The "email" is a required variable and must be a "string" format.
>-   Make sure the email is valid 



## <a id="tracking-new-registrations"></a>  Tracking New Registrations

Whenever a single user action requires a new registration (e.g., registration, newsletter signup), you should use the **registration event**.

**Example usage 1:**  Registration without callback function:

```javascript

// example variables
var SDK_ID = 'JohnDoe';

var parameters = {
      email: 'john@gmail.com',
      first_name: 'John',
      optin: true,
      brand: 'example',
      source: 'footer'    
}


// passing the variables to the SDK function
optimoveSDK.API.reportEvent('registration',parameters,null,SDK_ID)
```

**Example usage 2:**  Registration with callback function to report additional custom event/s:
```javascript 

// example variables
var SDK_ID = 'JohnDoe';

var parameters = {
      email: 'john@gmail.com',
      first_name: 'John',
      optin: true,
      brand: 'example',
      source: 'footer'    
}

var callback = function() { 
	optimoveSDK.API.reportEvent('items_in_cart', {'product_name': 'shirt','product_price':7}); 
	};

// passing the variables to the SDK function
optimoveSDK.API.reportEvent('registration',parameters,callback,SDK_ID)
```
**Notes:**

>-   You should include the email parameter if you want to target the user via email.


## <a id="tracking-login-for-existing-users"></a>Tracking login for existing users
In all cases, where you detect a user login call the **login event**

**Example usage 1:**  login without callback function:
```javascript
// example variables
var SDK_ID = 'JohnDoe';

var parameters = {
      brand: 'example',    
}

// passing the variables to the SDK function
optimoveSDK.API.reportEvent('login',parameters,null,SDK_ID)
```

**Example usage 2:**  login with callback function:
```javascript
// example variables
var SDK_ID = 'JohnDoe';

var parameters = {
      brand: 'example',    
}

var callback = function() { 
	optimoveSDK.API.reportEvent('player_balance', {balance: 5}); 
	};

// passing the variables to the SDK function
optimoveSDK.API.reportEvent('login',parameters,callback,SDK_ID)
```
**Notes:**

>-   Event names and parameters must first be configured within the Optimove instance using the  [Events Configuration Screen](https://academy.optimove.com/en/article/configuring-events-in-optimove)
>
>-   Event and parameter names are case sensitive.
>
>-   Events and parameters use lowercase and snake_case as a naming convention. Separate each word with one underscore character (_) and no spaces. (e.g., checkout_completed)
>
>-   The parameter types available for use in event-reporting functions are: >  **String**  – A series of alphanumeric characters of up to 255 characters in length, using any encoding >**Number**  – Any numeric value, whether an integer or a value containing a decimal point >**Boolean**  – Is either "true" or "false" values, not a string
>
>-   All monetary values must be reported in the same currency defined in your Optimove instance (e.g., if your instance is based on US dollars, all monetary event values must be reported in dollars). Optimove will not perform currency conversions.
>
>-   If your Optimove instance supports multiple languages, all event parameters must use a single default language. This is required in order to maintain a unified set of events.
>
>- The callback function must be sent as null when it is not in use
>
>-   **In order to ensure the correct order of arrival, utilize the Optimove SDK callback functions.**

#  <a id=advanced-setup></a>Advanced Setup
### <a id="intro-advanced"></a> Intro  
The  **Advanced Setup**  includes everything in the  [Basic Setup](#basic-setup)  as well as reporting custom events.

Following your Basic Setup SDK deployment, Optimove's Product Integration Manager will setup a call to help you create and implement the custom events.

> **Note:**  The Basic Setup is a pre-requisite to the Advanced one.

### <a id="reporting-custom-events"></a> Reporting Custom Events

Your website reports a predefined event to Optimove by using JavaScript to call  **reportEvent()**  in this format:
```javascript
optimoveSDK.API.reportEvent(<event_name>, <parameter JS object>);
```
In case a user is identified you can add the SDK_ID to the reportEvent() function in this format:
```javascript
optimoveSDK.API.reportEvent(<event_name>, <parameter JS object>,null,SDK_ID);
```

**Example usage 1:**  reportEvent() without callback function for unidentified users 
```javascript
// an function for adding a product to a specific wish list
function addToWishList(list_name, pid, pname, price) {
   var params = {};
	   params['list_name'] = list_name;
	   params['pid'] = pid;
	   params['name'] = pname;
	   params['price'] = price;
   optimoveSDK.API.reportEvent ('add_to_wishlist',params);
}

// calling the add to wish list function with the relavant data
addToWishList('my wish list 1', 123456, 'product name', 1.99);
```
**Example usage 2:**  reportEvent() without callback function for identified users 
```javascript
// an function for adding a product to a specific wish list
function addToWishList(list_name, pid, pname, price) {
   var params = {};
	   params['list_name'] = list_name;
	   params['pid'] = pid;
	   params['name'] = pname;
	   params['price'] = price;
   optimoveSDK.API.reportEvent ('add_to_wishlist',params,null,SDK_ID);
}

// calling the add to wish list function with the relavant data
addToWishList('my wish list 1', 123456, 'product name', 1.99);
```
**Example usage 3:**  reportEvent() with callback function for unidentified users 
```javascript
// a function for adding a product to a specific wish list 
//and updating the current size of the wish list 
function addToWishList(list_name, pid, pname, price) { 
	var params = {}; 
		params['list_name'] = list_name; 
		params['pid'] = pid; 
		params['name'] = pname; 
		params['price'] = price; 
		
	var callback = function() { 
	optimoveSDK.API.reportEvent('wishlist_size_change', {wishlist_size: 5}); 
	}; 

optimoveSDK.API.reportEvent ('add_to_wishlist', params, callback); 
} 

// calling the add to wish list function with the relavant data 
addToWishList('my wish list 1', 123456, 'product name', 1.99);
```
**Example usage 4:**  reportEvent() with callback function for identified users 
```javascript
// a function for adding a product to a specific wish list 
//and updating the current size of the wish list 
function addToWishList(list_name, pid, pname, price) { 
	var params = {}; 
		params['list_name'] = list_name; 
		params['pid'] = pid; 
		params['name'] = pname; 
		params['price'] = price; 
		
	var callback = function() { 
	optimoveSDK.API.reportEvent('wishlist_size_change', {wishlist_size: 5}); 
	}; 

optimoveSDK.API.reportEvent ('add_to_wishlist', params, callback,SDK_ID); 
} 

// calling the add to wish list function with the relavant data 
addToWishList('my wish list 1', 123456, 'product name', 1.99);
```
> **Note:**
> >-   Event names and parameters must first be configured within the Optimove instance using the  [Events Configuration Screen](https://academy.optimove.com/hc/en-us/articles/8738483087517-Configuring-Events-in-Optimove)
> -   Event and parameter names are case sensitive.
> -   Events and parameters use lowercase and snake_case as a naming convention. Separate each word with one underscore character (_) and no spaces. (e.g., checkout_completed)
> -   The parameter types available for use in event-reporting functions are:  
>     **String**  – A series of alphanumeric characters of up to 255 characters in length, using any encoding  
>     **Number**  – Any numeric value, whether an integer or a value containing a decimal point  
>     **Boolean**  – Is either "true" or "false" values, not a string  
>     
> -   All monetary values must be reported in the same currency defined in your Optimove instance (e.g., if your instance is based on US dollars, all monetary event values must be reported in dollars). Optimove will not perform currency conversions.
> -   If your Optimove instance supports multiple languages, all event parameters must use a single default language. This is required in order to maintain a unified set of events.


## <a id="web-push-implementation"></a>Web Push Implementation

### <a id="web-push-prerequisites"></a>Pre-requisites
For implementing our web push channel, please ensure that the [Basic Setup](#basic-setup) steps above have been completed to add the SDK to your site, and tracking of page visits is implemented. Once completed, follow the instructions below to finish the required setup for web push.

### <a id="safari-push-certificate"></a>Generating a Safari push certificate

To support sending notifications to desktop Safari, configuration of the following items is necessary:
-	Site URL
-	Icon (square, >=512px)
-	APNS certificate
To generate an APNS certificate, you need access to an Apple Developer account.
In the Apple Developer console, [add a new identifier for your website](https://developer.apple.com/account/resources/identifiers/add/websitePushId).

<img src="https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/blob/185182-safari-certificate-docs/images/safari-push/safari-cert-1.png">
 
Next, navigate to the [certificates list](https://developer.apple.com/account/resources/certificates/list). From here, add a new certificate.
For the type of certificate, choose Services > Website Push ID Certificate:
 
 <img src="https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/blob/185182-safari-certificate-docs/images/safari-push/safari-cert-2.png">

Generate a Certificate Signing Request (CSR) using the Keychain Access app:
 
 <img src="https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/blob/185182-safari-certificate-docs/images/safari-push/safari-cert-3.png">

 <img src="https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/blob/185182-safari-certificate-docs/images/safari-push/safari-cert-4.png">
 
Upload the generated CSR into the developer console:
 
  <img src="https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/blob/185182-safari-certificate-docs/images/safari-push/safari-cert-5.png">

Download the issued certificate:
 
  <img src="https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/blob/185182-safari-certificate-docs/images/safari-push/safari-cert-6.png">

Once downloaded, open the certificate file to add it to your keychain.
Now export the certificate & private key from the Keychain access tool as a P12 file:

 <img src="https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/blob/185182-safari-certificate-docs/images/safari-push/safari-cert-7.png">
 
Finally, upload the certificate into the Web push config settings, and fill in the p12 password field:  

 <img src="https://github.com/optimove-tech/Web-SDK-Integration-Guide-V3/blob/185182-safari-certificate-docs/images/safari-push/safari-cert-8.png">


### <a id="service-worker"></a>Service Worker
To enable support for web push notifications, it is necessary to host a Service Worker file with the following content:

```javascript
importScripts('https://static.app.delivery/sdks/web/optimove-worker.js');
```
> If you have an existing service worker, you can add this line to your existing implementation.


By default, the SDK will look for a service worker called `worker.js`, hosted at the root of your domain. For example, if your site is `https://example.com`, the SDK will search in the following path: `https://example.com/worker.js`.

You can customize the location path and the name of the service worker by passing an attribute to the Optimove script tag - `data-optimove-service-worker-path` , for example:

```html
<script async src="https://sdk.optimove.net/websdk/?tenant_id=000&tenant_token=99999wwwwwwwAAAAABBB" data-optimove-service-worker-path="/custom/path/to/yourWorker.js"></script>
``` 

Web push notifications also require your site to be served securly over TLS with the `https` scheme.

For more information on our web push channel please contact your CSM.

### <a id="web-push-prompts"></a>Prompt Configuration

In order to send web push notifications, it is necessary to request permission from the user. The Optimove SDK provides a permission prompt system that can be configured in the Settings section of Optimove UI.

By default, a notification Bell prompt will be configured to be shown in response to the set page visit event.

To customize the default prompt appearance, or add additional prompts, see the Messaging > Configuration > Web Push section in the Settings section of Optimove UI.

> Note that prompt definitions are cached in the browser for an hour, and clearing browser storage may be necessary whilst testing changes to prompts.

### <a id="web-push-testing"></a>Testing

#### Checking Integration

When you open your site in a supported browser, you can check that the SDK has been initialized correctly by selecting the project in the Settings section of Optimove UI and clicking the Installs tab to see the fifty most recent installs of the SDK. Click on any install to see more information.

In this view you can also search by customer ID if you have set the customer ID using the SDK.

#### Sending a Test Push

Once you have opened your site, and granted permission for notifications using a prompt, look for your record in the Installs tab of the Settings section of Optimove UI. From here, you can send a test push directly to your browser. Click on an install record to expand it, select the 'Push' tab, and click Send Test Push.

If you do not receive the push notification, you can check the Error Log for any errors sending the push notification by accessing Messaging, Configuration, then selecting the Error Log tab.

### <a id="ios-web-push-gotchas"></a>Requirements
As of Safari 16.4, Safari for iOS supports subscribing to push notifications. In order for these to work, a number of prerequisites must be met:
  - The application implementing the Optimove SDK must also serve a manifest.json file, specifying the display mode as "standalone". More info on this can be found here: https://developer.mozilla.org/en-US/docs/Web/Manifest
  - Users wishing to receive web push notifications must add the website to their home screen. Only when opened from the home screen can they subscribe to web notifications
  - Currently, some devices with a compatible iOS version don't have the Push API and Notifications API enabled in Safari settings. Users which don't have these enabled in their experimental features will not be able to subscribe to push notifications until they do so.
