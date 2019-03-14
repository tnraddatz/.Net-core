# .Net-core
Learning Guide for .Net Core MVC

#Controllers 

##Creating Controllers 
Create a new controller by right clicking the controllers folder and clicking: [add] [empty]


##Routing 
Routing is done in the Startup.cs file, this file is where most configurations are 

The default routing format is 
***app.UseMvc(routes =>
{
    routes.MapRoute(
        name: "default",
        template: "{controller=Home}/{action=Index}/{id?}");
}); ***
