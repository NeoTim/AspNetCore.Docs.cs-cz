---
title: Použijte šablonu projektu reagující na Redux s ASP.NET Core
author: SteveSandersonMS
description: Naučte se, jak začít pracovat s ASP.NET Core šablonou projektu s jednou stránkou (SPA) a reagovat s Redux a vytvořit reakci-aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
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
uid: spa/react-with-redux
ms.openlocfilehash: 96ee4a06360d6cca8d48c300ecebab9014da9c56
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013135"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a>Použijte šablonu projektu reagující na Redux s ASP.NET Core

Aktualizovaná šablona projektu reagující na Redux poskytuje pohodlný výchozí bod pro ASP.NET Core aplikace pomocí konvencí reagující, Redux a CRA ( [Create-reagující na aplikace](https://github.com/facebookincubator/create-react-app) ) k implementaci bohatě náročného uživatelského rozhraní (UI) na straně klienta.

S výjimkou příkazu pro vytvoření projektu jsou všechny informace o šabloně reagující na Redux stejné jako v šabloně reakce. Chcete-li vytvořit tento typ projektu, spusťte `dotnet new reactredux` místo `dotnet new react` . Další informace o funkcích, které jsou společné pro šablony založené na reakci, najdete v tématu [reakce na šablonu v dokumentaci](xref:spa/react).

Informace o konfiguraci podaplikace s příReduxou v rámci služby IIS najdete v tématu [ReactRedux Template 2,1: ve službě IIS (ASPNET/šablonování &num; 555) nelze použít Spa](https://github.com/aspnet/Templating/issues/555).
