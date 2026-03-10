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

### Custom Login Authentication

If you are expecting to handle the login when the user launching the application in a standalone scenario, the Unity PixoVR SDK also provide you a way to accomplish that.

To authenticate, you can either bundle a username and password into a `LoginData` object or pass a username and password to `ApexSystem.Login()`. You can also bundle a One-Time Code, generated on the PixoVR Platform, as the username and call `ApexSystem.Login()` with an empty password string.

## Authorization

By default, authorization is handled automatically after authentication occurs.
In the scenario you want to handle authorization by itself, you will make a call to `ApexSystem.CheckModuleAccess()` and pass your `Module ID`.

The `ApexSystem.OnModuleAccessSuccess` event will be invoked if the user has access to the module.
`ApexSystem.OnModuleAccessFailed` event will be invoked if the user does not have access to the module.

These events will be what you want to listen to before you proceed to creating a `PixoVR Platform Session` as without authorization, the user cannot make calls to the session.

## Examples

### Authenticated Launch and Manual Login Example

```
void Start()
{
  if (!string.IsNullOrEmpty(ApexSystem.PassedLoginToken))
  {
      ApexSystem.LoginWithToken();
  }

  ApexSystem.Instance.OnLoginSuccess.AddListener(OnLoginSuccess);
  ApexSystem.Instance.OnLoginFailed.AddListener(OnLoginFailed);
  ApexSystem.Instance.OnModuleAccessSuccess.AddListener(OnModuleAccessCheckSuccess);
  ApexSystem.Instance.OnModuleAccessFailed.AddListener(OnModuleAccessCheckFailed);
}

// This will be bound to your login button to occur when it is clicked. Handle any extra case data here as well.
public void OnLoginButtonClicked()
{
    string username = usernameField.text;
    string password = passwordField.text;
    ApexSystem.Login(username, password);
}
 
void OnLoginFailed(FailureResponse response)
{
  // Handle failed login here...
}
 
void OnLoginSuccess()
{
  // Handle successful login here...
}

void OnModuleAccessCheckFailed(FailureResponse response)
{
  // Handle failed module access check here...
}

void OnModuleAccessCheckSuccess(LoginResponseContent loginResponse)
{
  // Handle successful module access check here...
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

  ApexSystem.Instance.OnLoginSuccess.AddListener(OnLoginSuccess);
  ApexSystem.Instance.OnLoginFailed.AddListener(OnLoginFailed);
  ApexSystem.Instance.OnModuleAccessSuccess.AddListener(OnModuleAccessCheckSuccess);
  ApexSystem.Instance.OnModuleAccessFailed.AddListener(OnModuleAccessCheckFailed);
}

// This will be bound to your login button to occur when it is clicked. Handle any extra case data here as well.
public void OnLoginButtonClicked()
{
    string username = usernameField.text;
    string password = passwordField.text;
    ApexSystem.Login(username, password);
}
 
void OnLoginFailed(FailureResponse response)
{
  // Handle failed login here...
}
 
void OnLoginSuccess()
{
  // Handle successful login here...
  ApexSystem.CheckModuleAccess(ApexSystem.ModuleID);
}

void OnModuleAccessCheckFailed(FailureResponse response)
{
  // Handle failed module access check here...
}

void OnModuleAccessCheckSuccess(LoginResponseContent loginResponse)
{
  // Handle successful module access check here...
}
```