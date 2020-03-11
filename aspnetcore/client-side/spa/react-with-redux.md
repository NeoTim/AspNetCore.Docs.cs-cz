---
title: Použijte šablonu projektu reagující na Redux s ASP.NET Core
author: SteveSandersonMS
description: Naučte se, jak začít pracovat s ASP.NET Core šablonou projektu s jednou stránkou (SPA) a reagovat s Redux a vytvořit reakci-aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/react-with-redux
ms.openlocfilehash: ed2e9aea449ddb09fef049a391f40f57452786a8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657638"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="f0269-103">Použijte šablonu projektu reagující na Redux s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0269-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="f0269-104">Aktualizovaná šablona projektu reagující na Redux poskytuje pohodlný výchozí bod pro ASP.NET Core aplikace pomocí konvencí reagující, Redux a CRA ( [Create-reagující na aplikace](https://github.com/facebookincubator/create-react-app) ) k implementaci bohatě náročného uživatelského rozhraní (UI) na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f0269-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="f0269-105">S výjimkou příkazu pro vytvoření projektu jsou všechny informace o šabloně reagující na Redux stejné jako v šabloně reakce.</span><span class="sxs-lookup"><span data-stu-id="f0269-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="f0269-106">Chcete-li vytvořit tento typ projektu, spusťte `dotnet new reactredux` místo `dotnet new react`.</span><span class="sxs-lookup"><span data-stu-id="f0269-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="f0269-107">Další informace o funkcích, které jsou společné pro šablony založené na reakci, najdete v tématu [reakce na šablonu v dokumentaci](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="f0269-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="f0269-108">Informace o konfiguraci podaplikace s příReduxm ve službě IIS najdete v tématu [ReactRedux Template 2,1: ve službě IIS nelze použít Spa (ASPNET/šablonování &num;555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="f0269-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
