## Start the Authentication

The [Auth0 hosted login page](/hosted-pages/login) is the easiest way to set up authentication in your application. We recommend using the Auth0 hosted login page for the best experience, best security and the fullest array of features.

::: note
You can also embed the Lock widget directly in your application. If you use this method, some features, such as single sign-on, will not be accessible. 
To learn how to embed the Lock widget in your application, follow the [Embedded Login sample](https://github.com/auth0-samples/auth0-android-sample/tree/embedded-login/01-Embedded-Login).
:::

::: note
Read the [Browser-Based vs. Native Login Flows on Mobile Devices](/tutorials/browser-based-vs-native-experience-on-mobile) article to learn how to choose between the two types of login flows.
:::

In the `login` method, create a new instance of the `Auth0` class to hold user credentials. 

You can use a constructor that receives an Android Context if you have added the following String resources: 
* `R.string.com_auth0_client_id`
* `R.string.com_auth0_domain`

If you prefer to hardcode the resources, use the constructor that receives both strings. Then, use the `WebAuthProvider` class to authenticate with any connection you enabled on your client in the [Auth0 dashboard](${manage_url}/#/).

You need to make sure you get a response compliant with the OpenID Connect protocol. You can choose between two options:

* Set the audience
* Turn on the **OIDC conformant** switch in your Auth0 dashboard
  		  
::: note
To turn on the **OIDC conformant** switch, in your [Client Settings](${manage_url}/#/applications/${account.clientId}/settings), click on **Show Advanced Settings** > **OAuth**.
:::

After you call the `WebAuthProvider#start` function, the browser launches and shows the **Lock** widget. Once the user authenticates, the callback URL is called. The callback URL contains the final result of the authentication process. 

```java
// app/src/main/java/com/auth0/samples/MainActivity.java

private void login() {
    Auth0 auth0 = new Auth0(this);
    auth0.setOIDCConformant(true);
    WebAuthProvider.init(auth0)
                  .withScheme("demo")
                  .withAudience(String.format("https://%s/userinfo", getString(R.string.com_auth0_domain)))
                  .start(MainActivity.this, new AuthCallback() {
                      @Override
                      public void onFailure(@NonNull Dialog dialog) {
                        // Show error Dialog to user
                      }

                      @Override
                      public void onFailure(AuthenticationException exception) {
                        // Show error to user
                      }

                      @Override
                      public void onSuccess(@NonNull Credentials credentials) {
                          // Store credentials
                          // Navigate to your main activity
                      }
                });
}
```

## Capture the Result

Whitelist the callback URL for your app in the **Allowed Callback URLs** section in [Client settings](${manage_url}/#/clients). In that section, enter the following URL: 

```text
demo://${account.namespace}/android/YOUR_APP_PACKAGE_NAME/callback
```

::: note
Replace `YOUR_APP_PACKAGE_NAME` with your application's package name, available in the `app/build.gradle` file as the `applicationId` attribute.
:::

After authentication, the browser redirects the user to your application with the authentication result. The SDK captures the result and parses it. 

::: note
You do not need to declare a specific `intent-filter` for your activity, because you have defined the manifest placeholders with your Auth0 **Domain** and **Scheme** values.
:::

The `AndroidManifest.xml` file should look like this:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.auth0.samples">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">

        <activity android:name="com.auth0.samples.MainActivity">
          <intent-filter>
              <action android:name="android.intent.action.MAIN" />
              <category android:name="android.intent.category.LAUNCHER" />
          </intent-filter>
        </activity>

    </application>

</manifest>
```

There are many options to customize the authentication with the `WebAuthProvider` builder. You can read about them in the [Auth0 SDK for Android documentation](/libraries/auth0-android).
<div class="phone-mockup">
  <img src="/media/articles/native-platforms/android/login-android.png" alt="Mobile example screenshot" />
</div>
