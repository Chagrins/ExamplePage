---
title: Sessions & Data Reporting
parent: Unity - PixoVR SDK
nav_order: 4
---

# Sessions and Data Reporting
{: .no_toc }

---

A session is one complete play-through of single scenario. Different modules handle this in different ways, but the main idea is that a session should be the scenario itself, not the process of selecting an experience.

Some modules have a lobby that a user loads into after authentication and return to after running through a scenario. Time in the lobby should not count towards any session- the session begins when you choose an experience from the lobby, and the session ends when you finish the experience, either by completing it, or using a menu option to return to the lobby.

Other modules just have a front end menu, but the concept is the same - the session begins once you have selected the experience from the menu.

## Session Flow

### Starting a Session

When a user enters a scenario, you'll want to start a session to start tracking the users actions.
The function to accomplish this is `ApexSystem.JoinSession()`.

`ApexSystem.JoinSession()` requires you at least pass in a `scenarioID`, which is a string that you as the developer will determine and can be whatever string you want.

`ApexSystem.JoinSession()` takes in the following parameters.
 - `string` : scenarioID - A developer determined identifier string.
 - `PixoVR.Apex.XAPI.Extension` : contextExtension - More information for the session that helps provide additional context.
 - `Action<HttpResponseMessage, JoinSessionResponse>` : success - A success callback function.
 - `Action<HttpResponseMessage, FailureResponse>` : failure - A failure callback function.

### During a Session

Now that the user is in a session, you can now report information about the users session back to the PixoVR Platform. `ApexSystem.SendSessionEvent()` and `ApexSystem.SendSimpleSessionEvent()` both accomplish this.

`ApexSystem.SendSimpleSessionEvent()` provides the simplest way to report information during a session. It simply takes a verb and a target object.

`ApexSystem.SendSimpleSessionEvent()` takes in the following parameters.
 - `string` : verbName
 - `string` : targetObject
 - `PixoVR.Apex.XAPI.Extension` : contextExtension - More information for the session that helps provide additional context.
 - `Action<HttpResponseMessage, object>` : success - A success callback function.
 - `Action<HttpResponseMessage, FailureResponse>` : failure - A failure callback function.

`ApexSystem.SendSessionEvent()` lets you create an entire XAPI `TinCan.Statement`, in full detail. There are requirements for what must be filled out in the XAPI statement.

In order to successfully call `ApexSystem.SendSessionEvent` will need the following information as apart of the `TinCan.Statement`
 - `statement.actor`
 - `statement.verb`
 - `statement.verb.id`
 - `statement.target`

`ApexSystem.SendSessionEvent()` takes in the following parameters.
 - `TinCan.Statement` : eventStatement - An XAPI statement object containing all information for this session event.
 - `Action<HttpResponseMessage, object>` : success - A success callback function.
 - `Action<HttpResponseMessage, FailureResponse>` : failure - A failure callback function.

### Ending a Session

Once the user has completed the scenario or training, you'll want to report this completion to the PixoVR Platform with `ApexSystem.CompleteSession()`.
Completing a session requires that you report information like a users score, how long they took to complete the scenario and if they completed and passed the scenario, all of this is apart of `PixoVR.Apex.SessionData`.

`ApexSystem.CompleteSession()` takes in the following parameters.
 - `PixoVR.Apex.SessionData` : currentSessionData - The users score, pass/fail, if it was completed and the amount of time to reach the end of the scenario.
 - `PixoVR.Apex.XAPI.Extension` : contextExtension - More information for the session that helps provide additional context.
 - `PixoVR.Apex.XAPI.Extension` : resultExtension - More information for the sessions results that helps provide additional context about the scoring.
 - `Action<HttpResponseMessage, object>` : success - A success callback function.
 - `Action<HttpResponseMessage, FailureResponse>` : failure - A failure callback function.

## Examples

### Calling ApexSystem.JoinSession()

```
var contextExtension = new PixoVR.Apex.XAPI.Extension();
contextExtension.AddSimple("Session Start Time", $"{DateTime.UtcNow}");
contextExtension.AddSimple("UserName", $"Hello World");
contextExtension.AddSimple("Mode", $"Mode");
ApexSystem.JoinSession("Demo", contextExtension, JoinSessionSuccess, JoinSessionFailed);
```

### Calling ApexSystem.CompleteSession()

```
float raw = (float)System.Convert.ToDouble(RawScoreInput.text);
float scaled = (float)System.Convert.ToDouble(ScaledScoreInput.text);
float min = (float)System.Convert.ToDouble(MinScoreInput.text);
float max = (float)System.Convert.ToDouble(MaxScoreInput.text);
int duration = System.Convert.ToInt32(DurationInput.text);
bool moduleComplete = true;
bool userPassed = scaled > min;
ApexSystem.CompleteSession(new SessionData(raw, scaled, min, max, duration, moduleComplete, userPassed), null, null, CompleteSessionSuccess, CompleteSessionFailed);
```

### Calling ApexSystem.SendSimpleSessionEvent()

```
ApexSystem.SendSimpleSessionEvent("Sending an event", "User", null, SessionEventSuccess, SessionEventFailed);
```