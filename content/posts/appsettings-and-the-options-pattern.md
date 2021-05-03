---
title: appsettings.json and the Options Pattern
date: 2021-04-30
draft: false;
---
All right! Time to get busy using the Options Pattern in ASP.NET Core. As I said in an earlier post, there are a variety of ways to get information from the appsettings.json file. Let’s get another one under our belt, shall we? First, we'll add some information to the appsettings.json file.

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
  "ScrapeDataSettings": {
    "TheMorningDew": {
      "Url": "https://www.alvinashcraft.io/",
      "TargetNode": "//div[@class='main-content']/ul[1]/li/a[@href]",
      "AttributeName": "href"
    }
  }
}
```

Under the TheMorningDew section we got three keys: “Url”, “TargetNode” and “AttributeName”. We’re going to create a new class, an options class, using these keys as properties and then bind the values to these properties. First things first, let’s create the class.

```csharp
namespace WebApplication
{
  public class ScrapeDataSettings
  {
    public string Url { get; set; }
    public string TargetNode { get; set; }
    public string AttributeName { get; set; }
  }
}
```

Now we’ll have to do some coding in our ASP.NET Core startup class, under ConfigureServices we’ll “Register a configuration instance which TOptions will bind against”. Basically, we are telling ASP what class we are binding the values to and in what section in appsettings.json it will find the key/values.

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.AddControllersWithViews();
  services.Configure<ScrapeDataSettings>(Configuration.GetSection("ScrapeDataSettings:TheMorningDew"));
}
```

OK, so we’ve told ASP where to get the values and what class to bind them to. Due to the awesomeness of ASP.NET Core we’ve now added the values to the dependency injection service container which means we can access the values via our controller constructor and assign them like this.

```csharp
public class HomeController : Controller
{
  private readonly ScrapeDataSettings scrapeDataSettings;

  public HomeController(IOptions<ScrapeDataSettings> options)
  {
    scrapeDataSettings = options.Value;
  }

  public IActionResult Index()
  {
    var url = scrapeDataSettings.Url;
    var targetNode = scrapeDataSettings.TargetNode;
    var attributeName = scrapeDataSettings.AttributeName;

    return View();
  }
}
```

Case closed, everything's great, let’s go home… or not.
Let’s say we have multiple sections, with similar keys, that we want to be able to access from our Controller. Instead of creating and binding to multiple classes, we can use “Named Options”. This means we bind the different sections to the same options class but give each section a name identifier. First, we will have to modify ConfigureServices.

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.AddControllersWithViews();

  services.Configure<ScrapeDataSettings>("TheMorningDew", 
    Configuration.GetSection("ScrapeDataSettings:TheMorningDew"));
            
  services.Configure<ScrapeDataSettings>("TheEveningBrew", 
    Configuration.GetSection("ScrapeDataSettings:TheEveningBrew"));
}
```

Before we can access the dependency injected container we have to change the parameter type from IOption to IOptionsSnapshot and assign the values to separate properties.

```csharp
public class HomeController : Controller
{
  private readonly ScrapeDataSettings theMorningDewSettings;
  private readonly ScrapeDataSettings theEveningBrewSettings;

  public HomeController(IOptionsSnapshot<ScrapeDataSettings> options)
  {
    theMorningDewSettings = options.Get("TheMorningDew");
    theEveningBrewSettings = options.Get("TheEveningBrew");
  }

  public IActionResult Index()
  {
    var morningUrl = theMorningDewSettings.Url;
    var morningTargetNode = theMorningDewSettings.TargetNode;
    var morningAttributeName = theMorningDewSettings.AttributeName;
    var eveningUrl = theEveningBrewSettings.Url;
    var eveningTargetNode = theEveningBrewSettings.TargetNode;
    var eveningAttributeName = theEveningBrewSettings.AttributeName;

    return View();
  }
}
```

We can now fetch the values from the sections. 
There is so much more stuff to do with the options pattern, one of my favourits are validations. More on that in another post.