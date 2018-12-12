---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Rychlý kurz, který vytvoří a spustí jednoduchou aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2018
uid: getting-started
ms.openlocfilehash: cf9e731f7638687b3f40b42864ef7ee8f5522b39
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284347"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="a92ce-103">Kurz: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a92ce-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="a92ce-104">Tento návod ukazuje, jak vytvořit webovou aplikaci ASP.NET jádro pomocí rozhraní příkazového řádku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a92ce-104">This tutorial shows how to use the .NET Core command-line interface to create an ASP.NET Core web app.</span></span>

<span data-ttu-id="a92ce-105">Se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="a92ce-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a92ce-106">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a92ce-106">Create a web app project.</span></span>
> * <span data-ttu-id="a92ce-107">Povolte místní HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a92ce-107">Enable local HTTPS.</span></span>
> * <span data-ttu-id="a92ce-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a92ce-108">Run the app.</span></span>
> * <span data-ttu-id="a92ce-109">Úprava stránky syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="a92ce-109">Edit a Razor page.</span></span>

<span data-ttu-id="a92ce-110">Na konci máte spuštěn v místním počítači pomocí aplikace pracovní web app.</span><span class="sxs-lookup"><span data-stu-id="a92ce-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="a92ce-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a92ce-112">Prerequisites</span></span>

* [<span data-ttu-id="a92ce-113">.NET core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="a92ce-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="a92ce-114">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="a92ce-114">Create a web app project</span></span>

<span data-ttu-id="a92ce-115">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a92ce-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a><span data-ttu-id="a92ce-116">Povolit místní HTTPS</span><span class="sxs-lookup"><span data-stu-id="a92ce-116">Enable local HTTPS</span></span>

<span data-ttu-id="a92ce-117">Důvěřujete certifikátu vývoj HTTPS:</span><span class="sxs-lookup"><span data-stu-id="a92ce-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="a92ce-118">Windows</span><span class="sxs-lookup"><span data-stu-id="a92ce-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="a92ce-119">Ve výstupu předchozího příkazu se zobrazí následující dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="a92ce-119">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](_static/cert.png)

<span data-ttu-id="a92ce-121">Vyberte **Ano** Pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.</span><span class="sxs-lookup"><span data-stu-id="a92ce-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="a92ce-122">macOS</span><span class="sxs-lookup"><span data-stu-id="a92ce-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="a92ce-123">Ve výstupu předchozího příkazu se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="a92ce-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="a92ce-124">*Byla vyžádána důvěřující vývojářský certifikát HTTPS. Pokud certifikát není důvěryhodný provedeme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span><span class="sxs-lookup"><span data-stu-id="a92ce-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span></span>  
<span data-ttu-id="a92ce-125">\* Tento příkaz vás může vyzvat k zadání hesla k instalaci certifikátu v řetězci klíčů systému.</span><span class="sxs-lookup"><span data-stu-id="a92ce-125">\*This command might prompt you for your password to install the certificate on the system keychain.</span></span>

<span data-ttu-id="a92ce-126">Heslo: \*</span><span class="sxs-lookup"><span data-stu-id="a92ce-126">Password:\*</span></span>

<span data-ttu-id="a92ce-127">Zadejte svoje heslo, pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.</span><span class="sxs-lookup"><span data-stu-id="a92ce-127">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="a92ce-128">Linux</span><span class="sxs-lookup"><span data-stu-id="a92ce-128">Linux</span></span>](#tab/linux)

<span data-ttu-id="a92ce-129">O tom, jak důvěřovat certifikátu protokolu HTTPS vývoj naleznete v dokumentaci k vaší distribuci Linuxu.</span><span class="sxs-lookup"><span data-stu-id="a92ce-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

## <a name="run-the-app"></a><span data-ttu-id="a92ce-130">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a92ce-130">Run the app</span></span>

<span data-ttu-id="a92ce-131">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a92ce-131">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="a92ce-132">Přejděte do [ https://localhost:5001 ](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="a92ce-132">Browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="a92ce-133">Klikněte na tlačítko **přijmout** přijměte zásady ochrany osobních údajů a soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="a92ce-133">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="a92ce-134">Tato aplikace nemá uchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="a92ce-134">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="a92ce-135">Úprava stránky syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="a92ce-135">Edit a Razor page</span></span>

<span data-ttu-id="a92ce-136">Otevřít *Pages/Index.cshtml* a upravovat na stránce s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="a92ce-136">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="a92ce-137">Přejděte do [ https://localhost:5001 ](https://localhost:5001)a ověřte změny jsou zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="a92ce-137">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a92ce-138">Další kroky</span><span class="sxs-lookup"><span data-stu-id="a92ce-138">Next steps</span></span>

<span data-ttu-id="a92ce-139">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="a92ce-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a92ce-140">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a92ce-140">Create a web app project.</span></span>
> * <span data-ttu-id="a92ce-141">Povolte místní HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a92ce-141">Enable local HTTPS.</span></span>
> * <span data-ttu-id="a92ce-142">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="a92ce-142">Run the project.</span></span>
> * <span data-ttu-id="a92ce-143">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="a92ce-143">Make a change.</span></span>

<span data-ttu-id="a92ce-144">Další informace o základní technologie ASP.NET naleznete v tématu zavedení:</span><span class="sxs-lookup"><span data-stu-id="a92ce-144">To learn more about ASP.NET Core, see the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index>

> [!NOTE]
> <span data-ttu-id="a92ce-145">Testujeme použitelnost navrhovanou novou strukturu pro ASP.NET Core tabulku obsahu.</span><span class="sxs-lookup"><span data-stu-id="a92ce-145">We're testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span> <span data-ttu-id="a92ce-146">Pokud máte pár minut a zkuste cvičení sedm různých hledání témat v tabulce navrhované nebo aktuální obsah, [kliknutím sem se účastnit studie](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span><span class="sxs-lookup"><span data-stu-id="a92ce-146">If you have a few minutes to try an exercise of finding seven different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span></span>
