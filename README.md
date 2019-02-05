# Bitrise CI & CD

You only have to copy the `wrapper.yml` into the Bitrise YML editor located in the Workflow Editor. This will automatically
pull the latest `bitrise.yml` configuration from this repository.

## 1. Deployment Strategy

There are currently two workflows, **Testing** and **Release**.

### Testing

This workflow is triggered automatically after a push to the master branch of a repository. Various QA tools are run and build artifacts being uploaded to BrowserStack.

### Release

Automatically runs when a tag is created. This workflow will run QA tools again and upload the final app to Google Play and the App Store.

### Secrets

The following secrets need to be set for a project using the bitrise.yml in this repository.

```
NPM_TOKEN

TEAM_ID (Your Apple Developer Team ID)

BROWSERSTACK_USERNAME
BROWSERSTACK_ACCESS_KEY

SLACK_WEB_HOOK
SLACK_CHANNEL

# This is for building iOS apps. Project name can be found in app.json
BITRISE_PROJECT_PATH (For example: ios/han.xcodeproj)
BITRISE_SCHEME (For example: han)

# Package name Google Play
PACKAGE_NAME (For example: com.han)
```
## 2. Workflows

A Bitrise workflow is a collection of Steps. When a build of an app is run, the steps will be executed in the order that is defined in the workflow.
[Read More](https://devcenter.bitrise.io/getting-started/getting-started-workflows/)

You can specify as many workflows as you like, for instance, you could have a workflow for building and testing your app, and another workflow for uploading and publishing
your app in the Google Play or Apple Store.

### Steps

You can add any Step to your workflow - there are absolutely no restrictions. You can either create them yourself, or use pre-defined ones, which can be found [here](https://app.bitrise.io/integrations/steps).
Essentially, all a Step does is execute a script, such as sending messages to a Slack channel or building your app inside the Bitrise virtual machine.

### Triggers

You can set up triggers so that every time code is pushed to a specified branch of your repository or a tag or PR is created,
a build is automatically triggered on Bitrise. [Read More](https://devcenter.bitrise.io/builds/triggering-builds/triggering-builds/)

## 3. Building iOS Apps

### Bitrise Steps

We are using a pre-defined Bitrise step called [Xcode Archive & Export for iOS](https://www.bitrise.io/integrations/steps/xcode-archive) to automatically build our iOS app. 
Signing will also be done by [iOS Auto Provision and Developer Account](https://blog.bitrise.io/ios-auto-provision-step).

### Apple Developer Account

In order to build and sign an iOS application, you will need an [Apple Developer Account](https://developer.apple.com/account).

### Signing an iOS App

To sign your app on Bitrise, you will need the following things:

* A `.p12 Certificate` (One for Development, one for Distribution)

* An `iOS App Identifier`

Normally you also need a `Provisioning Profile`, however Bitrise will create and manage these automatically for you, by using
a pre-defined Bitrise Step called [iOS Auto Provision](https://app.bitrise.io/integrations/steps/ios-auto-provision). (You will also need to link your Apple Developer Account to your Bitrise profile)

More on Bitrise signing can be found [here](https://devcenter.bitrise.io/code-signing/ios-code-signing/code-signing/) and [here](https://devcenter.bitrise.io/code-signing/ios-code-signing/ios-code-signing-troubleshooting/).

#### Connect your Apple Developer Account

Go to [Bitrise Profile Page](https://app.bitrise.io/me/profile#/apple_developer_account) and add your account. This is required
for automatic iOS code signing by Bitrise.

More details at [iOS Auto Provision and Developer Account](https://blog.bitrise.io/ios-auto-provision-step)

#### .p12 Certificate

To obtain a .p12 Certificate you need to log into your [Apple Developer Account](https://developer.apple.com/account/) and
navigate to `Certificates, Identifiers & Profiles` on the left side.

You have to create two certificates, one for Development and one for Distribution (Production).

Click the + icon in the top right corner next to `iOS Certificates` and select `iOS App Development` under `Development`. Click continue and follow the steps
described. You will end up with a `.certSigningRequest` file, which you have to upload to your Apple Developer Account.

After the upload is complete, Appe will allow you to download the final certificate. You will now have a `.cert` file saved on disk.
Open (double click) it. If you are on a Mac, Keychain Access should open.

You will see a list with all your certificates. You should see an entry similar to:
`iPhone Developer: <YOUR NAMNE OR COMPANY NAME> (<LETTERS AND NUMBERS>)`. Right click and export it (Make sure to remember the password, you will need it later). 
This will provide you with a Personal Information Exchange file (.p12).

This time you have to repeat the steps above again, but this time select `App Store and Ad Hoc` under `Production` instead of iOS App Development
after clicking on the + icon.

You will have an entry similar to `iPhone Distribution: <YOUR NAMNE OR COMPANY NAME> (<LETTERS AND NUMBERS>)`. Follow the
steps above again to export it.

Please make sure to create a backup of all your certificates. (Preferably in LastPass)

#### Uploading .p12 certificates to Bitrise

Open the [Bitrise Dashboard](https://app.bitrise.io/dashboard/builds) in your browser to view all your apps. 
On the right side you should see a list of all your apps. Select the one you want to upload the certificates for. 

Click on `Workflow`, then `Code Signing`. Drag and drop both of your .p12 certificates onto the `CODE SIGNING IDENTITY`
field and enter the password you specified during the export from Keychain Access for each certificate.

After successfully uploading a certificate, you will see a label called `Team: <YOUR NAMNE OR COMPANY NAME> (<LETTERS AND NUMBERS>)`
Write down the TEAM ID (the numbers and letters in brackets after the name, for example 65XU8B9P1K), which you will need later on.

#### iOS App Identifier

Your app will need a registered `iOS App Identifier`. You can create one by logging into your [Apple Developer Account](https://developer.apple.com/account/) and
navigating to `Certificates, Identifiers & Profiles` on the left side. Click on `App IDs` which can be found under the `Identifiers` section.

Click on the + icon in the top right corner and follow the steps to create an identifier for your app.
(Note that the `App ID Suffix - Explicit App ID` needs to match the one specified in your xcode project)

## 4. Releasing iOS Apps

Publishing an App in the Android Store can be achieved using a Bitrise step called [Deploy To iTunes Connect](https://app.bitrise.io/integrations/steps/deploy-to-itunesconnect-deliver).

Additionally, you will need to configure your App before publishing it. For example, information such as in-app purchases, descriptions, screenshots etc, will be required.
For more information see [Publish iOS App](https://medium.com/@the_manifest/how-to-publish-your-app-on-apples-app-store-in-2018-f76f22a5c33a).

Once the initial setup is complete, Bitrise will automatically upload your latest build to the App Store.

(A manual step might be required to complete the final release)

## 5. Building Android Apps

### Bitrise Steps

We are using a pre-defined Bitrise step called [Android Build](https://app.bitrise.io/integrations/steps/android-build) to automatically build our Android app. 
Signing will be done by [Sign APK](https://www.bitrise.io/integrations/steps/sign-apk).

### Google Developer Account

In order to build and sign an Android application, you will need a [Google Developer Account](https://play.google.com/apps/publish/).

### Signing an Android App

To sign your app on Bitrise, you will need the following things:

* [Android Studio](https://developer.android.com/studio/)

* A manually generated `Keystore` file


More on Bitrise signing can be found [here](https://devcenter.bitrise.io/code-signing/android-code-signing/android-code-signing-using-bitrise-sign-apk-step/).

#### Generating a **keystore** using Android Studio

Open your project in Android Studio. From the menu select `Build` and then `Generate Signed Bundle / APK`. 

You will be prompted with a pop-up window. Choose APK (Android App Bundle might work as well but it has not been tested yet).
Click on `Next` and choose `Create new` below `Key store path`. Fill out all the fields, but make sure to remember the password,
as well as the alias you specify. (Those will be needed later on in Bitrise). Click `next` again and build your app.

A new keystore file will be created for you in the directory you choose during the build.

For more details please visit [Android App Signing](https://developer.android.com/studio/publish/app-signing#generate-key).

#### Uploading keystore to Bitrise

A detailed explanation on how to upload the keystore to Bitrise can be found [here](https://devcenter.bitrise.io/code-signing/android-code-signing/android-code-signing-using-bitrise-sign-apk-step/).

## 6. Releasing Android Apps

Publishing an App in the Android Store can be achieved using a Bitrise step called [Google Play Deploy](https://app.bitrise.io/integrations/steps/google-play-deploy).

Before you can automate the process, you need to create your App manually and provide details, such as descriptions, screenshots, country restrictions etc.
For a detailed explanation you can take a look at [here](https://developer.android.com/studio/publish/) and [here](https://blog.bitrise.io/how-to-deploy-android-app-to-google-play-store).

Once everything is configured, Bitrise should take care of the rest.

(An additional step to release an App might be required after it has been uploaded to the Google Play Store)

## 7. Configuring Environment Variables on Bitrise (Secrets)

### Input

You can specify [Step Inputs (Environment Variables)](https://devcenter.bitrise.io/bitrise-cli/step-inputs/) for every step in your workflow.
This is how we will configure the build of our iOS and Android apps.

Let's take `$TEAM_ID` as an example, which is your Apple Team ID.
You should have noted down your Team ID earlier (See: **Uploading .p12 certificates to Bitrise**).

Open your [Bitrise Dashboard](https://app.bitrise.io/dashboard/builds) again, select your app and click on `Workflow`, then on `Secrets`.
Click on `Add New` and create a secret called `TEAM_ID`, with the value of your Team ID and save the changes.

Afterwards, go back to `Workflow` and select the step `iOS Auto Provision`. You will see a list of `Input Variables`.
For `The Developer Portal team id` select your newly created secret (`$TEAM_ID`).

You need to do this for all of your workflows as they do not share the same secrrets. (You can switch between them in the top left corner)

### Ouput

Steps can also produce [Environment Variables](https://devcenter.bitrise.io/bitrise-cli/step-outputs/).
One practical example would be the output of a URL after uploading files to a third party service, such as BrowserStack.

This variable could be used, for example, in another step to send a notification in Slack that our app is ready to be tested,
with the link appended for easy accessibility.

## 8. Bitrise Artifacts

In our example `bitrise.yml`, we're using a pre-defined step called `Deploy to Bitrise.io - Apps, Logs, Artifacts`.
This step will automatically provide us with **.ipa** and **.apk** files if our build succeeds. Those artifacts will
be available as a download in the `APPS & ARTIFACTS` tab of the build. (Environment Variables are also produced during the build,
which allows us to use the artifacts in other steps later on.)

## 9. Testing with BrowserStack

When a build has succeeded, the resulting artifacts will automatically be uploaded
to BrowserStack.
