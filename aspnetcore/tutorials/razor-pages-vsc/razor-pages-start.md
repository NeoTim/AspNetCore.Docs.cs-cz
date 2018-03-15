---
title: "Začínáme s ASP.NET Core Razor stránky v kódu Visual Studio"
author: rick-anderson
description: "Přečtěte si základní informace o vytváření webové aplikace ASP.NET Core Razor stránky s kódem jazyka Visual Studio."
manager: wpickett
ms.author: riande
ms.date: 08/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages-vsc/razor-pages-start
ms.openlocfilehash: ab14937508ad68f3ffc9c9ba797119b3c83fd743
ms.sourcegitcommit: 493a215355576cfa481773365de021bcf04bb9c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2018
---
# <a name="get-started-with-aspnet-core-razor-pages-in-visual-studio-code"></a><span data-ttu-id="d526a-103">Začínáme s ASP.NET Core Razor stránky v kódu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d526a-103">Get started with ASP.NET Core Razor Pages in Visual Studio Code</span></span>

<span data-ttu-id="d526a-104">podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d526a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d526a-105">V tomto kurzu se dozvíte, jaké základní informace o vytváření webové aplikace ASP.NET Core Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="d526a-105">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="d526a-106">Doporučujeme, abyste dokončení [Úvod do stránky Razor](xref:mvc/razor-pages/index) před zahájením tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="d526a-106">We recommend you complete [Introduction to Razor Pages](xref:mvc/razor-pages/index) before starting this tutorial.</span></span> <span data-ttu-id="d526a-107">Stránky Razor je doporučeným způsobem, jak sestavit uživatelského rozhraní pro webové aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d526a-107">Razor Pages is the recommended way to build UI for web applications in ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d526a-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d526a-108">Prerequisites</span></span>

<span data-ttu-id="d526a-109">Nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="d526a-109">Install the following:</span></span>

* <span data-ttu-id="d526a-110">[.NET core 2.0.0 SDK](https://www.microsoft.com/net/core) nebo novější</span><span class="sxs-lookup"><span data-stu-id="d526a-110">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later</span></span>
* [<span data-ttu-id="d526a-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d526a-111">Visual Studio Code</span></span>](https://code.visualstudio.com)
* <span data-ttu-id="d526a-112">VS Code [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span><span class="sxs-lookup"><span data-stu-id="d526a-112">VS Code [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)</span></span> 

## <a name="create-a-razor-web-app"></a><span data-ttu-id="d526a-113">Vytvoření webové aplikace Razor</span><span class="sxs-lookup"><span data-stu-id="d526a-113">Create a Razor web app</span></span>

<span data-ttu-id="d526a-114">Z terminálu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d526a-114">From a terminal, run the following commands:</span></span>

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

<span data-ttu-id="d526a-115">Předchozí příkazy použijte [.NET Core rozhraní příkazového řádku](https://docs.microsoft.com/dotnet/core/tools/dotnet) k vytvoření a spuštění projektu stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="d526a-115">The preceding commands use the [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet) to create and run a Razor Pages project.</span></span> <span data-ttu-id="d526a-116">Otevřete v prohlížeči na http://localhost: 5000 zobrazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d526a-116">Open a browser to http://localhost:5000 to view the application.</span></span>

![Index nebo Domovská stránka](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a><span data-ttu-id="d526a-118">Otevřete projekt</span><span class="sxs-lookup"><span data-stu-id="d526a-118">Open the project</span></span>

<span data-ttu-id="d526a-119">Stiskněte kombinaci kláves Ctrl + C vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d526a-119">Press Ctrl+C to shut down the application.</span></span>

<span data-ttu-id="d526a-120">Visual Studio Code (kód VS), vyberte **soubor > Otevřít složku**a pak vyberte *RazorPagesMovie* složky.</span><span class="sxs-lookup"><span data-stu-id="d526a-120">From Visual Studio Code (VS Code), select **File > Open Folder**, and then select the *RazorPagesMovie* folder.</span></span>

- <span data-ttu-id="d526a-121">Vyberte **Ano** k **varování** zpráva "požadované prostředky pro sestavení a ladění chybí 'RazorPagesMovie'.</span><span class="sxs-lookup"><span data-stu-id="d526a-121">Select **Yes** to the **Warn** message "Required assets to build and debug are missing from 'RazorPagesMovie'.</span></span> <span data-ttu-id="d526a-122">Přidat jejich?"</span><span class="sxs-lookup"><span data-stu-id="d526a-122">Add them?"</span></span>
- <span data-ttu-id="d526a-123">Vyberte **obnovení** k **informace** zpráva "Neexistují nevyřešené závislosti".</span><span class="sxs-lookup"><span data-stu-id="d526a-123">Select **Restore** to the **Info** message "There are unresolved dependencies".</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="d526a-124">Spusťte aplikaci</span><span class="sxs-lookup"><span data-stu-id="d526a-124">Launch the app</span></span>

<span data-ttu-id="d526a-125">Stisknutím kombinace kláves Ctrl + F5 a spusťte aplikaci bez ladění.</span><span class="sxs-lookup"><span data-stu-id="d526a-125">Press Ctrl+F5 to start the app without debugging.</span></span> <span data-ttu-id="d526a-126">Můžete také z **ladění** nabídce vyberte možnost **spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="d526a-126">Alternatively, from the **Debug** menu, select **Start Without Debugging**.</span></span>

<span data-ttu-id="d526a-127">V dalším kurzu přidáme modelu do projektu.</span><span class="sxs-lookup"><span data-stu-id="d526a-127">In the next tutorial, we add a model to the project.</span></span> 

>[!div class="step-by-step"]
[<span data-ttu-id="d526a-128">Další krok: Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="d526a-128">Next: Adding a model</span></span>](xref:tutorials/razor-pages-vsc/model)  
