Welcome!
========

Thanks for checking out the Pocket SDK. With a few lines of code, your app can quickly add support for saving URLs to users' Pocket lists.

Setup
=====

1. Get an API key from http://getpocket.com/api/
2. Add the contents of the SDK folder to your app. If you already include SFHFKeychainUtils, you should not include the copy bundled with the SDK.
3. Add the Security framework to your application's linker phase
4. In your app delegate's application:didFinishLaunchingWithOptions: method, add this line: `[[PocketAPI sharedAPI] setAPIKey:@"Put Your API Key Here"];`
5. Replace the `"Put Your API Key Here"` bit with the API key you got in step 1.

Usage
=====

The PocketAPI class represents a singleton for saving stuff to a user's Pocket list. To begin, you will need to obtain an API token from https://getpocket.com/api/ and set it on the PocketAPI singleton at some point at the beginning of your application's lifecycle. APIs are presented in one of four ways, but all behave fundamentally the same. Their differences are presented for flexibility for your app. You can use:

- a delegate-based API
- a block-based API
- an NSOperation based on a delegate (for advanced uses)
- an NSOperation based on a block (for advanced uses)

All delegates and blocks are called on the main thread, so you can safely update UI from there.

You can find more information on these in PocketAPITypes.h

You will have to present your own login form to the user using UI appropriate for your app. You should check the loggedIn property on the PocketAPI to see if you should present a login UI. Once you have a username and password, call one of the login methods below. If it succeeds, your delegate or block will be notified and the user's credentials will be saved automatically to the keychain.

Once the user is logged in, and manually decides to save a URL to their Pocket list, you can call one of the save APIs to save the URL. If you get no error back, the save succeeded, and you should notify the user that their item was saved successfully to their Pocket list.
If you get an error back, there are a few status codes you should keep an eye out for:

- 401: This means the user's account information is invalid and you should prompt to login again.
- 503: This means the server is temporarily down. The error will contain a message explaining why.

The Save API also will inform you through an argument that the user needs to login again or not.

These classes are not implemented as ARC, but will interoperate with ARC. You will need to add the -fno-objc-arc compiler flag to each of the files in the SDK if you are using ARC in your app.

Sample Use
==========

To login:

```
// get these from your login UI
NSString *username = ...; 
NSString *password = ...;

// login
[[PocketAPI sharedAPI] loginWithUsername:username password:password handler:^(PocketAPI *api, NSError *error){
	if(error){
		// login failed, show an error to the user
		NSLog(@"Could not log in to Pocket: %@", error);
	}else{
		// login successful, update your UI here
	}
}];
```

To save a URL to the user's Pocket list:

```
// get this from your application
NSURL *url = ...;

// perform the save
[[PocketAPI sharedAPI] saveURL:url handler:^(PocketAPI *api, NSURL *url, NSError *error, BOOL needsToRelogin){
	if(error){
		if(needsToRelogin){
			// the user's login information changed elsewhere, prompt them to login here
			NSLog(@"User needs to login again. %@", error);
		}else{
			// a server issue happened, inform the user their URL will not be saved
			NSLog(@"Could not save the item to Pocket: %@", error);
		}
	}else{
		// URL saved successfully, update your UI here
	}
}];
```

Acknowledgements
================

The Pocket SDK uses the following open source software:

- [SFHFKeychainUtils](https://github.com/ldandersen/scifihifi-iphone/tree/master/security) for saving user credentials securely to the keychain.

License
=======

Copyright (c) 2012 Read It Later, Inc.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.