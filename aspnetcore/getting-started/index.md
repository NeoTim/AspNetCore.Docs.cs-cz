---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytváří a spouští základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 86a0c8d017138a949fddc0356f3de548d368a4c0
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417601"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="3ae57-103">Výuka: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ae57-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="3ae57-104">Tento kurz ukazuje, jak vytvořit a spustit ASP.NET webovou aplikaci Core pomocí rozhraní .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="3ae57-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="3ae57-105">Dozvíte se, jak provést tyto akce:</span><span class="sxs-lookup"><span data-stu-id="3ae57-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3ae57-106">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="3ae57-106">Create a web app project.</span></span>
> * <span data-ttu-id="3ae57-107">Důvěřujte vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="3ae57-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="3ae57-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3ae57-108">Run the app.</span></span>
> * <span data-ttu-id="3ae57-109">Upravte stránku Razor.</span><span class="sxs-lookup"><span data-stu-id="3ae57-109">Edit a Razor page.</span></span>

<span data-ttu-id="3ae57-110">Na konci budete mít funkční webovou aplikaci spuštěnou v místním počítači.</span><span class="sxs-lookup"><span data-stu-id="3ae57-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka webové aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="3ae57-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3ae57-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="3ae57-113">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="3ae57-113">Create a web app project</span></span>

<span data-ttu-id="3ae57-114">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3ae57-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="3ae57-115">Předchozí příkaz:</span><span class="sxs-lookup"><span data-stu-id="3ae57-115">The preceding command:</span></span>

* <span data-ttu-id="3ae57-116">Vytvoří novou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3ae57-116">Creates a new web app.</span></span>  
* <span data-ttu-id="3ae57-117">Parametr `-o aspnetcoreapp` vytvoří adresář s názvem *aspnetcoreapp* se zdrojovými soubory pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3ae57-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="3ae57-118">Důvěřovat certifikátu vývoje</span><span class="sxs-lookup"><span data-stu-id="3ae57-118">Trust the development certificate</span></span>

<span data-ttu-id="3ae57-119">Důvěřujte vývojovému certifikátu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3ae57-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="3ae57-120">Windows</span><span class="sxs-lookup"><span data-stu-id="3ae57-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="3ae57-121">Předchozí příkaz zobrazí následující dialog:</span><span class="sxs-lookup"><span data-stu-id="3ae57-121">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

<span data-ttu-id="3ae57-123">Pokud souhlasíte s tím, že certifikátu vývoje důvěřujete, vyberte **možnost Ano.**</span><span class="sxs-lookup"><span data-stu-id="3ae57-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="3ae57-124">macOS</span><span class="sxs-lookup"><span data-stu-id="3ae57-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="3ae57-125">Předchozí příkaz zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="3ae57-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="3ae57-126">*Bylo požadováno důvěřování vývojovému certifikátu HTTPS. Pokud certifikát ještě není důvěryhodný, spustíme následující příkaz:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="3ae57-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="3ae57-127">Tento příkaz vás může vyzvat k zadání hesla k instalaci certifikátu do systémové klíčenky.</span><span class="sxs-lookup"><span data-stu-id="3ae57-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="3ae57-128">Pokud souhlasíte s tím, že certifikátu vývoje důvěřujete, zadejte heslo.</span><span class="sxs-lookup"><span data-stu-id="3ae57-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="3ae57-129">Linux</span><span class="sxs-lookup"><span data-stu-id="3ae57-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="3ae57-130">Podívejte se na dokumentaci k distribuci Linuxu o tom, jak důvěřovat vývojovému certifikátu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3ae57-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="3ae57-131">Další informace naleznete [v tématu Trust ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="3ae57-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3ae57-132">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="3ae57-132">Run the app</span></span>

<span data-ttu-id="3ae57-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="3ae57-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="3ae57-134">Poté, co příkazové prostředí indikuje, že aplikace byla spuštěna, přejděte na `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3ae57-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="3ae57-135">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="3ae57-135">Edit a Razor page</span></span>

<span data-ttu-id="3ae57-136">Otevřete *stránky/index.cshtml* a upravte a uložte stránku s následujícími zvýrazněnými značkami:</span><span class="sxs-lookup"><span data-stu-id="3ae57-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="3ae57-137">Přejděte `https://localhost:5001`na , aktualizujte stránku a ověřte, zda jsou zobrazeny změny.</span><span class="sxs-lookup"><span data-stu-id="3ae57-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3ae57-138">Další kroky</span><span class="sxs-lookup"><span data-stu-id="3ae57-138">Next steps</span></span>

<span data-ttu-id="3ae57-139">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="3ae57-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3ae57-140">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="3ae57-140">Create a web app project.</span></span>
> * <span data-ttu-id="3ae57-141">Důvěřujte vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="3ae57-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="3ae57-142">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="3ae57-142">Run the project.</span></span>
> * <span data-ttu-id="3ae57-143">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="3ae57-143">Make a change.</span></span>

<span data-ttu-id="3ae57-144">Další informace o ASP.NET core najdete v doporučeném studijním programu v úvodu:</span><span class="sxs-lookup"><span data-stu-id="3ae57-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
