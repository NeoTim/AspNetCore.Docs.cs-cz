---
title: Externí zprostředkovatelé ověřování OAuth
author: rick-anderson
description: Objevte externí poskytovatele ověřování OAuth, kteří pracují s aplikacemi ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: 3de76b303c469341ba022fa3d33d3cd2a17581db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014903"
---
# <a name="external-oauth-authentication-providers"></a><span data-ttu-id="227c7-103">Externí zprostředkovatelé ověřování OAuth</span><span class="sxs-lookup"><span data-stu-id="227c7-103">External OAuth authentication providers</span></span>

<span data-ttu-id="227c7-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi předvádí](https://github.com/rustd)a [Valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="227c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="227c7-105">Následující seznam obsahuje běžné externí poskytovatele ověřování OAuth, kteří pracují s ASP.NET Core aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="227c7-105">The following list includes common external OAuth authentication providers that work with ASP.NET Core apps.</span></span> <span data-ttu-id="227c7-106">Balíčky NuGet třetích stran, například ty, které spravuje [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), je možné použít k doplnění zprostředkovatelů ověřování, které implementuje tým ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="227c7-106">Third-party NuGet packages, such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), can be used to complement the authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="227c7-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([pokyny](https://developer.linkedin.com/docs/oauth2))</span><span class="sxs-lookup"><span data-stu-id="227c7-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))</span></span>

* <span data-ttu-id="227c7-108">[Instagramu](https://www.instagram.com/developer/register/) ([pokyny](https://www.instagram.com/developer/authentication/))</span><span class="sxs-lookup"><span data-stu-id="227c7-108">[Instagram](https://www.instagram.com/developer/register/) ([Instructions](https://www.instagram.com/developer/authentication/))</span></span>

* <span data-ttu-id="227c7-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([pokyny](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span><span class="sxs-lookup"><span data-stu-id="227c7-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span></span>

* <span data-ttu-id="227c7-110">[GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([pokyny](https://developer.github.com/v3/oauth/))</span><span class="sxs-lookup"><span data-stu-id="227c7-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Instructions](https://developer.github.com/v3/oauth/))</span></span>

* <span data-ttu-id="227c7-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([pokyny](https://developer.yahoo.com/bbauth/user.html))</span><span class="sxs-lookup"><span data-stu-id="227c7-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Instructions](https://developer.yahoo.com/bbauth/user.html))</span></span>

* <span data-ttu-id="227c7-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([pokyny](https://www.tumblr.com/docs/api/v2#auth))</span><span class="sxs-lookup"><span data-stu-id="227c7-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Instructions](https://www.tumblr.com/docs/api/v2#auth))</span></span>

* <span data-ttu-id="227c7-113">[Pinterestu](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([pokyny](https://developers.pinterest.com/docs/api/overview/?))</span><span class="sxs-lookup"><span data-stu-id="227c7-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Instructions](https://developers.pinterest.com/docs/api/overview/?))</span></span>

* <span data-ttu-id="227c7-114">[Kapesní](https://getpocket.com/developer/apps/new) ([pokyny](https://getpocket.com/developer/docs/authentication))</span><span class="sxs-lookup"><span data-stu-id="227c7-114">[Pocket](https://getpocket.com/developer/apps/new) ([Instructions](https://getpocket.com/developer/docs/authentication))</span></span>

* <span data-ttu-id="227c7-115">[Flickr](https://www.flickr.com/services/apps/create) ([pokyny](https://www.flickr.com/services/api/auth.oauth.html))</span><span class="sxs-lookup"><span data-stu-id="227c7-115">[Flickr](https://www.flickr.com/services/apps/create) ([Instructions](https://www.flickr.com/services/api/auth.oauth.html))</span></span>

* <span data-ttu-id="227c7-116">[Dribble](https://dribbble.com/signup) ([pokyny](https://developer.dribbble.com/v1/oauth/))</span><span class="sxs-lookup"><span data-stu-id="227c7-116">[Dribble](https://dribbble.com/signup) ([Instructions](https://developer.dribbble.com/v1/oauth/))</span></span>

* <span data-ttu-id="227c7-117">[Vimeo](https://vimeo.com/join) ([pokyny](https://developer.vimeo.com/api/authentication))</span><span class="sxs-lookup"><span data-stu-id="227c7-117">[Vimeo](https://vimeo.com/join) ([Instructions](https://developer.vimeo.com/api/authentication))</span></span>

* <span data-ttu-id="227c7-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([pokyny](https://developers.soundcloud.com/blog/we-love-oauth-2))</span><span class="sxs-lookup"><span data-stu-id="227c7-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))</span></span>

* <span data-ttu-id="227c7-119">[VK](https://vk.com/apps?act=manage) ([pokyny](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span><span class="sxs-lookup"><span data-stu-id="227c7-119">[VK](https://vk.com/apps?act=manage) ([Instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span></span>

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
