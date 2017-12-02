These notes have been prepared on my own based on [this course on LinkedIn Learning](https://www.linkedin.com/learning/learning-asp-dot-net-core-mvc).

# Creating a new project
* These notes assume this has been done by choosing the Empty Project template of an ASP.NET Core Web Application project.

# Responding to HTTP Requests
* The `Startup.cs` file already has a basic Hello World application, which looks like this:
```
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
```
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
```
var configuration = new ConfigurationBuilder()
                        .AddEnvironmentVariables()
                        .Build();
if (configuration["IsDevEnv"] == "True")
{
    app.UseDeveloperExceptionPage();
}
```
* This code is ugly and we’d like to automatically convert the value to a Boolean. This isn’t available built-in, but is possible through an extension.
```
if (configuration.GetValue<bool>("IsDevEnv"))
{
    app.UseDeveloperExceptionPage();
}
```
* To add configuration settings from a JSON file, we simply add a call to `AddJsonFile` method, and specify the path as an argument. Complex JSON objects can also be parsed, but a colon, rather than a period, is used to read nested properties.
```
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
```
public class FeatureToggles
{
    public bool EnableDeveloperExceptions { get; set; }
}
```
and then modify the `Configure` method as:
```
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
```
 public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<FeatureToggles>();
}
```
* ASP.NET Core allows you to control how objects are created here, by allowing you to pass a function to the `AddTransient` (and other) methods that returns an instance of the type.
```
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<FeatureToggles>(x => new FeatureToggles());
}
```
* The above is what ASP.NET Core does by default. Alternatively, we can populate its properties from configuration settings. For that, we move the configuration object to a property and populate it in the startup constructor as opposed to the `Configure` method. Since we use the `env` variable, we have to bring along a reference to the `IHostingEnvironment`.
```
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
```
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<FeatureToggles>(x => new FeatureToggles
    {
        EnableDeveloperExceptions =
            configuration.GetValue<bool>("FeatureToggles:EnableDeveloperExceptions")
    });
}
```