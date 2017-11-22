# MPESA ANDROID SDK
This SDK allows android developers to easily make MPESA payments on their apps.

The SDK includes the following:
1. Android native  SDK to accept MPESA payments in less than 10 lines of code
2. A sample backend in Laravel you can easily deploy to your server to quickly get started
3. A sample SB schema for the backend
4. Integration with Firebase Cloud Messaging to easily send push notifications to the app after payments
5. A complete sample application you can easily run to see the SDK in action
6. A free hosted backend at my website mpesa.bdhobare.com you can use for testing purposes.
7. This nice README :(

## Getting started
To get started you need the following from the [Safaricom Developer dashboard](https://developer.safaricom.co.ke)
1. Your Consumer Key
2. Your Consumer Secret
3. The business shortcode
4. A Firebase project with a server key. Create one [here](https://console.firebase.google.com/)


For testing purposes, you can use get the Safaricom test credentials [here](https://developer.safaricom.co.ke/test_credentials)
Use the Lipa Na Mpesa Online Shortcode and Lipa Na Mpesa Online Passkey.

## Integrating with your app
1. Add the SDK to your project

    ``` compile 'com.bdhobare:mpesa:0.0.5'```
    
2. Initialize the SDK

    ```Mpesa.with(context, CONSUMER_KEY, CONSUMER_SECRET);```

3. Implement the `AuthListener` interface in your activity.The interface provides two methods

* ```public void onAuthError(Pair<Integer, String> result)```

    This method is called when initializing the Mpesa instance fails.You can get the response code using `result.code` and the error message using `result.message`. Make sure your credentials are correct

* ``` public void onAuthSuccess()``` 

    This method is called once the Mpesa instance initializes successfully.You can only make a successful transaction after this method is called.Therefore, you can use this method to update the user interface like enabling a disabled button.

## Making a transaction
Before making a transaction, your need to make sure you have the following items:
1. A business shortcode. For testing purposes , use [this](https://developer.safaricom.co.ke/test_credentials) one from Safaricom.
2. A pass key. Get one from the link in step 1 above for testing purposes.
3. The amount to transact.
4. The phone number making the payment. It can be in 07xx.. or 254xx.. or +254xx... The SDK will sanitize it for you.

To make a transaction , you build an `STKPush` instance and pass it to `pay()` method

```STKPush.Builder builder = new Builder(BUSINESS_SHORT_CODE, PASSKEY, amount,BUSINESS_SHORT_CODE, phone);```

```STKPush push = builder.build();```

```Mpesa.getInstance().pay(context, push);```

Hurray! You you have made an MPESA transaction.

##Customising the transaction
Most of the times you want to receive the transaction details on your server, store them in your database and then send a confirmation notification to the user.

I have include a complete working backend in Laravel you can easily deploy to your server.

For building the sample project, you don't need to deploy your own backend. I have already set up one for you [here](https://mpesa.bdhobare.com/mpesa) to use freely. Just replace the credentials and hit run :)

### Setting up the backend.
1. Clone the backend from [here]().If you are new to Laravel you can quickly catch up [here](https://www.parthpatel.net/laravel-tutorial-for-beginner-5-4/).
2. Upload the repository to your server's webroot and set up the necessary configurations(Apache, Composer, Laravel, MySQL/PostgreSQL etc).
3. Create a sample database in Postgresql or Mysql and connect it with the project by editing the `.env` file.
4. Migrate by running `php artisan migrate` from the project root.
5. Get the link to your url. I am going to assume you have deployed it to `http://YOUR_URL`

Set up the callback url to the `STKPush.Builder` by calling this method:

```builder.setCallBackURL(http://YOUR_URL/mpesa)```. Please note that the url is `YOUR_URL/mpesa` not `YOUR_URL`

###Setting up Firebase Notifications

1. To send a confirmation notification to the user, you need to add Firebase to your app by following this short guide [here](https://firebase.google.com/docs/cloud-messaging/android/client)

    **The sample app already has a fully working Firebase implementation.You can copy paste the code to avoid repeating the process. You only need to download your google-services.json file and put it in app/ folder.**

2. Get your server key from the Firebase Dashboard by going to Settings >> Cloud Messaging

3. Login into your backend server and open the `.env` file. Add this line to the file

    ``FIREBASE_SERVER_KEY=YOUR_SERVER_KEY``
4. On your android app, pass the Firebase token  to `STKPush.Builder` by calling this method

    ```builder.setFirebaseRegID(token);```

*Note: The firebase token will be appended to the callback url to get a url of the format `http://YOUR_URL/mpesa/token`*

## Going live
Although the SDK is still under development and might be unstable, it can be used in live apps.
The SDK  has tow modes `SANDBOX` and `PRODUCTION`.
To change to production mode, call this method:

```Mpesa.getInstance().setMode(Mode.PRODUCTION);```

```Mpesa.getInstance().setProductionBaseURL(url);```

**NOTE: When you set the mode to production, you have to provide the production base url.For sandbox , the base url is `https://sandbox.safaricom.co.ke/`, so the production base url can be of the form `https://wwww.safaricom.co.ke/`**

## Building the Sample Project
To build the sample project, clone the project and replace the credentials in  `MainActivity.java`. Hit RUN and you are set.

##Contributing and Issues
The project is really young and I would appreciate any pull requests or bug reports.Use [JIRA](https://issues.sonatype.org/browse/OSSRH-35483) for bug reports.



