---
title: Použití šablony projektu React-with-Redux s ASP.NET Core
author: SteveSandersonMS
description: Přečtěte si, jak začít s ASP.NET základní jednostránkovou aplikaci (SPA) pro react s Redux a vytvořit aplikaci pro vytvoření a reakci.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/react-with-redux
ms.openlocfilehash: ed2e9aea449ddb09fef049a391f40f57452786a8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657638"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="46483-103">Použití šablony projektu React-with-Redux s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46483-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="46483-104">Aktualizovaná šablona projektu React-with-Redux poskytuje pohodlný výchozí bod pro aplikace ASP.NET Core pomocí konvencí React, Redux a [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) k implementaci bohatého uživatelského rozhraní na straně klienta( UI).</span><span class="sxs-lookup"><span data-stu-id="46483-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="46483-105">S výjimkou příkazu pro vytvoření projektu jsou všechny informace o šabloně React-with-Redux stejné jako u šablony React.</span><span class="sxs-lookup"><span data-stu-id="46483-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="46483-106">Chcete-li vytvořit tento `dotnet new reactredux` typ `dotnet new react`projektu, spusťte místo .</span><span class="sxs-lookup"><span data-stu-id="46483-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="46483-107">Další informace o funkcích, které jsou společné pro obě šablony založené na reactu, naleznete v [dokumentaci k šabloně React](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="46483-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="46483-108">Informace o konfiguraci dílčí aplikace React-with-Redux ve službě IIS naleznete v části [ReactRedux Template 2.1: Nelze &num;použít spa ve službě IIS (aspnet/Templating 555).](https://github.com/aspnet/Templating/issues/555)</span><span class="sxs-lookup"><span data-stu-id="46483-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
