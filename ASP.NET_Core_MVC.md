---
tags:
  - ASP,NET,ASP.NET,Core,MVC
---

ASP.NET Core MVC is a VS framework for building web apps and APIs with the MVC design pattern. 

Model Responsibilities: represents the state of the application and any business logic or operations that should be performed by it
   MS -> Classes that represent the data of the app. The model classes use validation logic to enforce business rules for that data. Typically, model objects retrieve and store model state in a database. 
View Responsibilities: presents the content through the UI
Controller Responsibilities: components that handle user interaction, work with the model, and ultimately select a view to render.
  MS -> Classes that handle browser requests. They retrieve model data and call view templates that return a response. In an MVC app, the view only displays information; the controller handles and responds to user input and interaction. For example, the controller handles route data and query-string values, and passes these values to the model. The model might use these values to query the database. For example, https://localhost:5001/Home/Privacy has route data of Home (the controller) and Privacy (the action method to call on the home controller). https://localhost:5001/Movies/Edit/5 is a request to edit the movie with ID=5 using the movie controller.

Routes

ASP.NET Core MVC is build on routing, a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs. This enables you to define your application's URL naming patters that work well for search engine optimization. "Convention-based routing" is what allows you to globally define the URL formats that your application accepts. When an incoming request is received the routing engine parses the URL and patches it to one of the define URL formats and then calls the associated controller's action method.

routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");

Attribute routing enables you to specify routing information by decorating controllers and actions with attributes that define the applicaiton's routes:

[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}

Model Binding

ASP.NET Core MVC model binding converts client request data (form values, route data, query string params, http headers) into objects that the controller can handle. As a result, your controller logic doesn't have to do the work of figuring out the request data; it simply has the data as paramters to its action methods.

public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }

Validation is supported by decorating the model object with data annotation validation attributes. The validation attributes are checked on the client side before values are posted to the server as well as on the side of the server before the controller action is called.

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

A controller action:

public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}