---
title: Externí zprostředkovatelé ověřování OAuth
author: rick-anderson
description: Objevte externí poskytovatele ověřování OAuth, kteří pracují s aplikacemi ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: 438b06dfa55a6b5bdd7b97516005e1f918f7b6ae
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406066"
---
# <a name="external-oauth-authentication-providers"></a>Externí zprostředkovatelé ověřování OAuth

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi předvádí](https://github.com/rustd)a [Valeriy Novytskyy](https://github.com/01binary)

Následující seznam obsahuje běžné externí poskytovatele ověřování OAuth, kteří pracují s ASP.NET Core aplikacemi. Balíčky NuGet třetích stran, například ty, které spravuje [ASPNET-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), je možné použít k doplnění zprostředkovatelů ověřování, které implementuje tým ASP.NET Core.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([pokyny](https://developer.linkedin.com/docs/oauth2))

* [Instagramu](https://www.instagram.com/developer/register/) ([pokyny](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([pokyny](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([pokyny](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([pokyny](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([pokyny](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterestu](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([pokyny](https://developers.pinterest.com/docs/api/overview/?))

* [Kapesní](https://getpocket.com/developer/apps/new) ([pokyny](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([pokyny](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribble](https://dribbble.com/signup) ([pokyny](https://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([pokyny](https://developer.vimeo.com/api/authentication))

* [SoundCloud](https://soundcloud.com/you/apps/new) ([pokyny](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([pokyny](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
