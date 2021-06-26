---
title: Reading values from appsettings.json
date: 2021-04-20
draft: false
---
Let’s see how we fetch data from the appsettings.json file to the controller. First off we got to add some worthwhile data to the appsettings.json file, what is more worthwhile than the meaning of life?

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  "Settings": {
    "Home": {
      "MeaningOfLife": 42
    }
  }
}
```

Ok, so how do we access this data from our Home Controller? Via the awesomeness of dependency injection of course! In this case there is no need to configure the dependency injection in the Startup class. ASP will automatically add the IConfiguration instance to the dependency injection container. With the IConfiguration instance we have full access to all the information in the appsettings.json file. All we have to do now is to add IConfiguration to the constructor in our controller like this:

```csharp
namespace WebApplication.Controllers
{
  public class HomeController : Controller
  {
    private readonly IConfiguration configuration;

    public HomeController(IConfiguration configuration)
    {
      this.configuration = configuration;
    }

    public IActionResult Index()
    {
      int meaningOfLife = configuration.GetValue<int>("Settings:Home:MeaningOfLife");

      return View();
    }
  }
}
```

As you can see we can use the GetValue method to get the appsettings.json data from the IConfiguration instance. All we have to do is to specify the return type and the path to the key. Got a lot of settings? Maybe you got deeply nested sections in appsettings.json? No problem people, the GetSection comes to the rescue:

```csharp
public IActionResult Index()
{
  var homeSettings = configuration.GetSection("Settings:Home");
  var meaningOfLife = homeSettings.GetValue<int>("MeaningOfLife");

  return View();
}
```

First we’ll save the target section in a variable and then we use the GetValue method on that variable.

OK so that is it, there are a lot of ways to access the appsettings.json file and the ones in this post are the most basic ones. Another great way is to bind the sections to an object or using the options pattern.

Go configure! 
