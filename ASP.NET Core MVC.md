These notes have been prepared on my own based on [this course on LinkedIn Learning](https://www.linkedin.com/learning/learning-asp-dot-net-core-mvc).

# Creating a new project
* These notes assume this has been done by choosing the Empty Project template of an ASP.NET Core Web Application project.

# Responding to HTTP Requests
* The `Startup.cs` file already has a basic Hello World application, which looks like this:
```cs
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Hello, World!");
});
```

* You can add middleware by using `app.Use`, which takes a function that accepts two parameters, `context` and `next`.

# Serving Static Files
* Static files can be served by using `app.UseFileServer`, which is part of the `StaticFiles` project. 
* The files to be served statically must be put under the `wwwroot` directory.

# Error Handling and Diagnostics
* Production and dev errors can be handled separately, as shown. This is put in the `Configure` method of `Startup.cs`.
```cs
app.UseExceptionHandler("/error.html");
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}
```
* The developer page takes priority over the production one, even though it's declared first.
* To view the production error page, go to Project Properties - Debug, and change the value of `ASPNETCORE_ENVIRONMENT`.

# Custom configuration
* We can use the configuration library to set up custom configuration. The package that is required is a dependency of the logging package that is added by default, so it is installed too.
* We first create an instance of the `ConfigurationBuilder` object. This is part of the `Microsoft.Extensions.Configuration` namespace. We can then define where the application settings will be stored, by using the `AddEnvironmentVariables` method. Then, we call the `Build` method, which builds the configuration object so we get access to the configuration sources.
* We can get the configuration by a dictionary-style syntax.
```cs
var configuration = new ConfigurationBuilder()
                        .AddEnvironmentVariables()
                        .Build();
if (configuration["IsDevEnv"] == "True")
{
    app.UseDeveloperExceptionPage();
}
```
* This code is ugly and we’d like to automatically convert the value to a Boolean. This isn’t available built-in, but is possible through an extension.
```cs
if (configuration.GetValue<bool>("IsDevEnv"))
{
    app.UseDeveloperExceptionPage();
}
```
* To add configuration settings from a JSON file, we simply add a call to `AddJsonFile` method, and specify the path as an argument. Complex JSON objects can also be parsed, but a colon, rather than a period, is used to read nested properties.
```cs
var configuration = new ConfigurationBuilder()
                        .AddEnvironmentVariables()
                        .AddJsonFile(env.ContentRootPath + "/config.json")
                        .Build();
if (configuration.GetValue<bool>("FeatureToggles:EnableDeveloperExceptions"))
{
    app.UseDeveloperExceptionPage();
}
```
* **What if same variable from 2 sources?**

# Using Dependency Injection
* Rather than use the files as configuration sources, we could use another class, and not care about where the configurations came from. To do this, we add a class, called `FeatureToggles`:
```cs
public class FeatureToggles
{
    public bool EnableDeveloperExceptions { get; set; }
}
```
and then modify the `Configure` method as:
```cs
public void Configure(IApplicationBuilder app, 
    IHostingEnvironment env,
    FeatureToggles features)
{
    var configuration = new ConfigurationBuilder()
                            .AddEnvironmentVariables()
                            .AddJsonFile(env.ContentRootPath + "/config.json")
                            .Build();
    if (features.EnableDeveloperExceptions)
    {
        app.UseDeveloperExceptionPage();
    }
}
```
* To let VS know how to use this `FeatureToggles` class, we must edit the `ConfigureServices` method. There are 3 important methods in the services variable, which is an object of `IServiceCollection`. The main difference lies in how long the service lives.
    * The `AddTransient` method has the shortest (transient) lifespan. A new instance is created every time one is requested.
    * If we add a type using the `AddScoped` method, a single instance will be created for each "scope", which is almost always the current web request. This allows you to share data between different components for the same request, without worrying about other users' requests gaining access to that data.
    * To share data between requests, the `AddSingleton` method can be used. This only creates one instance of each type for the entire lifetime of the application.
```cs
 public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<FeatureToggles>();
}
```
* ASP.NET Core allows you to control how objects are created here, by allowing you to pass a function to the `AddTransient` (and other) methods that returns an instance of the type.
```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<FeatureToggles>(x => new FeatureToggles());
}
```
* The above is what ASP.NET Core does by default. Alternatively, we can populate its properties from configuration settings. For that, we move the configuration object to a property and populate it in the startup constructor as opposed to the `Configure` method. Since we use the `env` variable, we have to bring along a reference to the `IHostingEnvironment`.
```cs
public IConfigurationRoot configuration { get; }

public Startup(IHostingEnvironment env)
{
    configuration = new ConfigurationBuilder()
                        .AddEnvironmentVariables()
                        .AddJsonFile(env.ContentRootPath + "/config.json")
                        .Build();
}
```
* With this in place, we can now use this in the `ConfigureServices` method:
```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<FeatureToggles>(x => new FeatureToggles
    {
        EnableDeveloperExceptions =
            configuration.GetValue<bool>("FeatureToggles:EnableDeveloperExceptions")
    });
}
```

# Adding ASP.NET MVC to the application
* The MVC framework is in the `Microsoft.AspNetCore.Mvc.Core` package, and can be added by writing
```cs
app.UseMvc();
```
* This method also accepts an optional configuration function, where you can define the route patterns that ASP.NET Core MVC should listen to.
```cs
app.UseMvc(routes =>
{
    routes.MapRoute("Default",
        "{controller=Home}/{action=Index}/{id?}"
    );
});
```
* This alone won't work, and will throw an exception saying it expects additional services, which refers to the fact that it uses dependency injection, and requires them to be registered in the `ConfigureServices` method. However, the framework provides methods that can be called, which provide default configurations. We do this by adding to `ConfigureServices`,
```cs
services.AddMvc();
```
This function is in the `Microsoft.AspNetCore.Mvc` package.
* ASP.NET Core MVC will find controller classes anywhere, but the convention is to put them in a `Controllers` folder.
* Every action in a controller returns an `IActionResult`. The simplest one is a `ContentResult`, which you can do by adding:
```cs
public class HomeController : Controller
{
    public IActionResult Index()
    {
        return new ContentResult { Content = "Hello, World!" };
    }
}
```

# Passing parameters to controllers
* Since actions are simply methods, they can accept arguments as well. These can be passed as query strings or form fields, or as part of the URL (in the route defined above, this would be the `id` parameter).
* We can also set types for the parameters. This is called model binding. For example:
```cs
public IActionResult Post(int id)
{
    return new ContentResult { Content = id.ToString() };
}
```
* If we now go to `/blog/post/test`, we get 0 on the screen. Since `test` isn't a valid integer, rather than throwing an error, the framework simply returns the default value.
* Sometimes, we want to be able to differentiate between the default value when passed explicitly, and the default value when a parsing error occurs. There are two approaches for this, both of which are C# features.
    * Use a nullable type. In this case, the default value becomes `null`.
    ```cs
    public IActionResult Post(int? id)
    {
        if (id == null)
            return new ContentResult { Content = "null" };
        return new ContentResult { Content = id.ToString() };
    }
    ```
    * Use the C# default argument syntax to explicitly provide a default value.

# Routing
* We can define route patterns, as done above. 
* An = denotes a default value, and a ? denotes an optional value.
* Thus, in the above route, all of `/home/index`, `/home`, and `/` map to the same action.
* We can also specify constraints, like types on the parameters. This is done by adding a colon. Example:
```cs
"{controller=Home}/{action=Index}/{id:int?}"
```

## Customizing Application URLs
* We can customize what action occurs for what URL pattern rather than following the default convention if we prefer. We do this by adding a `Route` attribute with a string parameter above the action. The order the parameters occur in the argument list to the function does not have to match the order that they appear in the route pattern.
```cs
[Route("blog/{year:int}/{month:int}/{key}")]
public IActionResult Post(int month, int year, string key)
{
    return new ContentResult
    {
        Content = string.Format("Year = {0}, Month = {1}, Key = {2}", year, month, key)
    };
}
```
* We can also take out common parts of the routes and promote them to the controllers themselves.
```cs
[Route("blog")]
public class BlogController : Controller
{
    public IActionResult Index()
    {
        return View();
    }

    [Route("{year:min(2000)}/{month:range(1, 12)}/{key}")]
    public IActionResult Post(int month, int year, string key)
    {
        return new ContentResult
        {
            Content = string.Format("Year = {0}, Month = {1}, Key = {2}", year, month, key)
        };
    }
}
```
*  Applying the route attribute at the controller level does not automatically apply the custom route to actions that don't have the route attributes applied. We can use an empty string for the parameter of the `Route` attribute in cases where we want the custom route.
* Adding custom routes like this removes them from the candidates for the more generic routes defined in the `Startup` class.

# Rendering Dynamic Content with Razor
* The default code for each action has `return View();`. ASP.NET Core MVC looks for the view in 2 folders, `/Views/Home/Index.cshtml`, and `/Views/Shared/Index.cshtml` (for the Index action of the Home controller).  

* HTML is valid Razor syntax. To add C# code, simply use the @ symbol. Example:
```
<p>
    Copyright @DateTime.Now.Year
</p>
```
* The real power of Razor comes from HTML Helpers. The most common is the `Html.ActionLink` helper, which generates anchor tags based on a controller and an action name. This version takes 3 parameters - the link text, the action, and the controller where the action lives. Example:
```
@Html.ActionLink("Read Our Blog", "Index", "Blog")
```
* An alternate form of this takes two additional parameters, a `RouteData` object, and a `HtmlAttributes` object. In the below example, we use an anonymous type for the last parameter.
```
@Html.ActionLink("Read Our Blog", "Index", "Blog", null, new { title = "Read Our Blog!" })
```
* An alternative to `Html.ActionLink` is the `Url.Action` helper. This takes the names of the action and controller. However, this only renders the URL of the link, and no additional HTML. The equivalent to the above would be:
```
<a href="@Url.Action("Index", "Blog)" title="Read Our Blog!">Read Our Blog</a>
```

# Layouts with Razor
* Layouts let you extract common markup into a different Razor view, which is put into the `Shared` views folder.
* We put shared HTML markup into `/Views/Shared/_Layout.cshtml`. The `_Layout` is simply a naming convention to indicate this is a layout page.
* In the layout page, we remove all the content, leaving only the shared markup. Then, where the page content will fit in, we add `@RenderBody()`.
* To use this layout, in `/Views/Home/index.cshtml`, we remove all the code, and simply type:
```
@{
    Layout = "_Layout";
}

<div>
    <!-- Body content here -->
</div>
```
* The MVC framework also has a `@RenderSection` helper when multiple parts of the side need to change for different pages. To do this, use 
```
@RenderSection("section_name");
```
in the layout page. Then, in the page using this layout, add
```
@section section_name {
    <h2>The markup here
}
```
* `RenderSection` takes an optional second parameter, which is a boolean telling whether the section is required or not.
* You can also define a default markup for a section as shown:
```
@if (IsSectionDefined("header"))
{
    @RenderSection("header", false)
} else
{
    <h1>Explore our world your way</h1>
}
```