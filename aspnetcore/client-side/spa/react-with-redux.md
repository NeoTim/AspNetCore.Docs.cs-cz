---
title: Šablona projektu React s Redux pomocí ASP.NET Core
author: SteveSandersonMS
description: Zjistěte, jak začít pracovat s ASP.NET Core jedné stránky aplikace (SPA) šablona projektu pro React s Reduxem a vytvořit aplikaci react.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/react-with-redux
ms.openlocfilehash: c1aedcf1e14a9e7b339b60dd02c4267cd5945a49
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341605"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="bd575-103">Šablona projektu React s Redux pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bd575-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> <span data-ttu-id="bd575-104">Tato dokumentace nemá týkají React s Redux šablony projektu, které jsou součástí ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="bd575-104">This documentation doesn't pertain to the React-with-Redux project template included in ASP.NET Core 2.0.</span></span> <span data-ttu-id="bd575-105">To se vztahují na novější React s Redux šablonu, kterou můžete aktualizovat ručně.</span><span class="sxs-lookup"><span data-stu-id="bd575-105">It pertains to the newer React-with-Redux template that you can update manually.</span></span> <span data-ttu-id="bd575-106">Šablona je k dispozici v ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="bd575-106">The template is available in ASP.NET Core 2.1 or later.</span></span>

::: moniker-end

<span data-ttu-id="bd575-107">Aktualizovaná šablona projektu React s Reduxem poskytuje příhodný výchozí bod pro aplikace ASP.NET Core pomocí React, Reduxem, a [vytvořit aplikaci react](https://github.com/facebookincubator/create-react-app) konvence (CRA) k implementaci bohatě vybaveným a na straně klienta uživatelské rozhraní (UI).</span><span class="sxs-lookup"><span data-stu-id="bd575-107">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="bd575-108">S výjimkou příkazu pro vytvoření projektu všechny informace o šabloně React s Redux je stejný jako šablona React.</span><span class="sxs-lookup"><span data-stu-id="bd575-108">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="bd575-109">Chcete-li vytvořit tento typ projektu, spusťte `dotnet new reactredux` místo `dotnet new react`.</span><span class="sxs-lookup"><span data-stu-id="bd575-109">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="bd575-110">Další informace o funkcích, které jsou společné pro obě šablony založené na React najdete v tématu [React dokumentaci k šabloně](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="bd575-110">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="bd575-111">Informace o konfiguraci React s Redux dílčí aplikace ve službě IIS najdete v tématu [2.1 šablony ReactRedux: Nelze použít jednostránková aplikace ve službě IIS (aspnet/Šablonování &num;555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="bd575-111">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
