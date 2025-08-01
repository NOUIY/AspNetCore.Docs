---
uid: fundamentals/servers/yarp/header-guidelines
title: YARP HTTP header guidelines
description: Learn about YARP HTTP header guidelines.
author: wadepickett
ms.author: wpickett
ms.date: 04/03/2025
ms.topic: article
content_well_notification: AI-contribution
ai-usage: ai-assisted
---
# YARP HTTP header guidelines

Headers are a very important part of processing HTTP requests and each have their own semantics and considerations. Most headers are proxied by default, though some used to control how the request is delivered are automatically adjusted or removed by the proxy. The connections between the client and the proxy and between the proxy and the destination are independent. Therefore, headers that affect the connection and transport must be filtered. Many headers contain information like domain names, paths, or other details that may be affected when a reverse proxy is included in the application architecture. The following is a collection of guidelines about how specific headers might be impacted and what to do about them.

## YARP header filtering

YARP automatically removes request and response headers that could impact its ability to forward a request correctly, or that may be used maliciously to bypass features of the proxy. A complete list can be found [here](https://github.com/microsoft/reverse-proxy/blob/main/src/ReverseProxy/Forwarder/RequestUtilities.cs#L71), with some highlights described below.

### `Connection`, `KeepAlive`, `Close`

These headers control how the TCP connection is managed and are removed to prevent impacting the connection on the other side of the proxy.

### `Transfer-Encoding`

This header describes the format of the request or response body on the wire, e.g. 'chunked', and is removed because the format can vary between the internal and external connection. The incoming and outgoing HTTP stacks will add transport headers as needed.

### `TE`

Only the `TE: trailers` header value is allowed through the proxy since it's required for some gRPC implementations.

### `Upgrade`

This is used for protocols like WebSockets. It is removed by default and only added back for specifically supported protocols (WebSockets, SPDY).

### `Proxy-*`

These are headers used with proxies and are not considered appropriate to forward.

### `Alt-Svc`

This response header is used with HTTP/3 upgrades and only applies to the immediate connection.

### Distributed tracing headers

These headers include `TraceParent`, `Request-Id`, `TraceState`, `Baggage`, and `Correlation-Context`.

They're automatically removed based on <xref:System.Diagnostics.DistributedContextPropagator.Fields%2A?displayProperty=nameWithType>, allowing the forwarding <xref:System.Net.Http.HttpClient> to replace them with updated values.

You can opt out of modifying these headers by setting <xref:System.Net.Http.SocketsHttpHandler.ActivityHeadersPropagator%2A?displayProperty=nameWithType> to `null`:

```csharp
services.AddReverseProxy()
    .ConfigureHttpClient((_, handler) => handler.ActivityHeadersPropagator = null);
```

### `Strict-Transport-Security`

This header instructs clients to always use HTTPS, but there may be a conflict between values provided by the proxy and destination. To avoid confusion, the destination's value is not copied to the response if one was already added to the response by the proxy application.

## Other header guidelines

### `Host`

The Host header indicates which site on the server the request is intended for. This header is removed by default since the host name used publicly by the proxy is likely to differ from the one used by the service behind the proxy. This can be configured using the [`RequestHeaderOriginalHost`](xref:fundamentals/servers/yarp/transforms#requestheaderoriginalhost) transform.

### `X-Forwarded-*`, `Forwarded`

Because a separate connection is used to communicate with the destination, these request headers can be used to forward information about the original connection, such as the IP, scheme, port, and client certificate. `X-Forwarded-For`, `X-Forwarded-Proto`, `X-Forwarded-Host`, and `X-Forwarded-Prefix` are enabled by default. This information is subject to spoofing attacks so any existing headers on the request are removed and replaced by default. The destination app should be careful about how much trust it places in these values. See [transforms](xref:fundamentals/servers/yarp/transforms#defaults) for configuring these in the proxy. For guidance on configuring the destination app to read these headers, see <xref:host-and-deploy/proxy-load-balancer>.

### `X-http-method-override`, `x-http-method`, `x-method-override`

Some clients and servers limit which HTTP methods they allow (for example, GET). These request headers are sometimes used to work around those restrictions. These headers are proxied by default. If in the proxy you want to prevent these bypasses then use the [RequestHeaderRemove](xref:fundamentals/servers/yarp/transforms#requestheaderremove) transform.

### `Set-Cookie`

This response header may contain fields that constrain aspects of the URL, such as the scheme, domain, or path, where the cookie should be used. Using a reverse proxy may change the effective scheme, domain, or path of a site from the public view. While it would be possible to [rewrite response cookies using custom transforms](https://github.com/microsoft/reverse-proxy/issues/1109), we recommended instead to use the `Forwarded` headers described earlier to flow the correct values to the destination app so it can generate the correct `set-cookie` headers.

### `Location`

This response header is used with redirects and may contain a scheme, domain, and path that differ from the public values due to the use of the proxy. While it would be possible to [rewrite](https://github.com/microsoft/reverse-proxy/discussions/466) the Location header using custom transforms, it's recommended instead to use the Forwarded headers described above to flow the correct values to the destination app so it can generate the correct Location headers. 

### `Server`

This response header indicates what server technology was used to generate the response (for example, IIS, Kestrel). This header is proxied from the destination by default. Applications that want to remove it can use the [ResponseHeaderRemove](xref:fundamentals/servers/yarp/transforms#responseheaderremove) transform, in which case the proxy's default server header will be used. Suppressing the proxy default server header is server specific, such as for [Kestrel](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.addserverheader#Microsoft_AspNetCore_Server_Kestrel_Core_KestrelServerOptions_AddServerHeader).

### `X-Powered-By`

This response header indicates what web framework was used to generate the response (for example, ASP.NET). ASP.NET Core does not generate this header but IIS can. This header is proxied from the destination by default. Applications that want to remove it can use the [ResponseHeaderRemove](xref:fundamentals/servers/yarp/transforms#responseheaderremove) transform.
