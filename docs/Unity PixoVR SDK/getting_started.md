---
title: Getting Started
parent: Unity - PixoVR SDK
nav_order: 2
---

# Getting Started
{: .no_toc }

---

## Understanding xAPI and TinCan.NET

Data sent to the PixoVR Platform is formatted using the xAPI Standard. The Unity PixoVR SDK Plugin utilizes TinCan.NET to ensure all data is xAPI Compliant.

To understand how TinCan.NET is used, visit the documentation here. To get a better understanding of the xAPI Standard, visit the xAPI Spec here.

## Requirements for building with the Apex Plugin

The projects API Compatbility Level needs to be .NET 4.x or newer.

## Integrating with ManageXR

The PixoVR Platform provides a way to integrate with the ManageXR platform. Follow the steps below to integrate with ManageXR.

Go to your project manifest json file located at `Packages/manifest.json`. Add this line to your dependency list

`"com.mxr.unity.sdk":"https://www.github.com/ManageXR/mxr-unity-sdk.git#VERSION"`
    VERSION is the SDK version you want to install
    For example, if you want to install version v1.0.15, the line should be "com.mxr.unity.sdk":"https://www.github.com/ManageXR/mxr-unity-sdk.git#v1.0.15"

Next, open your Unity project and go to `Edit > Project Settings > Player`.

Select the Android platform and go to `Other Settings > Script Compliation`.

Add `MANAGE_XR` to the Scripting Define Symbols and press Apply. This is case sensitive.

Make sure you do not call `await MXRManager.InitAsync();` in any of your scripts. This is called by the Unity PixoVR SDK.

## Installing the Unity Package

To install the Unity PixoVR SDK, open Window > Package Manager.
Click the '+' in the top left corner and select 'Add package from disk...'
Navigate to where you've extracted the downloaded zip file and select package.json.
Now you should be able to see the package in the Package Manager, where you can install it into your project.

You can also manually install the package by copying the `com.pixovr.apexunitysdk` folder into your projects `Packages` folder. Unity will automatically detect the change and add the package once assets are reimported.

{: .warning }
> Older releases of the Unity package may contain a `documentation` folder, which has been known to cause errors when you open your project. Feel free to delete this folder.

## Setup steps after installing the package

If you're building for Android or any Android XR headsets, go to `PixoVR > Setup`.

## Using the Unity PixoVR SDK Plugin

### Per Scenario Settings

After installing the Unity PixoVR SDK, navigate to `Runtime > Unity`. Add the `ApexSystem` script to any object in the scene. Any object that the `ApexSystem` script is added to will **NOT** be deleted between level changes.

The following properties will need to be set on the `ApexSystem` component once it's added to the scene.

`Platform Target Server`
    This fills in the server IP address that you want to send the information to.
    Defaults to the `North America - Production envrionment`.
`Module ID`
    This is the ID of the module that will be distributed to customers. This will be generated when you create a project on the Apex platform. This must be set for the data to be reported to the proper module.
`Module Name`
    This is the name of your module.
`Scenario ID`
    The name of the current scenario within your module that the user is taking part in. You can have multiple scenarios within a module and is completely up to you with how you use this value.

### Project Settings

There are a few more settings available in `Project Settings > PixoVR`. Some of these will probably be modified more frequently and are used in a more global sense of your project.

`Module Version`
    The version number for your module currently being distributed.
    The Module Version is based on Semantic numbering with a Major, Minor and Patch number. Both Minor and Patch numbers are always 2 digits, using a 0 as the first value when the number is below 10. (e.g.) 1.03.08. This is enforced on the platform itself as well.
`Custom URL Scheme`
    This should be ignored.
`Sync Versions on Build Pre Process`
    This will synchronize the projects `Version` with the `Module Version`. This just eliminates the need to make multiple changes in your settings if you want versioning to align between your project and module version.