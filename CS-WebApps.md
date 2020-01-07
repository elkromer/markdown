---
tags:
  - ASP.NET
  - Core
  - Web
  - App
---

# ASP.NET Core Razor Pages

### Structure
- `Pages`: Contains Razor pages and supporting files. 
- `wwwroot`: Contains static files such as HTML files, JS files, CSS files 
- `appSettings.json`: Contains configuration data such as connection strings
- `Program.cs`: Contains the entry point for the program
- `Startup.cs`: Contains code that configures app behavior, such as whether it requires conset for cookies

Each razor page is a pair of files. A `*.cshtml` file that contains HTML markup mixed with C# code using Razor syntax. A `*.cshtml.cs` file that contains C# code that handles page events. Supporting files have names that begin with underscore

## Notes on Layouts

Common HTML structures are frequently used by many pages within an app. All the shared elements can be defined in a `_Layout.cshtml` file, which can then be referenced by any view used within the app. Layouts reduce duplicate code in views

The layout defines a top level template for views in the app. Apps can define more than one layout with different views specifying different layouts. Razor views have a Layout property. Views specify a layout by setting the property.

Every layout must call `@RenderBody()` - Wherever the call is placed the contents of the view will be rendered.

## Notes on Sections

Layouts can optionally reference one or more sections by calling `@RenderSection()`. Sections provide a way to organize where certain page elements should be placed. Each call to RenderSection can specify whether that section is required or optional

`@RenderSection("Scripts", required: false)`

Individual views specify the content to be rendered within a section using the `@section` syntax. If a page or view defines a section it must be rendered or an error will occur

```cs
@section Scripts {
    <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

By default, the body and all sections in a content page must all be rendered by the layout page. The razor view engine enforces this by tracking whether the body and each section have been rendered. The body and every section in a Razor page must be either rendered or ignored.

## Notes on Shared Directives

Views and pages can use Razor directives to import namespaces and use dependency injection. Directives shared by many views may be specified in a common `_ViewImports.cshtml` file 

A ViewImports file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders. ViewImports files are processed starting at the root level and then for each folder leading up to the location of the page or view itself. ViewImports settings specified at the root level may be overridden at the folder level

Suppose the root level `_ViewImports.cshtml` file includes...
```cs
  @model Model1 
  @addTagHelper *, MyTagHelper1
```
Suppose a subfolder `_ViewImports.cshtml` file includes... 
```cs
  @model Model2
  @addTagHelper *, MyTagHelper2
```

Pages and views in the subfolder will have access to both Tag Helpers and the `Model2` model

Tag helpers all run. The closest `@model` import to the view overrides any others. That is the behavior you see above. The closest `@inherits` import to the view overrides any others. `@using` imports all run; duplicates ignored. `@inject` imports run for each property; the closest one to the view overrides any others with the same name.
  
### Running code before each view

Code that needs to run before each view or page should be placed in the `_ViewStart` file. The statements listed in the ViewStart are run before every full view (not layouts, and not partial views). Like ViewImports, ViewStart is heirarchial. ViewImports and ViewStarts not usually placed in Shared folders

## Notes on Dependency Injection

ASP.NET Core is built with dependency injection. Services (such as EF Core DB Context) are registered with dependency injection during application startup. Components that require these services (such as Razor Pages) are provided these services via constructor parameters 

The scaffolding tool in VS automatically creates a DB context and registers it with the dependency injection container. This happens in the `Startup.ConfigureServices()` method

## Notes on Working with Databases

The `*Context.cs` file coordinates EF Core functionality (Create, Read, Update, Delete) for the `*Model.cs`. The data context is derived from the DbContext class. The data context specifies which entities are included in the data model.

In the data context class a `DbSet<Model>` property is created for the "entity set". In Entity Framework, an entity set corresponds to a database table. An entity corresponds to a row in the table. The name of the connection string is passed in to the context by calling on a method on a DbContextOptions object.  

# ASP.NET Core MVC

ASP.NET Core MVC is a VS framework for building web apps and APIs with the MVC design pattern. 

`Model` responsibilities
- The state of the application and any business logic or operations that should be performed by it
- Classes that represent the data of the app
- The model classes use validation logic to enforce business rules for that data. Typically, model objects retrieve and store model state in a database

`View` responsibilities 
- presents the content through the UI

`Controller` responsibilities 
- components that handle user interaction, work with the model, and ultimately select a view to render.
- Classes that handle browser requests. They retrieve model data and call view templates that return a response. 

In an MVC app, the view only displays information; the controller handles and responds to user input and interaction. For example, the controller handles route data and query-string values, and passes these values to the model. The model might use these values to query the database. For example, `https://localhost:5001/Home/Privacy` has route data of Home (the controller) and Privacy (the action method to call on the home controller). `https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.

## Routes

ASP.NET Core MVC is build on routing, a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs. This enables you to define your application's URL naming patters that work well for search engine optimization. "Convention-based routing" is what allows you to globally define the URL formats that your application accepts. 

When an incoming request is received the routing engine parses the URL and patches it to one of the define URL formats and then calls the associated controller's action method.

```cs
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

Attribute routing enables you to specify routing information by decorating controllers and actions with attributes that define the applicaiton's routes

```
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

## Model Binding

ASP.NET Core MVC model binding converts client request data (form values, route data, query string params, http headers) into objects that the controller can handle. As a result, your controller logic doesn't have to do the work of figuring out the request data; it simply has the data as paramters to its action methods.

`public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }`

Validation is supported by decorating the model object with data annotation validation attributes. The validation attributes are checked on the client side before values are posted to the server as well as on the side of the server before the controller action is called.

```cs
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```
A controller action:
```cs
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```
