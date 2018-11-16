---
title: Externí zprostředkovatelé ověřování OAuth
author: rick-anderson
description: Objevte zprostředkovatele ověřování externí OAuth, které pracují s aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/otherlogins
ms.openlocfilehash: b69c366ec1bf12ccf434991fc8a79eaf8c09da3d
ms.sourcegitcommit: 09bcda59a58019fdf47b2db5259fe87acf19dd38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708462"
---
# <a name="external-oauth-authentication-providers"></a>Externí zprostředkovatelé ověřování OAuth

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), a [Valeriy Novytskyy](https://github.com/01binary)

Následující seznam obsahuje společné externího zprostředkovatele ověřování OAuth, které pracují s aplikací ASP.NET Core. Balíčky NuGet třetích stran, jako jsou ty udržuje [aspnet contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), můžete použít jako doplněk zprostředkovatele ověřování, které jsou implementované tým ASP.NET Core.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([pokyny](https://developer.linkedin.com/docs/oauth2))

* [Instagram](https://www.instagram.com/developer/register/) ([pokyny](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([pokyny](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([pokyny](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([pokyny](https://developer.yahoo.com/bbauth/user.html))

* [Tumblr](https://www.tumblr.com/oauth/apps) ([pokyny](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterestu](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([pokyny](https://developers.pinterest.com/docs/api/overview/?))

* [Pocket](https://getpocket.com/developer/apps/new) ([pokyny](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([pokyny](https://www.flickr.com/services/api/auth.oauth.html))

* [Dribble](https://dribbble.com/signup) ([pokyny](http://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([pokyny](https://developer.vimeo.com/api/authentication))

* [SoundCloud](https://soundcloud.com/you/apps/new) ([pokyny](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([pokyny](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
