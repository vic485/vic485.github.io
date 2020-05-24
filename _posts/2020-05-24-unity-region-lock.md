---
layout: post
title: "Region Lock for Unity Games"
tags: blog code
description: Simple system to keep games from running outside their intended region.
---

## Region Lock for Unity Games (or other C# scripting engines)
When publishing a game, it sometimes becomes necessary to restrict where the game can be bought and played. Whether it be due to regional restrictions on content, or to prevent cheats in on-line games. The system here will be more focused towards the former of the two scenarios, as it is designed to prevent the game from running if the user is outside our acceptable areas. I will be using Unity to show this system, but this can be easily adapted to pretty much any engine as long as you can access the `System.TimeZoneInfo` class in C# or something similar in another language.

![Unity editor](/images/unity-region-lock/editor_setup.png)
Here I have setup a simple scene. This will be the very first scene loaded by our game and just contains the region lock script, and a message that will show if the user is in the wrong region. There's also a button to quit the game if needed.

Create a new C# script to act as our region lock, and inside create a list to be serialized in the inspector.
```cs
public List<string> allowedTimeZones = new List<string>();
```
In the Unity inspector we can set as many time zones as we want to allow by putting in their ids
![Time zone list](/images/unity-region-lock/timezone_list.png)
The following snippet of code can be used to find applicable ids.
```cs
foreach (var timeZoneInfo in TimeZoneInfo.GetSystemTimeZones())
{
    // use "print()" inside Unity instead of "Console.WriteLine()"
    Console.WriteLine(timeZoneInfo.Id);
}
```

Now onto the actual logic of the region lock. We'll store our system's current time zone in a variable, and check if it matches any of the time zones from our allowed list. If one matches we will move to immediately load the next scene in the game allowing us to play. We put this in the `Awake` method so that it runs as the object initializes and cuts down wait time.
```cs
private void Awake()
{
    var tz = TimeZoneInfo.Local;

    if (allowedTimeZones.Any(x => TimeZoneInfo.FindSystemTimeZoneById(x).Equals(tz)))
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex + 1)
}
```
A variation on this, if we only wanted to block certain regions, would be to make the list a block list and return instead of loading the next scene if we have a match, or inverting the if statement.

Finally our full script should look something like this
```cs
using System;
using System.Collections.Generic;
using System.Linq;
using UnityEngine;
using UnityEngine.SceneManagement;

public class RegionLock : MonoBehaviour
{
    public List<string> allowedTimeZones = new List<string>();

    private void Awake()
    {
        var tz = TimeZoneInfo.Local;

        if (allowedTimeZones.Any(x => TimeZoneInfo.FindSystemTimeZoneById(x).Equals(tz)))
            SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex + 1);
    }

    // Called by the quit button in our scene
    public void QuitButton()
    {
        Application.Quit(0);
    }
}
```

We now have a system to keep our game from running outside of the regions we want it to be played in. However there are some holes in it still. Since we only check the local time zone of the system, if a user changes this on their computer it will pass. This may or may not be acceptable depending on your situation. If not it would be good to setup or look for an on-line api to get the requester's region and check against that, but I will leave that for a future post.
