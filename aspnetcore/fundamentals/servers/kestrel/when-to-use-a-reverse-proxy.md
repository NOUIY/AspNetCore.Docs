---
title: When to use a reverse proxy with the ASP.NET Core Kestrel web server
author: tdykstra
description: Learn about when to use a reverse proxy in front of Kestrel, the cross-platform web server for ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/06/2025
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
---

# When to use Kestrel with a reverse proxy

[!INCLUDE[](~/includes/not-latest-version.md)]

Kestrel can be used by itself or with a *reverse proxy server*. A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel. Examples of a reverse proxy server include:

* [Internet Information Services (IIS)](https://www.iis.net/)
* [Nginx](https://nginx.org)
* [Apache](https://httpd.apache.org/)
* [YARP: Yet Another Reverse Proxy](https://dotnet.github.io/yarp/)

Kestrel used as an edge (Internet-facing) web server:

:::image source="_static/kestrel-to-internet2.png" alt-text="Kestrel communicates directly with the Internet without a reverse proxy server":::

Kestrel used in a reverse proxy configuration:

:::image source="_static/kestrel-to-internet.png" alt-text="Kestrel communicates indirectly with the Internet through a reverse proxy server, such as IIS, Nginx, or Apache":::

Either configuration, with or without a reverse proxy server, is a supported hosting configuration.

When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported. When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers. A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.

Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.

A reverse proxy:

* Can limit the exposed public surface area of the apps that it hosts.
* Provides an additional layer of configuration and defense-in-depth cybersecurity.
* Might integrate better with existing infrastructure.
* Simplifies load balancing and secure communication (HTTPS) configuration. Only the reverse proxy server requires the X.509 certificate for the public domain(s). That server can communicate with the app's servers on the internal network using plain HTTP or HTTPS with locally managed certificates. Internal HTTPS increases security but adds significant overhead.

> [!WARNING]
> Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).

## Additional resources

<xref:host-and-deploy/proxy-load-balancer>

