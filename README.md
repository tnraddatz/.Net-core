# .Net-core
Learning Guide for .Net Core MVC

#Controllers 

##Creating Controllers 
Create a new controller by right clicking the controllers folder and clicking: [add] [empty]


##Routing 
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
