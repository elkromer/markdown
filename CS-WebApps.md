---
tags:
  - ASP.NET
  - Core
  - Web
  - App
---

Web Application Programming with ASP.NET Core

VS Web Application

Project Structure
  Pages folder: Contains Razor pages and supporting files. Each razor page is a pair of files. A .cshtml file that contains HTML markup mixed with C# code using Razor syntax. A .cshtml.cs file that contains C# code that handles page events. Supporting files have names that begin with underscore.
  wwwroot folder: Contains static files such as HTML files, JS files, CSS files. 
  appSettings.json: Contains configuration data such as connection strings.
  Program.cs: Contains the entry point for the program.
  Startup.cs: Contains code that configures app behavior, such as whether it requires conset for cookies.

Layout
  Common HTML structures are frequently user by many pages within an app. All the shared elements can be defined in a layout file, which can then be referenced by any view used within the app. Layouts reduce duplicate code in views. 

  Razor Pages: Pages/Shared/_Layout.cshtml
  MVC Views: Views/Shared/_Layout.cshtml

  The layout defines a top level template for views in the app. Apps can define more than one layout with different views specifying different layouts. Razor views have a Layout propery. Views specify a layout by setting the property.

  Every layout must call RenderBody - Wherever the call is placed the contents of the view will be rendered.

  Sections
    Layouts can optionally reference one or more sections by calling RenderSection. Sections provide a way to organize where certain page elements should be placed. Each call to RenderSection can specify whether that section is required or optional. 

    @RenderSection("Scripts", required: false)

    Individual views specify the content to be rendered within a section using the @section syntax. If a page or view defines a section it must be rendered or an error will occur

    @section Scripts {
        <script type="text/javascript" src="~/scripts/main.js"></script>
    }

    By default, the body and all sections in a content page must all be rendered by the layout page. The razor view engine enforces this by tracking whether the body and each section have been rendered. The body and every section in a Razor page must be either rendered or ignored.

  Importing Shared Directives
    Views and pages can ise Razor directives to import namespaces and use dependency injection. Directives shared by many views may be specified in a common _ViewImports.cshtml file. 
    
    A _ViewImports file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders. _ViewImports files are processed starting at the root level and then for each folder leading up to the location of the page or view itself. _ViewImports settings specified at the root level may be overridden at the folder level.

    Suppose the root level _ViewImports file includes 
      @model Model1 
      @addTagHelper *, MyTagHelper1
    Suppose a subfolder _ViewImports file includes 
      @model Model2
      @addTagHelper *, MyTagHelper2
    
    Pages and views in the subfolder will have access to both Tag Helpers and the MyModel2 model. Tag helpers all run. The closest @model import to the view overrides any others. That is the behavior you see above. The closest @inherits import to the view overrides any others. @using imports all run; duplicates ignored. @inject imports run for each property; the closest one to the view overrides any others with the same name.
  
  Running code before each view
    Code that needs to run before each view or page should be placed in the _ViewStart file. The statements listed in the _ViewStart are run before every full view (not layouts, and not partial views). Like _ViewImports, _ViewStart is heirarchial. _ViewImports and _ViewStarts not usually placed in Shared folders. 

ASP.NET Core is built with dependency injection. Services (such as EF Core DB Context) are registered with dependency injection during application startup. Components that require these services (such as Razor Pages) are provided these services via constructor parameters. 

The scaffolding tool in VS automatically creates a DB context and registers it with the dependency injection container. This happens in the Startup.ConfigureServices method.

Notes on working with databases:
The *Context file coordinates EF Core functionality (Create, Read, Update, Delete) for the * model. The data context is derived from the DbContext class. The data context specifies which entities are included in the data model.

In the data context class a DbSet<Model> property is created for the "entity set". In Entity Framework, an entity set corresponds to a database table. An entity corresponds to a row in the table. The name of the connection string is passed in to the context by calling on a method on a DbContextOptions object.   
