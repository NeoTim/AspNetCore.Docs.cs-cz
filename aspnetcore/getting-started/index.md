---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: 116a22bce80257948bfcc02c03a74a4b5568b8b5
ms.sourcegitcommit: 4649814d1ae32248419da4e8f8242850fd8679a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975694"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="41212-103">Kurz: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41212-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="41212-104">V tomto kurzu se dozvíte, jak používat rozhraní příkazového řádku .NET Core k vytvoření a spuštění webové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41212-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="41212-105">Naučíte se:</span><span class="sxs-lookup"><span data-stu-id="41212-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="41212-106">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="41212-106">Create a web app project.</span></span>
> * <span data-ttu-id="41212-107">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="41212-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="41212-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41212-108">Run the app.</span></span>
> * <span data-ttu-id="41212-109">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="41212-109">Edit a Razor page.</span></span>

<span data-ttu-id="41212-110">Na konci budete mít na svém místním počítači spuštěnou funkční webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41212-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka webové aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="41212-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="41212-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="41212-113">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="41212-113">Create a web app project</span></span>

<span data-ttu-id="41212-114">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="41212-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="41212-115">Předchozí příkaz:</span><span class="sxs-lookup"><span data-stu-id="41212-115">The preceding command:</span></span>

* <span data-ttu-id="41212-116">Vytvoří novou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41212-116">Creates a new web app.</span></span>  
* <span data-ttu-id="41212-117">Parametr `-o aspnetcoreapp` vytvoří adresář s názvem *aspnetcoreapp* se zdrojovými soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="41212-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="41212-118">Důvěřovat vývojovému certifikátu</span><span class="sxs-lookup"><span data-stu-id="41212-118">Trust the development certificate</span></span>

<span data-ttu-id="41212-119">Důvěřovat vývojovému certifikátu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="41212-119">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="41212-120">Windows</span><span class="sxs-lookup"><span data-stu-id="41212-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="41212-121">Předchozí příkaz zobrazí následující dialog:</span><span class="sxs-lookup"><span data-stu-id="41212-121">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

<span data-ttu-id="41212-123">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .</span><span class="sxs-lookup"><span data-stu-id="41212-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="41212-124">macOS</span><span class="sxs-lookup"><span data-stu-id="41212-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="41212-125">Předchozí příkaz zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="41212-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="41212-126">*Byl požadován vztah důvěryhodnosti s vývojovým certifikátem https. Pokud certifikát ještě nedůvěřuje, spustíme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="41212-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="41212-127">Tento příkaz vás může vyzvat k zadání hesla pro instalaci certifikátu do systémového řetězce klíčů.</span><span class="sxs-lookup"><span data-stu-id="41212-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="41212-128">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, zadejte své heslo.</span><span class="sxs-lookup"><span data-stu-id="41212-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="41212-129">Linux</span><span class="sxs-lookup"><span data-stu-id="41212-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="41212-130">Informace o tom, jak důvěřovat certifikátu vývoje HTTPS, najdete v dokumentaci k distribuci systému Linux.</span><span class="sxs-lookup"><span data-stu-id="41212-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="41212-131">Další informace najdete v tématu [důvěryhodnost ASP.NET Core certifikát pro vývoj https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="41212-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="41212-132">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="41212-132">Run the app</span></span>

<span data-ttu-id="41212-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="41212-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="41212-134">Jakmile se v příkazovém prostředí ukáže, že je aplikace spuštěná, přejděte do [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="41212-134">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="41212-135">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="41212-135">Edit a Razor page</span></span>

<span data-ttu-id="41212-136">Otevřete *pages/index. cshtml* a upravte a uložte stránku s následujícím zvýrazněným označením:</span><span class="sxs-lookup"><span data-stu-id="41212-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="41212-137">Přejděte na [https://localhost:5001](https://localhost:5001), aktualizujte stránku a ověřte, zda jsou zobrazeny změny.</span><span class="sxs-lookup"><span data-stu-id="41212-137">Browse to [https://localhost:5001](https://localhost:5001), refresh the page and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="41212-138">Další kroky</span><span class="sxs-lookup"><span data-stu-id="41212-138">Next steps</span></span>

<span data-ttu-id="41212-139">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="41212-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="41212-140">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="41212-140">Create a web app project.</span></span>
> * <span data-ttu-id="41212-141">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="41212-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="41212-142">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="41212-142">Run the project.</span></span>
> * <span data-ttu-id="41212-143">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="41212-143">Make a change.</span></span>

<span data-ttu-id="41212-144">Další informace o ASP.NET Core najdete v tématu doporučený postup výuky v úvodu:</span><span class="sxs-lookup"><span data-stu-id="41212-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
