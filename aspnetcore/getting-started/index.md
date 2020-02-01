---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 4f7e67e1e422afe3f7e2970e0c40380f065390ac
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928319"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="18860-103">Kurz: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18860-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="18860-104">V tomto kurzu se dozvíte, jak pomocí .NET Core CLI vytvořit a spustit webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18860-104">This tutorial shows how to use the .NET Core CLI to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="18860-105">Naučíte se:</span><span class="sxs-lookup"><span data-stu-id="18860-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="18860-106">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="18860-106">Create a web app project.</span></span>
> * <span data-ttu-id="18860-107">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="18860-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="18860-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="18860-108">Run the app.</span></span>
> * <span data-ttu-id="18860-109">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="18860-109">Edit a Razor page.</span></span>

<span data-ttu-id="18860-110">Na konci budete mít na svém místním počítači spuštěnou funkční webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="18860-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka webové aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="18860-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="18860-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="18860-113">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="18860-113">Create a web app project</span></span>

<span data-ttu-id="18860-114">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="18860-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="18860-115">Předchozí příkaz:</span><span class="sxs-lookup"><span data-stu-id="18860-115">The preceding command:</span></span>

* <span data-ttu-id="18860-116">Vytvoří novou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="18860-116">Creates a new web app.</span></span>  
* <span data-ttu-id="18860-117">Parametr `-o aspnetcoreapp` vytvoří adresář s názvem *aspnetcoreapp* se zdrojovými soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="18860-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="18860-118">Důvěřovat vývojovému certifikátu</span><span class="sxs-lookup"><span data-stu-id="18860-118">Trust the development certificate</span></span>

<span data-ttu-id="18860-119">Důvěřovat vývojovému certifikátu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="18860-119">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="18860-120">Windows</span><span class="sxs-lookup"><span data-stu-id="18860-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="18860-121">Předchozí příkaz zobrazí následující dialog:</span><span class="sxs-lookup"><span data-stu-id="18860-121">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

<span data-ttu-id="18860-123">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .</span><span class="sxs-lookup"><span data-stu-id="18860-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="18860-124">macOS</span><span class="sxs-lookup"><span data-stu-id="18860-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="18860-125">Předchozí příkaz zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="18860-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="18860-126">*Byl požadován vztah důvěryhodnosti s vývojovým certifikátem https. Pokud certifikát ještě není důvěryhodný, spustíme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="18860-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="18860-127">Tento příkaz vás může vyzvat k zadání hesla pro instalaci certifikátu do systémového řetězce klíčů.</span><span class="sxs-lookup"><span data-stu-id="18860-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="18860-128">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, zadejte své heslo.</span><span class="sxs-lookup"><span data-stu-id="18860-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="18860-129">Linux</span><span class="sxs-lookup"><span data-stu-id="18860-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="18860-130">Informace o tom, jak důvěřovat certifikátu vývoje HTTPS, najdete v dokumentaci k distribuci systému Linux.</span><span class="sxs-lookup"><span data-stu-id="18860-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="18860-131">Další informace najdete v tématu [důvěryhodnost ASP.NET Core certifikát pro vývoj https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="18860-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="18860-132">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="18860-132">Run the app</span></span>

<span data-ttu-id="18860-133">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="18860-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="18860-134">Jakmile se v příkazovém prostředí ukáže, že je aplikace spuštěná, přejděte do [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="18860-134">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="18860-135">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="18860-135">Edit a Razor page</span></span>

<span data-ttu-id="18860-136">Otevřete *pages/index. cshtml* a upravte a uložte stránku s následujícím zvýrazněným označením:</span><span class="sxs-lookup"><span data-stu-id="18860-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="18860-137">Vyhledejte [https://localhost:5001](https://localhost:5001), aktualizujte stránku a ověřte, zda jsou změny zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="18860-137">Browse to [https://localhost:5001](https://localhost:5001), refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="18860-138">Další kroky</span><span class="sxs-lookup"><span data-stu-id="18860-138">Next steps</span></span>

<span data-ttu-id="18860-139">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="18860-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="18860-140">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="18860-140">Create a web app project.</span></span>
> * <span data-ttu-id="18860-141">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="18860-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="18860-142">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="18860-142">Run the project.</span></span>
> * <span data-ttu-id="18860-143">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="18860-143">Make a change.</span></span>

<span data-ttu-id="18860-144">Další informace o ASP.NET Core najdete v tématu doporučený postup výuky v úvodu:</span><span class="sxs-lookup"><span data-stu-id="18860-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
