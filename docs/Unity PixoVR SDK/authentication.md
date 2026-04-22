---
title: Authentication
parent: Unity - PixoVR SDK
nav_order: 3
---

# Authentication and Authorization
{: .no_toc }

---

When you authenticate against the platform, you'll also need to authorize that the user has access to the content within this module.
By default, both of these things are accomplished once authentication occurs.

## Authentication

Most modules that are connected to our platform are launched through the Hub App. Each module launched through the Hub App should support Authenticated Launching.
Fortunately, the Unity PixoVR SDK provides a single call to make to handle this and a variable to check to ensure that you are needing to make this authenticated launch call.

Checking `ApexSystem.PassedLoginToken` should provide a non-null or empty string value if it is an Authenticated Launch.
Once you've verified that the PassedLoginToken is not invalid, you will need to call `ApexSystem.LoginWithToken()`.

There will be an example at the bottom in the `Example` section that shows you how to use handle an Authenticate Launch.

`ApexSystem.LoginWithToken()` takes in the following parameters.
 - `Action<HttpResponseMessage, ActiveUserInformation>` : success - A success callback function.
 - `Action<HttpResponseMessage, FailureResponse>` : failure - A failure callback function.

### Custom Login Authentication

If you are expecting to handle the login when the user launching the application in a standalone scenario, the Unity PixoVR SDK also provide you a way to accomplish that.

To authenticate, you can either bundle a username and password into a `LoginData` object or pass a username and password to `ApexSystem.Login()`. You can also bundle a One-Time Code, generated on the PixoVR Platform, as the username and call `ApexSystem.Login()` with an empty password string.
By default, authorization is also handled and will call success or failure after the authorization is called.

`ApexSystem.Login()` takes in the following parameters.
 - `string` : username - A users username or their one-time code.
 - `string` : password - A users password if there is a username. This can be empty or null if using a one-time code.
 - `Action<HttpResponseMessage, ActiveUserInformation>` : success - A success callback function.
 - `Action<HttpResponseMessage, FailureResponse>` : failure - A failure callback function.

The `ActiveUserInformation.ModuleUserInformation` will be null if authorization was **not** handled during Login.

## Authorization

By default, authorization is handled automatically after authentication occurs.
In the scenario you want to handle authorization by itself, you will make a call to `ApexSystem.CheckModuleAccess()` and pass your `Module ID`.

`ApexSystem.CheckModuleAccess()` takes in the following parameters.
 - `int` : targetModuleID - The module id that was assigned to the module when it was created on the PixoVR Platform.
 - `Action<HttpResponseMessage, ActiveUserInformation>` : success - A success callback function.
 - `Action<HttpResponseMessage, FailureResponse>` : failure - A failure callback function.

The `success` function will be invoked if the user has access to the module.
The `failure` function will be invoked if the user does not have access to the module.

## Examples

### Authenticated Launch and Manual Login Example

```
void Start()
{
  if (!string.IsNullOrEmpty(ApexSystem.PassedLoginToken))
  {
      ApexSystem.LoginWithToken(OnLoginSuccess, OnLoginFailed);
  }
}

// This will be bound to your login button to occur when it is clicked. Handle any extra case data here as well.
public void OnLoginButtonClicked()
{
    string username = usernameField.text;
    string password = passwordField.text;
    ApexSystem.Login(username, password, OnLoginSuccess, OnLoginFailed);
}
 
void OnLoginFailed(HttpResponseMessage msg, FailureResponse data)
{
  // Handle failed login here...
}
 
void OnLoginSuccess(HttpResponseMessage msg, ActiveUserInformation data)
{
  // Handle successful login here...
}
```

### Manual Authorization Example

```
void Start()
{
  if (!string.IsNullOrEmpty(ApexSystem.PassedLoginToken))
  {
      ApexSystem.LoginWithToken();
  }
}

// This will be bound to your login button to occur when it is clicked. Handle any extra case data here as well.
public void OnLoginButtonClicked()
{
    string username = usernameField.text;
    string password = passwordField.text;
    ApexSystem.Login(username, password);
}
 
void OnLoginFailed(HttpResponseMessage msg, FailureResponse data)
{
  // Handle failed login here...
}
 
void OnLoginSuccess(HttpResponseMessage msg, ActiveUserInformation data)
{
  if(data.ModuleUserInformation == null)
  {
    ApexSystem.CheckModuleAccess(ApexSystem.ModuleID, OnLoginSuccess, OnLoginFailed);
  }
}
```