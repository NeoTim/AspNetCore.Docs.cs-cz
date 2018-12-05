---
title: Začínáme s ASP.NET Core MVC a sady Visual Studio pro Mac
author: rick-anderson
description: Zjistěte, jak začít pracovat s ASP.NET Core MVC a sady Visual Studio
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: tutorials/first-mvc-app-mac/start-mvc
ms.openlocfilehash: 1e3cc91003ac946ac2e5efcd79e20d9215ded902
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862223"
---
# <a name="get-started-with-aspnet-core-mvc-and-visual-studio-for-mac"></a><span data-ttu-id="3eb93-103">Začínáme s ASP.NET Core MVC a sady Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3eb93-103">Get started with ASP.NET Core MVC and Visual Studio for Mac</span></span>

<span data-ttu-id="3eb93-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3eb93-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3eb93-105">V tomto kurzu se naučíte se základy vytváření webové aplikace ASP.NET Core MVC pomocí [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/).</span><span class="sxs-lookup"><span data-stu-id="3eb93-105">This tutorial teaches you the basics of building an ASP.NET Core MVC web app using [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/).</span></span>

[!INCLUDE [consider RP](../../includes/razor.md)]

<span data-ttu-id="3eb93-106">Existují 3 verze tohoto kurzu:</span><span class="sxs-lookup"><span data-stu-id="3eb93-106">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="3eb93-107">macOS: [sestavení aplikace ASP.NET Core MVC v jazyce Visual Studio pro Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="3eb93-107">macOS: [Build an ASP.NET Core MVC app with Visual Studio for Mac](xref:tutorials/first-mvc-app-mac/start-mvc)</span></span>
* <span data-ttu-id="3eb93-108">Windows: [sestavit aplikaci MVC ASP.NET Core pomocí sady Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="3eb93-108">Windows: [Build an ASP.NET Core MVC app with Visual Studio](xref:tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="3eb93-109">Linux, macOS a Windows: [sestavení aplikace ASP.NET Core MVC v jazyce Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="3eb93-109">Linux, macOS, and Windows: [Build an ASP.NET Core MVC app with Visual Studio Code](xref:tutorials/first-mvc-app-xplat/start-mvc)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3eb93-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3eb93-110">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-prereqs-macos.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="3eb93-111">Vytvoření webové aplikace</span><span class="sxs-lookup"><span data-stu-id="3eb93-111">Create a web app</span></span>

<span data-ttu-id="3eb93-112">Ze sady Visual Studio, vyberte **souboru** > **nové řešení**.</span><span class="sxs-lookup"><span data-stu-id="3eb93-112">From Visual Studio, select **File** > **New Solution**.</span></span>

![macOS nové řešení](../first-web-api-mac/_static/sln.png)

<span data-ttu-id="3eb93-114">Vyberte **aplikace .NET Core** > **ASP.NET Core** > **webové aplikace ASP.NET Core (MVC)** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="3eb93-114">Select **.NET Core App** > **ASP.NET Core** > **ASP.NET Core Web App (MVC)** > **Next**.</span></span>

![macOS dialogové okno nového projektu](start-mvc/1.png)

<span data-ttu-id="3eb93-116">Pojmenujte projekt **MvcMovie**a pak vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3eb93-116">Name the project **MvcMovie**, and then select **Create**.</span></span>

![macOS dialogové okno nového projektu](start-mvc/2.png)

### <a name="launch-the-app"></a><span data-ttu-id="3eb93-118">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="3eb93-118">Launch the app</span></span>

<span data-ttu-id="3eb93-119">V sadě Visual Studio, vyberte **spustit** > **spustit bez ladění** aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="3eb93-119">In Visual Studio, select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="3eb93-120">Visual Studio spustí [Kestrel](xref:fundamentals/servers/index#kestrel) serveru, spustí se prohlížeč a přejde na `http://localhost:port`, kde *port* je číslo portu náhodně vybrané.</span><span class="sxs-lookup"><span data-stu-id="3eb93-120">Visual Studio starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

![Prohlížeč s nový projekt](start-mvc/b1.png)

* <span data-ttu-id="3eb93-122">Zobrazí se panel Adresa `localhost:port#` a nemít něco podobného `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3eb93-122">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3eb93-123">Důvodem je, že `localhost` je standardní název hostitele místního počítače.</span><span class="sxs-lookup"><span data-stu-id="3eb93-123">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3eb93-124">Když Visual Studio vytvoří webový projekt, náhodný port se používá pro webový server.</span><span class="sxs-lookup"><span data-stu-id="3eb93-124">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3eb93-125">Při spuštění aplikace se zobrazí jiné číslo portu.</span><span class="sxs-lookup"><span data-stu-id="3eb93-125">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="3eb93-126">Spustíte ji v ladění nebo v režimu bez ladění z **spustit** nabídky.</span><span class="sxs-lookup"><span data-stu-id="3eb93-126">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="3eb93-127">Výchozí šablony vám **Domů**, **o**, a **kontakt** odkazy.</span><span class="sxs-lookup"><span data-stu-id="3eb93-127">The default template gives you **Home**, **About**, and **Contact** links.</span></span> <span data-ttu-id="3eb93-128">Na předchozím obrázku prohlížeče nezobrazí těchto odkazů.</span><span class="sxs-lookup"><span data-stu-id="3eb93-128">The preceding browser image doesn't show these links.</span></span> <span data-ttu-id="3eb93-129">V závislosti na velikosti vašeho prohlížeče mohou muset kliknout na ikonu navigace se budou zobrazovat.</span><span class="sxs-lookup"><span data-stu-id="3eb93-129">Depending on the size of your browser, you might need to click the navigation icon to show them.</span></span>

![Prohlížeč s nový projekt](start-mvc/b2.png)

<span data-ttu-id="3eb93-131">V další části tohoto kurzu přečtěte si o MVC a začít psát kód.</span><span class="sxs-lookup"><span data-stu-id="3eb93-131">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3eb93-132">Next</span><span class="sxs-lookup"><span data-stu-id="3eb93-132">Next</span></span>](adding-controller.md)  
