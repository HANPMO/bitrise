# Bitrise CI & CD

## 1. Workflows

A Bitrise workflow is a collection of Steps. When a build of an app is run, the steps will be executed in the order that is defined in the workflow.
[Read More](https://devcenter.bitrise.io/getting-started/getting-started-workflows/)

You can specify as many workflows as you like, for instance, you could have a workflow for building and testing your app, and another workflow for uploading and publishing
your app in the Google Play or Apple Store.

The example `bitrise.yml` in this repository defines two workflows, **Release** and **Testing**.

### Steps

You can add any Step to your workflow - there are absolutely no restrictions. You can either create them yourself, or use pre-defined ones, which can be found [here](https://app.bitrise.io/integrations/steps).
Essentially, all a Step does is execute a script, such as sending messages to a Slack channel or building your app inside the Bitrise virtual machine.

### Triggers

You can set up triggers so that every time code is pushed to a specified branch of your repository or a tag or PR is created,
a build is automatically triggered on Bitrise. [Read More](https://devcenter.bitrise.io/builds/triggering-builds/triggering-builds/)

For our example there are two triggers specified:
   
   * When changes are pushed to master, the **Testing** workflow will be executed.
   * If a Tag is created, the **Release** workflow is triggered.


## 2. Building iOS Apps

### Apple Developer Account

In order to build and sign an iOS application, you will need a [Developer Account](https://developer.apple.com/account).

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

##### Uploading .p12 certificates to Bitrise

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

## 3. Building Android Apps

WIP

### Signing an Android App

WIP


## 4. Configuring Environmental Variables on Bitrise (Secrets)

You can specify [Step Inputs (Environmental Variables)](https://devcenter.bitrise.io/bitrise-cli/step-inputs/) for every step in your workflow.
This is how we will configure the build of our iOS and Android apps.

The example bitrise.yml file in this repository only needs one variable to be present, `$TEAM_ID`, which is your Apple Team ID.
You should have noted down your Team ID earlier (See: **Uploading .p12 certificates to Bitrise**).

Open your [Bitrise Dashboard](https://app.bitrise.io/dashboard/builds) again, select your app and click on `Workflow`, then on `Secrets`.
Click on `Add New` and create a secret called `TEAM_ID`, with the value of your Team ID and save the changes.

Afterwards, go back to `Workflow` and select the step `iOS Auto Provision`. You will see a list of `Input Variables`.
For `The Developer Portal team id` select your newly created secret (`$TEAM_ID`).

You need to do this for all of your workflows as they do not share the same secrrets. (You can switch between them in the top left corner)
