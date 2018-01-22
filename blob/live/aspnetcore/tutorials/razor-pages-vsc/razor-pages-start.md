---
title: "Začínáme s stránky Razor v ASP.NET Core s kódem jazyka Visual Studio"
author: rick-anderson
description: "Začínáme s stránky Razor v ASP.NET Core pomocí Visual Studio Code"
ms.author: riande
manager: wpickett
ms.date: 08/27/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages-vsc/razor-pages-start
ms.openlocfilehash: 727c3d5c8bed0aef3094816b7e5f0a940aea654c
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-code"></a><span data-ttu-id="2d093-103">Začínáme s stránky Razor v ASP.NET Core s kódem jazyka Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d093-103">Getting started with Razor Pages in ASP.NET Core with Visual Studio Code</span></span>

<span data-ttu-id="2d093-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2d093-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2d093-105">V tomto kurzu se dozvíte, jaké základní informace o vytváření webové aplikace ASP.NET Core Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="2d093-105">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="2d093-106">Doporučujeme, abyste dokončení [Úvod do stránky Razor](xref:mvc/razor-pages/index) před zahájením tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="2d093-106">We recommend you complete [Introduction to Razor Pages](xref:mvc/razor-pages/index) before starting this tutorial.</span></span> <span data-ttu-id="2d093-107">Stránky Razor je doporučeným způsobem, jak sestavit uživatelského rozhraní pro webové aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d093-107">Razor Pages is the recommended way to build UI for web applications in ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2d093-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2d093-108">Prerequisites</span></span>

<span data-ttu-id="2d093-109">Nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="2d093-109">Install the following:</span></span>

* <span data-ttu-id="2d093-110">[.NET core 2.0.0 SDK](https://www.microsoft.com/net/core) nebo novější</span><span class="sxs-lookup"><span data-stu-id="2d093-110">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later</span></span>
* [<span data-ttu-id="2d093-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2d093-111">Visual Studio Code</span></span>](https://code.visualstudio.com)
* <span data-ttu-id="2d093-112">VS Code [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span><span class="sxs-lookup"><span data-stu-id="2d093-112">VS Code [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span></span> 

## <a name="create-a-razor-web-app"></a><span data-ttu-id="2d093-113">Vytvoření webové aplikace Razor</span><span class="sxs-lookup"><span data-stu-id="2d093-113">Create a Razor web app</span></span>

<span data-ttu-id="2d093-114">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2d093-114">From a terminal, run the following commands:</span></span>

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

<span data-ttu-id="2d093-115">Předchozí příkazy použijte [.NET Core rozhraní příkazového řádku](https://docs.microsoft.com/dotnet/core/tools/dotnet) k vytvoření a spuštění projektu stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="2d093-115">The preceding commands use the [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet) to create and run a Razor Pages project.</span></span> <span data-ttu-id="2d093-116">Otevřete v prohlížeči na http://localhost: 5000 zobrazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d093-116">Open a browser to http://localhost:5000 to view the application.</span></span>

![Index nebo Domovská stránka](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a><span data-ttu-id="2d093-118">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="2d093-118">Open the project</span></span>

<span data-ttu-id="2d093-119">Stiskněte kombinaci kláves Ctrl + C vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d093-119">Press Ctrl+C to shut down the application.</span></span>

<span data-ttu-id="2d093-120">Visual Studio Code (kód VS), vyberte **soubor > Otevřít složku**a pak vyberte *RazorPagesMovie* složky.</span><span class="sxs-lookup"><span data-stu-id="2d093-120">From Visual Studio Code (VS Code), select **File > Open Folder**, and then select the *RazorPagesMovie* folder.</span></span>

- <span data-ttu-id="2d093-121">Vyberte **Ano** k **varování** zpráva "požadované prostředky pro sestavení a ladění chybí 'RazorPagesMovie'.</span><span class="sxs-lookup"><span data-stu-id="2d093-121">Select **Yes** to the **Warn** message "Required assets to build and debug are missing from 'RazorPagesMovie'.</span></span> <span data-ttu-id="2d093-122">Přidat jejich?"</span><span class="sxs-lookup"><span data-stu-id="2d093-122">Add them?"</span></span>
- <span data-ttu-id="2d093-123">Vyberte **obnovení** k **informace** zpráva "Neexistují nevyřešené závislosti".</span><span class="sxs-lookup"><span data-stu-id="2d093-123">Select **Restore** to the **Info** message "There are unresolved dependencies".</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="2d093-124">Spusťte aplikaci</span><span class="sxs-lookup"><span data-stu-id="2d093-124">Launch the app</span></span>

<span data-ttu-id="2d093-125">Stisknutím kombinace kláves Ctrl + F5 a spusťte aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="2d093-125">Press Ctrl+F5 to start the app without debugging.</span></span> <span data-ttu-id="2d093-126">Můžete také z **ladění** nabídce vyberte možnost **spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="2d093-126">Alternatively, from the **Debug** menu, select **Start Without Debugging**.</span></span>

<span data-ttu-id="2d093-127">V dalším kurzu přidáme modelu do projektu.</span><span class="sxs-lookup"><span data-stu-id="2d093-127">In the next tutorial, we add a model to the project.</span></span> 

>[!div class="step-by-step"]
[<span data-ttu-id="2d093-128">Další krok: Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="2d093-128">Next: Adding a model</span></span>](xref:tutorials/razor-pages-vsc/model)  
