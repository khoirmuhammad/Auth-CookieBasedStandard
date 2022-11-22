# Cookie Based Authentication
Here we will explain about standard authentication using cookie. This project is implemented in ASP NET Core Web API. There are many kind of authorization. In this repo, just showing a simple authentication. Analogy : We only need to get authorization to enter building and we able to access all resources in all floors and even each room, without limitation

1. Once we have created a solution / project, open startup / program class (based on service configuration is placed)

```
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie(........)
```
Don't forget using app.Authentication() middleware

2. Create AuthController. Within controller please provide login & logout action method

### Login
This piece of code will create claims data
```
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, username),
    new Claim("CustomClaim", "custom claim")
};

var claimsIdentity = new ClaimsIdentity(
    claims, CookieAuthenticationDefaults.AuthenticationScheme);
```

This piece of code will sigin in to application
- IsPersistent closely related to ExpiresUtc. 
- If we ever see about "remember me" checkbox. It relates to IsPersistent value
- If IsPersistent set to TRUE, then ExpiresUtc will be activated / used. If FALSE then ExpiresUtc won't be considered
- IsPersistent = TRUE -> Cookie will be removed after expires is happenned
```
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = false,
        ExpiresUtc = DateTimeOffset.UtcNow.AddMinutes(1)
    });
 ```
 
 equivalent with (Setting in service configuration startup / program class)
 ```
 options.Events.OnRedirectToLogin = (context) =>
{
    context.Response.StatusCode = 401;
    context.Properties.ExpiresUtc= DateTime.UtcNow.AddMinutes(1);
    context.Properties.IsPersistent = true; 
    return Task.CompletedTask;
};
 ```
