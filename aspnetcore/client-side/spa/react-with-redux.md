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
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="4e34a-103">Použijte šablonu projektu reagující na Redux s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e34a-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="4e34a-104">Aktualizovaná šablona projektu reagující na Redux poskytuje pohodlný výchozí bod pro ASP.NET Core aplikace pomocí konvencí reagující, Redux a CRA ( [Create-reagující na aplikace](https://github.com/facebookincubator/create-react-app) ) k implementaci bohatě náročného uživatelského rozhraní (UI) na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="4e34a-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="4e34a-105">S výjimkou příkazu pro vytvoření projektu jsou všechny informace o šabloně reagující na Redux stejné jako v šabloně reakce.</span><span class="sxs-lookup"><span data-stu-id="4e34a-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="4e34a-106">Chcete-li vytvořit tento typ projektu, spusťte `dotnet new reactredux` místo `dotnet new react` .</span><span class="sxs-lookup"><span data-stu-id="4e34a-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="4e34a-107">Další informace o funkcích, které jsou společné pro šablony založené na reakci, najdete v tématu [reakce na šablonu v dokumentaci](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="4e34a-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="4e34a-108">Informace o konfiguraci podaplikace s příReduxou v rámci služby IIS najdete v tématu [ReactRedux Template 2,1: ve službě IIS (ASPNET/šablonování &num; 555) nelze použít Spa](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="4e34a-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
