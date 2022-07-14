# Web SDK Setup Guide
 - **Basic Setup**
	 - [Intro](#basic-setup) 
	 - [Add Web SDK script to your website / tag manager](#add-code) 
	- **Implementation for Visitors (unidentified users)**
		 - [Tracking Page Visits for Visitors](#track-visits-visitors) 
		 - [Reporting/Updating User Email Addresses](#record-email) 
	- **Implementation for Customers (identified users)**
	  	- [Stitching Website Visitors to Registered Customer IDs ](#link-visit-customer) 
	  	- [Registering the User ID and User Email at the Same Time](#record-user-email) 
		- [Tracking Page Visits for Customers](#track-visits-customers) 

  - **Advanced Setup**
	 - [Intro](#advanced-setup)
 	- [Reporting Custom Events](#custom-events) 
 
  - **Web Push Implementation**
	- [Service Worker](#web-push-implementation)
	- [Prompt Configuration](#web-push-prompts)
	- [Testing](#web-push-testing)

----------

### <a id="intro-basic"></a> Intro 
Use the basic setup of the Web SDK in order to:

-   Implement  [Track & Trigger](https://docs.optimove.com/track-and-trigger/)
-   Implement  [Google Display Network](https://github.com/optimove-tech/GDN/blob/newImplementation/README.md)  execution channel

### <a id="initialization"></a> Initialization
### [](https://github.com/optimove-tech/Web-SDK-Integration-Guide-V2#--add-the-optimove-web-sdk-script-to-your-website)Add the Optimove Web SDK script to your website

The following code snippet must be added to every page in your website, either by adding it into the relevant site template files/code or using a website tag manager (such as  [Google Tag Manager code snippet](https://github.com/optimove-tech/Web-SDK-Integration-Guide/blob/newImplemetationUpdate/Web-SDK-Basic-Code-Setup/readme.md)) This code will load and initialize the SDK.
```javascript
<script async src="https://sdk.optimove.net/websdk/?tenant_id={your_tenant_id}"></script>
```
> **Note:**  Remember to replace  **your_tenant_id**  ,with the actual details that you receive from Optimove’s Integration Team.
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

Whenever the website captures a visitor’s email address, such as when a visitor submits a newsletter you can call a custom event that includes the ‘email’ parameter. After using a custom event including email parameter the visitor may be targeted by an email execution channel  

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



## <a id="tracking-new-registrastions"></a>  Tracking New Registrations

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


## <a id="tracking-login-for-existing-users"></a>Tracking login for exsting users
In all cases,where you detect a user login call the **login event**

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
### <a id="intro-advanced"></a> intro  
The  **Advanced Setup**  includes everything in the  [Basic Setup](https://github.com/optimove-tech/Web-SDK-Integration-Guide#basic-setup)  as well as reporting custom events.

Following your Basic Setup SDK deployment, Optimove's Product Integration Manager will setup a call to help you create and implement the custom events.

> **Note:**  The Basic Setup is a pre-requisite to the Advanced one.

### <a id="reporting-custom-events"></a> Reporting Custom Events

Your website reports a predefined event to Optimove by using JavaScript to call  **reportEvent()**  in this format:
```javascript
optimoveSDK.API.reportEvent(<event_name>, <parameter JS object>);
```
In case a user is identified you can add the SDK_ID to the reportEvent() function in this format:
```javascript
optimoveSDK.API.reportEvent(<event_name>, <parameter JS object>,SDK_ID);
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
> >-   Event names and parameters must first be configured within the Optimove instance using the  [Events Configuration Screen](https://academy.optimove.com/en/article/configuring-events-in-optimove)
> -   Event and parameter names are case sensitive.
> -   Events and parameters use lowercase and snake_case as a naming convention. Separate each word with one underscore character (_) and no spaces. (e.g., checkout_completed)
> -   The parameter types available for use in event-reporting functions are:  
>     **String**  – A series of alphanumeric characters of up to 255 characters in length, using any encoding  
>     **Number**  – Any numeric value, whether an integer or a value containing a decimal point  
>     **Boolean**  – Is either "true" or "false" values, not a string  
>     
> -   All monetary values must be reported in the same currency defined in your Optimove instance (e.g., if your instance is based on US dollars, all monetary event values must be reported in dollars). Optimove will not perform currency conversions.
> -   If your Optimove instance supports multiple languages, all event parameters must use a single default language. This is required in order to maintain a unified set of events.
