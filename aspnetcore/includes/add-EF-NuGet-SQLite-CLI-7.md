Run the following .NET CLI commands:

```dotnetcli
dotnet tool uninstall --global dotnet-aspnet-codegenerator
dotnet tool install --global dotnet-aspnet-codegenerator --prerelease
dotnet tool uninstall --global dotnet-ef
dotnet tool install --global dotnet-ef --prerelease
dotnet add package Microsoft.EntityFrameworkCore.Design --prerelease
dotnet add package Microsoft.EntityFrameworkCore.SQLite --prerelease
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --prerelease
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --prerelease
```

The preceding commands add:
* The [command-line interface (CLI) tools for EF Core](/ef/core/miscellaneous/cli/dotnet)
* The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Design time tools for EF Core
* The EF Core SQLite provider, which installs the EF Core package as a dependency.
* Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.

For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.
