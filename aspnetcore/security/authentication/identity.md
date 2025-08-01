---
title: Introduction to Identity on ASP.NET Core
author: wadepickett
description: Use Identity with an ASP.NET Core app. Learn how to set password requirements (RequireDigit, RequiredLength, RequiredUniqueChars, and more).
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 04/26/2024
uid: security/authentication/identity
---
# Introduction to Identity on ASP.NET Core

:::moniker range=">= aspnetcore-8.0"

ASP.NET Core Identity:

* Is an API that supports user interface (UI) login functionality.
* Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.

Users can create an account with the login information stored in Identity or they can use an external login provider. Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).

[!INCLUDE[](~/includes/requireAuth.md)]

The [Identity source code](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) is available on GitHub. [Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.

Identity is typically configured using a SQL Server database to store user names, passwords, and profile data. Alternatively, another persistent store can be used, for example, Azure Table Storage.

In this topic, you learn how to use Identity to register, log in, and log out a user. Note: the templates treat username and email as the same for users. For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).

For more information on Identity in Blazor apps, see <xref:blazor/security/index> and the articles that follow it in the Blazor documentation.

ASP.NET Core Identity isn't related to the [Microsoft identity platform](/azure/active-directory/develop/). Microsoft identity platform is:

* An evolution of the Azure Active Directory (Azure AD) developer platform.
* An alternative identity solution for authentication and authorization in ASP.NET Core apps.

[!INCLUDE[](~/includes/DuendeIdentityServer.md)]

