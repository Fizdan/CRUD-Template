# CRUD-Template

To Run
```bash
dotnet restore
docker compose up
cd template.api
dotnet ef database update
dotnet run
```
---
## To Recreate

- Create Project Solution
```bash
mkdir 'CRUD Template'
dotnet new sln
```

- Create Required Module
```bash
dotnet new classlib -o template.lib
dotnet sln add ./template.lib/template.lib.csproj

dotnet new webapi -o template.api
dotnet sln add ./template.api/template.api.csproj

dotnet new xunit -o template.test
dotnet sln add ./template.test/template.test.csproj
```

- Add Reference to Other Module
```bash
dotnet add ./template.api/template.api.csproj reference ./template.lib/template.lib.csproj
dotnet add ./template.test/template.test.csproj reference ./template.lib/template.lib.csproj
dotnet add ./template.test/template.test.csproj reference ./template.api/template.api.csproj
```

- Create DB Context with IdentityUser
```C#
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore;

namespace template.lib.Models;

public class ApplicationDbContext : IdentityDbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

- Add to Startup/Program
```C#
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(
        builder.Configuration.GetConnectionString("DefaultConnection"), 
        b => b.MigrationsAssembly("template.api"))
    );
builder.Services.AddDatabaseDeveloperPageExceptionFilter();

builder.Services.AddDefaultIdentity<IdentityUser>(options => 
    options.SignIn.RequireConfirmedAccount = false)
    .AddEntityFrameworkStores<ApplicationDbContext>()
    .AddDefaultTokenProviders();
```

- Create Migration & Update Database
```bash
dotnet ef migrations add FirstInit
dotnet ef database update
```

- Create controller with Manager Provided from IdentityUser([example](template.api/Controllers/AuthController.cs))

- Dotnet Run
---
### Reference Link

ASP Net Identity User 
- https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-6.0&tabs=netcore-cli
- https://www.tektutorialshub.com/asp-net-core/asp-net-core-identity-tutorial/


Postgre Database
- https://medium.com/@RobertKhou/getting-started-with-entity-framework-core-postgresql-c6fa09681624
- https://medium.com/@ankushjain358/entity-framework-core-with-postgresql-database-first-ab03bf1079c4