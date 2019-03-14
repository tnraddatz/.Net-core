# .Net-core
Learning Guide for .Net Core MVC

# Models 

## Adding a new data model class 
Right-click the Models folder > Add > Class. Name the class MyModel.

```
using System;
using System.ComponentModel.DataAnnotations;

namespace MvcMovie.Models
{
    public class Movie
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [DataType(DataType.Date)]
        public DateTime ReleaseDate { get; set; }
        public string Genre { get; set; }
        public decimal Price { get; set; }
    }
}
```
The Movie class contains:
- The Id field which is required by the database for the primary key.

- `[DataType(DataType.Date)]:` The DataType attribute specifies the type of the data (Date). With this attribute:

  - The user is not required to enter time information in the date field.
  - Only the date is displayed, not time information.
  
*In this section, the movie model is scaffolded. That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.*

## Scaffolding model
*In this section, the movie model is scaffolded. That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.*

- In Solution Explorer, right-click the Controllers folder > Add > New Scaffolded Item.
- In the Add Scaffold dialog, select MVC Controller with views, using Entity Framework > Add.
- Complete the Add Controller dialog:
  - Model class: Movie (MvcMovie.Models)
  - Data context class: Select the + icon and add the default MvcMovie.Models.MvcMovieContext
  - Views: Keep the default of each option checked
  - Controller name: Keep the default MoviesController
  - Select Add

Visual Studio creates:

- An Entity Framework Core database context class (Data/MvcMovieContext.cs)
- A movies controller (Controllers/MoviesController.cs)
- Razor view files for Create, Delete, Details, Edit, and Index pages (Views/Movies/*.cshtml)*

The automatic creation of the database context and `CRUD` `(create, read, update, and delete)` action methods and views is known as scaffolding.

## Migrations 
1. From the Tools menu, select NuGet Package Manager > Package Manager Console (PMC).
2. In the PMC, enter the following commands:
  - `Add-Migration Initial`
  - `Update-Database`

The `Add-Migration` command generates code to create the initial database schema.

The database schema is based on the model specified in the `MvcMovieContext class` (in the Data/MvcMovieContext.cs file). The `Initial` argument is the migration name. Any name can be used, but by convention, a name that describes the migration is used. For more information, see Tutorial: Using the migrations feature - ASP.NET MVC with EF Core.

The Update-Database command runs the Up method in the Migrations/{time-stamp}_InitialCreate.cs file, which creates the database.

# Examine the Context registered with dependency injection 
ASP.NET Core is built with dependency injection (DI). Services (such as the EF Core DB context) are registered with DI during application startup. Components that require these services (such as Razor Pages) are provided these services via constructor parameters. 

The scaffolding tool automatically created a DB context and registered it with the DI container.
Examine the following Startup.ConfigureServices method. The highlighted line was added by the scaffolder:
```
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies 
        // is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
    });


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);

    services.AddDbContext<MvcMovieContext>(options =>
--->     options.UseSqlServer(Configuration.GetConnectionString("MvcMovieContext")));
}
```

The MvcMovieContext coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the Movie model. The data context (MvcMovieContext) is derived from Microsoft.EntityFrameworkCore.DbContext. The data context specifies which entities are included in the data model:

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.EntityFrameworkCore;

namespace MvcMovie.Models
{
    public class MvcMovieContext : DbContext
    {
        public MvcMovieContext (DbContextOptions<MvcMovieContext> options)
            : base(options)
        {
        }

        public DbSet<MvcMovie.Models.Movie> Movie { get; set; }
    }
}
```
The preceding code creates a DbSet<Movie> property for the entity set. In Entity Framework terminology, an entity set typically corresponds to a database table. An entity corresponds to a row in the table.

The name of the connection string is passed in to the context by calling a method on a DbContextOptions object. For local development, the ASP.NET Core configuration system reads the connection string from the appsettings.json file.


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
