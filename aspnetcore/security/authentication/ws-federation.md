---
title: Authenticate users with WS-Federation in ASP.NET Core
author: chlowell
description: This tutorial demonstrates how to use WS-Federation in an ASP.NET Core app.
monikerRange: '>= aspnetcore-2.1'
ms.author: wpickett
ms.custom: mvc, sfi-image-nochange
ms.date: 01/16/2019
uid: security/authentication/ws-federation
---
# Authenticate users with WS-Federation in ASP.NET Core

[!INCLUDE[](~/includes/not-latest-version.md)]

This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Microsoft Entra ID](/azure/active-directory/). It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).

For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics. However, the components differ in a couple of important ways.

By default, the new middleware:

* Doesn't allow unsolicited logins. This feature of the WS-Federation protocol is vulnerable to XSRF attacks. However, it can be enabled with the `AllowUnsolicitedLogins` option.
* Doesn't check every form post for sign-in messages. Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath%2A?displayProperty=nameWithType> property of the <xref:Microsoft.AspNetCore.Authentication.WsFederation.WsFederationOptions> class. This path can be shared with other authentication providers by enabling the <xref:Microsoft.AspNetCore.Authentication.WsFederation.WsFederationOptions.SkipUnrecognizedRequests%2A> option.

## Register the app with Active Directory

### Active Directory Federation Services

* Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:

![Add Relying Party Trust Wizard: Welcome](ws-federation/_static/AdfsAddTrust.png)

* Choose to enter data manually:

![Add Relying Party Trust Wizard: Select Data Source](ws-federation/_static/AdfsSelectDataSource.png)

* Enter a display name for the relying party. The name isn't important to the ASP.NET Core app.

* [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:

![Add Relying Party Trust Wizard: Configure Certificate](ws-federation/_static/AdfsConfigureCert.png)

* Enable support for WS-Federation Passive protocol, using the app's URL. Verify the port is correct for the app:

![Add Relying Party Trust Wizard: Configure URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> This must be an HTTPS URL. IIS Express can provide a self-signed certificate when hosting the app during development. Kestrel requires manual certificate configuration. See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.

* Click **Next** through the rest of the wizard and **Close** at the end.

* ASP.NET Core Identity requires a **Name ID** claim. Add one from the **Edit Claim Rules** dialog:

![Edit Claim Rules](ws-federation/_static/EditClaimRules.png)

* In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**. Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:

![Add Transform Claim Rule Wizard: Configure Claim Rule](ws-federation/_static/AddTransformClaimRule.png)

* Click **Finish** > **OK** in the **Edit Claim Rules** window.

### Microsoft Entra ID

* Navigate to the Microsoft Entra ID tenant's app registrations blade. Click **New application registration**:

![Microsoft Entra ID: App registrations](ws-federation/_static/AadNewAppRegistration.png)

* Enter a name for the app registration. This isn't important to the ASP.NET Core app.
* Enter the URL the app listens on as the **Sign-on URL**:

![Microsoft Entra ID: Create app registration](ws-federation/_static/AadCreateAppRegistration.png)

* Click **Endpoints** and note the **Federation Metadata Document** URL. This is the WS-Federation middleware's `MetadataAddress`:

![Microsoft Entra ID: Endpoints](ws-federation/_static/AadFederationMetadataDocument.png)

* Navigate to the new app registration. Click **Expose an API**. Click Application ID URI **Set** > **Save**. Make note of the  **Application ID URI**. This is the WS-Federation middleware's `Wtrealm`:

![Microsoft Entra ID: App registration properties](ws-federation/_static/AadAppIdUri.png)

## Use WS-Federation without ASP.NET Core Identity

The WS-Federation middleware can be used without Identity. For example:

:::moniker range=">= aspnetcore-3.0"

:::code language="csharp" source="ws-federation/samples/StartupNon31.cs" id="snippet":::

:::moniker-end

:::moniker range="< aspnetcore-3.0"

:::code language="csharp" source="ws-federation/samples/StartupNon21.cs" id="snippet":::

:::moniker-end

## Add WS-Federation as an external login provider for ASP.NET Core Identity

* Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.

* Add WS-Federation to `Startup.ConfigureServices`:

:::moniker range=">= aspnetcore-3.0"

:::code language="csharp" source="ws-federation/samples/Startup31.cs" id="snippet":::

:::moniker-end

:::moniker range="< aspnetcore-3.0"

:::code language="csharp" source="ws-federation/samples/Startup21.cs" id="snippet":::

:::moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### Log in with WS-Federation

Browse to the app and click the **Log in** link in the nav header. There's an option to log in with WsFederation:

![Log in page](ws-federation/_static/WsFederationButton.png)

With ADFS as the provider, the button redirects to an ADFS sign-in page:

![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)

With Microsoft Entra ID as the provider, the button redirects to a Microsoft Entra ID sign-in page:

![Microsoft Entra ID sign-in page](ws-federation/_static/AadSignIn.png)

A successful sign-in for a new user redirects to the app's user registration page:

![Register page](ws-federation/_static/Register.png)
