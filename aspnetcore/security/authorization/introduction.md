---
title: Introduction to authorization in ASP.NET Core
author: wadepickett
description: Learn the basics of authorization and how authorization works in ASP.NET Core apps.
ms.author: wpickett
ms.date: 10/14/2016
uid: security/authorization/introduction
---
# Introduction to authorization in ASP.NET Core

<a name="security-authorization-introduction"></a>

Authorization refers to the process that determines what a user is able to do. For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them. A non-administrative user working with the library is only authorized to read the documents.

Authorization is separate and distinct from authentication. However, authorization relies on an authentication mechanism. Authentication is the process of verifying a user's identity, which may result in the creation of one or more identity objects for the user.

For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.

## Authorization types

ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model. Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements. Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.

## Namespaces

Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.

Consult the documentation on [simple authorization](xref:security/authorization/simple).

## Additional resources

* <xref:fundamentals/minimal-apis/security>
* <xref:blazor/security/index>
