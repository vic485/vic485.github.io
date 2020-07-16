---
layout: post
title: "Discord OAuth2 with Blazor"
tags: blog code
description: Setting up a small Blazor server application using Discord authentication.
---

## Authenticating users in Blazor with Discord OAuth2
Perhaps you want to build a dashboard for your discord bot, and need to access C# libraries for interacting with it. Perhaps you just like Blazor and want to add discord authentication to your website. Luckily for both scenarios, it's relatively easy and painless to get Discord OAuth2 working inside a Blazor server-side application. We only need two things to get us started:
- A Discord application to authenticate with
- A new or existing Blazor server website

### Setting up the Redirect Urls
By default, Blazor server runs on either `http://localhost:5000` or `https://localhost:5001`. We need to add one or both of these to the OAuth section of our application. The library we will use also needs "/signin-discord" at the end to function properly. Save changes so the text box is green, and we can begin working on the website.
![Redirect set](/images/discord-oauth-blazor/url-redirect.png)

### Building the Website
First you can remove the template provided pages from the project to clean up the file tree. The result should look similar to the project below. (Don't worry about the errors for now)
![Project with template pages removed](/images/discord-oauth-blazor/default-project-layout.png)

Next we'll add the configuration information to our application. Copy and paste both your discord app's "client id" and "client secret" into your `appsettings.json` file as a new object.
```json
  ...,
  "Discord": {
    "AppId": "id here",
    "AppSecret": "secret here"
  },
  ...
```

We'll need to bring in the Discord.Net OAuth 2 library next. Unfortunately, we can't use the nuget package as it hasn't been updated for a while and won't work with newer .net versions (at least not with .net 5 and .net core 3.1 in my testing). Download the code from [github](https://github.com/RogueException/Discord.OAuth2) and place the files into a subdirectory of your project, making the following changes to `DiscordHandler.cs`
```cs
// BEFORE
using Newtonsoft.Json.Linq;

/* Cut to save space */
var payload = JObject.Parse(await response.Content.ReadAsStringAsync());
var context = new OAuthCreatingTicketContext(new ClaimsPrincipal(identity), properties, Context, Scheme, Options, Backchannel, tokens, payload);

// AFTER
using System.Text.Json;

/* Cut to save space */
var payload = JsonDocument.Parse(await response.Content.ReadAsStringAsync());
var context = new OAuthCreatingTicketContext(new ClaimsPrincipal(identity), properties, Context, Scheme, Options, Backchannel, tokens, payload.RootElement);
```

To handle our user's login and logout, we'll create an `AccountController` under the Data folder. This will have two `HttpGet` methods to handle each function.
```cs
namespace DiscordAuthTest.Data
{
    [Route("[controller]/[action]")] // Microsoft.AspNetCore.Mvc.Route
    public class AccountController : ControllerBase
    {
        public IDataProtectionProvider Provider { get; }

        public AccountController(IDataProtectionProvider provider)
        {
            Provider = provider;
        }

        [HttpGet]
        public IActionResult Login(string returnUrl = "/")
        {
            return Challenge(new AuthenticationProperties {RedirectUri = returnUrl}, "Discord");
        }

        [HttpGet]
        public async Task<IActionResult> Logout(string returnUrl = "/")
        {
            //This removes the cookie assigned to the user login.
            await HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);
            return LocalRedirect(returnUrl);
        }
    }
}
```
We'll connect this in the `MainLayout.razor` file in our Shared folder. Add an `<AuthorizeView>` tag after the about link with a "log out" link in the `Authorized` section and a "log in" link in the `NotAuthorized`. I will also add a dummy link to display our name when we're logged in, which can point to some management page in the future.
```html
@inherits Microsoft.AspNetCore.Components.LayoutComponentBase

<div class="sidebar">
    <NavMenu/>
</div>

<div class="main">
    <div class="top-row px-4">
        <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
        <AuthorizeView>
            <Authorized>
                <a href="#">Hello, @context.User.Identity.Name!</a>
                <a href="Account/Logout">Log out</a>
            </Authorized>
            <NotAuthorized>
                <a href="Account/Login">Log in</a>
            </NotAuthorized>
        </AuthorizeView>
    </div>

    <div class="content px-4">
        @Body
    </div>
</div>
```

Next, we'll work on two pages under the Pages folder. For the `_Host.cshtml`, we need to remove the `<component>` tag within the `<app>` and replace it with `@(await Html.RenderComponentAsync<App>(RenderMode.Server))` which will simplify some of the initialization for us. You can also remove the `@{Layout = null;}` and "blazor-error-ui" div from here if you wish.

We'll also create a `RedirectToLogin.razor` page which simply forces navigation to our `Account/Login` link.
```cs
@using Microsoft.AspNetCore.Components
@inject NavigationManager Navigation

@code
{
    protected override void OnInitialized()
    {
        //This auto redirects a user to the login page
        Navigation.NavigateTo("Account/Login", true);
    }
}
```

Moving to our `App.razor`, change the `RouteView` to an `AuthorizeRouteView` and in the `NotAuthorized` tag put the `RedirectToLogin`. The `LayoutView` under the `NotFound` tag also needs to be put inside a `CascadingAuthenticationState`
```html
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <DiscordAuthTest.Pages.RedirectToLogin/>
            </NotAuthorized>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Finally, moving to the `Startup.cs`, replace the line that originally added the "WeatherForecastService" with the following authenticator.
```cs
services.AddAuthentication(opt =>
    {
        opt.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        opt.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        opt.DefaultChallengeScheme = DiscordDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddDiscord(x =>
    {
        x.AppId = Configuration["Discord:AppId"];
        x.AppSecret = Configuration["Discord:AppSecret"];

        x.SaveTokens = true;
    });
```

Move to the `Configure` method, and after `app.UseRouting();` add `app.UseAuthentication();`. Inside the `app.UseEndpoints` builder, add `endpoints.MapDefaultControllerRoute();`

With everything set, you should be able to run the server and log into your app with discord. This setup only does the "identify" scope by default, but additional scopes can be added, if needed, in the `AddDiscord` builder, for example: `x.AddScope("guilds");`.

![Unauthorized view](/images/discord-oauth-blazor/before-login.png)
![Logging in with OAuth](/images/discord-oauth-blazor/oauth-login.png)
![Authorized view](/images/discord-oauth-blazor/after-login.png)