[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## Create a Blazor Web App with authentication

Create an ASP.NET Core Blazor Web App project with Individual Accounts.

> [!NOTE]
> For a Razor Pages experience, see the [Create a Razor Pages app with authentication](#create-a-razor-pages-app-with-authentication) section.
>
> For an MVC experience, see the [Create an MVC app with authentication](#create-an-mvc-app-with-authentication) section.

# [Visual Studio](#tab/visual-studio)

* Select the **Blazor Web App** template. Select **Next**.
* Make the following selections:
  * **Authentication type**: **Individual Accounts**
  * **Interactive render mode**: **Server**
  * **Interactivity Location**: **Global**
* Select **Create**.

# [.NET CLI](#tab/net-cli)

```dotnetcli
dotnet new blazor -au Individual -o BlazorApp1
```

The `-o|--output` option creates a folder for the app and sets the app name/namespace.

The preceding command creates a Blazor Web App using SQLite. To create the app with LocalDB, run the following command:

```dotnetcli
dotnet new blazor -au Individual -uld -o BlazorApp1
```

---

The generated project includes Identity Razor components. The components are found in the `Components/Account` folder. For example:

* `/Components/Account/Pages/Register`
* `/Components/Account/Pages/Login`
* `/Components/Account/Pages/Manage/ChangePassword`

Identity Razor components are described individually in the documentation for specific use cases and are subject to change each release. When you generate a Blazor Web App with Individual Accounts, Identity Razor components are included in the generated project. The Identity Razor components can also be inspected in the [Blazor project template in the ASP.NET Core reference source (`dotnet/aspnetcore` GitHub repository)](https://github.com/dotnet/aspnetcore/tree/main/src/ProjectTemplates/Web.ProjectTemplates/content/BlazorWeb-CSharp/BlazorWeb-CSharp/Components/Account). 

[!INCLUDE[](~/includes/aspnetcore-repo-ref-source-links.md)]

For more information, see <xref:blazor/security/index> and the articles that follow it in the Blazor documentation. Most of the articles in the *Security and Identity* area of the main ASP.NET Core documentation set apply to Blazor apps. However, the Blazor documentation set contains articles and guidance that supersedes or adds information. We recommend studying the general ASP.NET Core documentation set first, followed by accessing the articles in the Blazor *Security and Identity* documentation.

## Create a Razor Pages app with authentication

Create an ASP.NET Core Web Application (Razor Pages) project with Individual Accounts.

# [Visual Studio](#tab/visual-studio)

* Select the **ASP.NET Core Web App (Razor Pages)** template. Select **Next**.
* For **Authentication type**, select **Individual Accounts**.
* Select **Create**.

# [.NET CLI](#tab/net-cli)

```dotnetcli
dotnet new webapp -au Individual -o WebApp1
```

The `-o|--output` option creates a folder for the app and sets the app name/namespace.

The preceding command creates a Razor Pages app using SQLite. To create the app with LocalDB, run the following command:

```dotnetcli
dotnet new webapp -au Individual -uld -o WebApp1
```

---

The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor class library (RCL)](xref:razor-pages/ui-class). The Identity Razor class library exposes endpoints with the `Identity` area. For example:

* `Areas/Identity/Pages/Account/Register`
* `Areas/Identity/Pages/Account/Login`
* `Areas/Identity/Pages/Account/Manage/ChangePassword`

Pages are described individually in the documentation for specific use cases and are subject to change each release. To view all of the pages in the RCL, see the [ASP.NET Core reference source (`dotnet/aspnetcore` GitHub repository, `Identity/UI/src/Areas/Identity/Pages` folder)](https://github.com/dotnet/aspnetcore/tree/main/src/Identity/UI/src/Areas/Identity/Pages). You can *scaffold* individual pages or all of the pages into the app. For more information, see <xref:security/authentication/scaffold-identity>.

## Create an MVC app with authentication

Create an ASP.NET Core MVC project with Individual Accounts.

# [Visual Studio](#tab/visual-studio)

* Select the **ASP.NET Core Web App (Model-View-Controller)** template. Select **Next**.
* For **Authentication type**, select **Individual Accounts**.
* Select **Create**.

# [.NET CLI](#tab/net-cli)

```dotnetcli
dotnet new mvc -au Individual -o WebApplication1
```

The `-o|--output` option creates a folder for the app and sets the app name/namespace.

The preceding command creates an MVC app using SQLite. To create the web app with LocalDB, run the following command:

```dotnetcli
dotnet new mvc -au Individual -uld -o WebApplication1
```

---

The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor class library (RCL)](xref:razor-pages/ui-class). The Identity Razor class library is based on Razor Pages and exposes endpoints with the `Identity` area. For example:

* `Areas/Identity/Pages/Account/Register`
* `Areas/Identity/Pages/Account/Login`
* `Areas/Identity/Pages/Account/Manage/ChangePassword`

Pages are described individually in the documentation for specific use cases and are subject to change each release. To view all of the pages in the RCL, see the [ASP.NET Core reference source (`dotnet/aspnetcore` GitHub repository, `Identity/UI/src/Areas/Identity/Pages` folder)](https://github.com/dotnet/aspnetcore/tree/main/src/Identity/UI/src/Areas/Identity/Pages). You can *scaffold* individual pages or all of the pages into the app. For more information, see <xref:security/authentication/scaffold-identity>.

### Apply migrations

Apply the migrations to initialize the database.

# [Visual Studio](#tab/visual-studio)

Run the following command in the Package Manager Console (PMC):

`Update-Database`

# [.NET CLI](#tab/net-cli)

Migrations are not necessary at this step when using SQLite.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

For LocalDB, run the following command:

```dotnetcli
dotnet ef database update
```

---

### Test Register and Login

Run the app and register a user. Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw6"></a>

### Configure Identity services

Services are added in `Program.cs`. The typical pattern is to call methods in the following order:

1. `Add{Service}`
1. `builder.Services.Configure{Service}`

:::code language="csharp" source="identity/sample/WebApp6x/Program.cs" id="snippet_":::

The preceding code configures Identity with default option values. Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).

Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>. `UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.

The template-generated app doesn't use [authorization](xref:security/authorization/secure-data). `app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization. `UseRouting`, `UseAuthentication`, and `UseAuthorization` must be called in the order shown in the preceding code.

For more information on `IdentityOptions`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).

<!-- Start here for .NET 6 -->

## Scaffold Register, Login, LogOut, and RegisterConfirmation

# [Visual Studio](#tab/visual-studio)

Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files. Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.

# [.NET CLI](#tab/net-cli)

If you created the project with name **WebApp1**, and you're not using SQLite, run the following commands. Otherwise, use the correct namespace for the `ApplicationDbContext`:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

When using SQLite, append `--useSqLite` or `-sqlite`:

```dotnetcli
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation" --useSqLite
```

PowerShell uses semicolon as a command separator. When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.

For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### Examine Register

When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked. The user is created by <xref:Microsoft.AspNetCore.Identity.UserManager%601.CreateAsync(%600)> on the `_userManager` object:

:::code language="csharp" source="identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs" id="snippet" highlight="9":::

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/main/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### Log in

The Login form is displayed when:

* The **Log in** link is selected.
* A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.

When the form on the Login page is submitted, the `OnPostAsync` action is called. `PasswordSignInAsync` is called on the `_signInManager` object.

:::code language="csharp" source="identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs" id="snippet" highlight="10-11":::

For information on how to make authorization decisions, see <xref:security/authorization/introduction>.

### Log out

The **Log out** link invokes the `LogoutModel.OnPost` action. 

:::code language="csharp" source="identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs" highlight="36":::

In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.

<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignOutAsync%2A> clears the user's claims stored in a cookie.

Post is specified in the `Pages/Shared/_LoginPartial.cshtml`:

:::code language="cshtml" source="identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml" highlight="15":::

## Test Identity

The default web project templates allow anonymous access to the home pages. To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):

:::code language="csharp" source="identity/sample/WebApp3/Pages/Privacy.cshtml.cs" highlight="7":::

If you are signed in, sign out. Run the app and select the **Privacy** link. You are redirected to the login page.

### Explore Identity

To explore Identity in more detail:

* [Create full identity UI source](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine the source of each page and step through the debugger.

## Identity Components

All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## Migrating to ASP.NET Core Identity

For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/fx-to-core/examples/identity).

## Setting password strength

See [Configuration](#pw6) for a sample that sets the minimum password requirements.

## AddDefaultIdentity and AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> was introduced in ASP.NET Core 2.1. Calling `AddDefaultIdentity` is similar to calling the following:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity%2A>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI%2A>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders%2A>

See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.

## Prevent publish of static Identity assets

To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## Next Steps

* <xref:blazor/security/index>
* [ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/main/src/Identity)
* [How to work with Roles in ASP.NET Core Identity](https://www.yogihosting.com/aspnet-core-identity-roles/)
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/7114 -->
* For information on configuring Identity using SQLite, see [How to config Identity for SQLite (`dotnet/AspNetCore.Docs` #5131)](https://github.com/dotnet/AspNetCore.Docs/issues/5131).
* [Configure Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/fx-to-core/examples/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/mfa>
* <xref:host-and-deploy/web-farm>

:::moniker-end

[!INCLUDE[](./identity/includes/identity6.md)]
