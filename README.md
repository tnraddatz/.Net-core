# .Net-core
Learning Guide for .Net Core MVC


# Views 
Views use Razor view files, which have embedded c# code within them. *You create a view template file using Razor. Razor-based view templates have a .cshtml file extension. They provide an elegant way to create HTML output with C#.*

## Adding Views 
- Right click on the Views folder, and then Add > New Folder and name the folder HelloWorld.
- Right click on the Views/HelloWorld folder, and then Add > New Item.
- In the Add New Item - MvcMovie dialog
  - In the search box in the upper-right, enter view
  - Select Razor View
  - Keep the Name box value, Index.cshtml.
  - Select Add

## Embedding C# Code in View
To embed code to the view use the `@` symbol and `{}` to encapsulate C# Code 
```
@{
    ViewData["Title"] = "Welcome";
}

<h2>Welcome</h2>

<ul>
    @for (int i = 0; i < (int)ViewData["NumTimes"]; i++)
    {
        <li>@ViewData["Message"]</li> 
    }
</ul>
```
## \_Layout File [Renders your view in its body]
*The Views/_ViewStart.cshtml file brings in the Views/Shared/_Layout.cshtml file to each view. The Layout property can be used to set a different layout view, or set it to null so no layout file will be used.*
all partial or shared views will begin with `_`, meaning that they can be rendered from another view
add `<title>@ViewData["Title"] - Movie App</title>` to the `<head>` element under the `<meta name="viewport"...>` line

## Routing to Controllers from a view (Buttons, links, etc ...) 
`<a asp-area="" asp-controller="Movie" asp-action="Index" class="navbar-brand">Movie App</a>`
the asp-area can be left blank if your app does not use areas, `asp-controller` is the designated controller, `asp-action` is the action within the controller

## Rendering from controllers 
```
public IActionResult Index()
{
    return View();
}
```
The preceding code calls the controller's View method. It uses a view template to generate an HTML response. Controller methods (also known as action methods), such as the `Index` method above, generally return an IActionResult (or a class derived from ActionResult), not a type like string.

# Controllers 

## Creating Controllers 
Create a new controller by right clicking the controllers folder and clicking:` [add] [empty] `

## Controller Parameters 
```
public string Welcome(string name, int numTimes = 1)
{
    return HtmlEncoder.Default.Encode($"Hello {name}, NumTimes is: {numTimes}");
}
```
The preceding code:

- Uses the C# optional-parameter feature to indicate that the numTimes parameter defaults to 1 if no value is passed for that parameter.
- Uses HtmlEncoder.Default.Encode to protect the app from malicious input (namely JavaScript).
- Uses Interpolated Strings in $"Hello {name}, NumTimes is: {numTimes}".

```
public string Welcome(string name, int ID = 1)
{
    return HtmlEncoder.Default.Encode($"Hello {name}, ID: {ID}");
}
```
This time the third URL segment matched the route parameter id. The Welcome method contains a parameter id that matched the URL template in the `MapRoute` method. The trailing `?` (in `id?`) indicates the id parameter is optional.

## Passing Data from the controller to the view 

The two actions below are the `Index()` action and `Welcome` action, both of these will have corresponding views i.e. `index.cshtml` and `Welcome.cshtml`.

```
namespace MvcMovie.Controllers
{
    public class HelloWorldController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }

        public IActionResult Welcome(string name, int numTimes = 1)
        {
            ViewData["Message"] = "Hello " + name;
            ViewData["NumTimes"] = numTimes;

            return View();
        }
    }
}
```

# Routing 
Routing is done in the Startup.cs file, this file is where most configurations are 

The default routing format is 
```
app.UseMvc(routes =>
{
    routes.MapRoute(
        name: "default",
        template: "{controller=Home}/{action=Index}/{id?}");
});
```
The first URL segment determines the controller class to run. So `localhost:xxxx/HelloWorld` maps to the HelloWorldController class. The second part of the URL segment determines the action method on the class. So `localhost:xxxx/HelloWorld/Index` would cause the Index method of the HelloWorldController class to run. Notice that you only had to browse to `localhost:xxxx/HelloWorld` and the Index method was called by default. This is because Index is the default method that will be called on a controller if a method name isn't explicitly specified. The third part of the URL segment (id) is for route data

## Routing to Controllers from a view (Buttons, links, etc ...) 
`<a asp-area="" asp-controller="Movie" asp-action="Index" class="navbar-brand">Movie App</a>`
the asp-area can be left blank, `asp-controller` is the designated controller, `asp-action` is the action within the controller
