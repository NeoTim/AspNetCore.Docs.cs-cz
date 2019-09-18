---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/15/2019
uid: getting-started
ms.openlocfilehash: d1edf91f1b37ba2b69732471dc6c1f306ac5ad24
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081132"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="d70af-103">Kurz: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d70af-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="d70af-104">V tomto kurzu se dozvíte, jak používat rozhraní příkazového řádku .NET Core k vytvoření a spuštění webové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d70af-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="d70af-105">Naučíte se:</span><span class="sxs-lookup"><span data-stu-id="d70af-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d70af-106">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="d70af-106">Create a web app project.</span></span>
> * <span data-ttu-id="d70af-107">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d70af-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="d70af-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d70af-108">Run the app.</span></span>
> * <span data-ttu-id="d70af-109">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="d70af-109">Edit a Razor page.</span></span>

<span data-ttu-id="d70af-110">Na konci budete mít na svém místním počítači spuštěnou funkční webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d70af-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka webové aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="d70af-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d70af-112">Prerequisites</span></span>

* [<span data-ttu-id="d70af-113">Sada .NET Core 2,2 SDK</span><span class="sxs-lookup"><span data-stu-id="d70af-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="d70af-114">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="d70af-114">Create a web app project</span></span>

<span data-ttu-id="d70af-115">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d70af-115">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a><span data-ttu-id="d70af-116">Důvěřovat vývojovému certifikátu</span><span class="sxs-lookup"><span data-stu-id="d70af-116">Trust the development certificate</span></span>

<span data-ttu-id="d70af-117">Důvěřovat vývojovému certifikátu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="d70af-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="d70af-118">Windows</span><span class="sxs-lookup"><span data-stu-id="d70af-118">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="d70af-119">Předchozí příkaz zobrazí následující dialog:</span><span class="sxs-lookup"><span data-stu-id="d70af-119">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

<span data-ttu-id="d70af-121">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .</span><span class="sxs-lookup"><span data-stu-id="d70af-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="d70af-122">macOS</span><span class="sxs-lookup"><span data-stu-id="d70af-122">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="d70af-123">Předchozí příkaz zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="d70af-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="d70af-124">*Byl požadován vztah důvěryhodnosti s vývojovým certifikátem HTTPS. Pokud certifikát ještě nedůvěřuje, spustíme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="d70af-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="d70af-125">Tento příkaz vás může vyzvat k zadání hesla pro instalaci certifikátu do systémového řetězce klíčů.</span><span class="sxs-lookup"><span data-stu-id="d70af-125">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="d70af-126">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, zadejte své heslo.</span><span class="sxs-lookup"><span data-stu-id="d70af-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d70af-127">Linux</span><span class="sxs-lookup"><span data-stu-id="d70af-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="d70af-128">Informace o subsystému Windows pro Linux najdete v tématu [Trust certifikát HTTPS v subsystému Windows pro Linux](xref:security/enforcing-ssl#wsl).</span><span class="sxs-lookup"><span data-stu-id="d70af-128">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="d70af-129">Informace o tom, jak důvěřovat certifikátu vývoje HTTPS, najdete v dokumentaci k distribuci systému Linux.</span><span class="sxs-lookup"><span data-stu-id="d70af-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="d70af-130">Další informace najdete v tématu [důvěryhodnost ASP.NET Core certifikát pro vývoj https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="d70af-130">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="d70af-131">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="d70af-131">Run the app</span></span>

<span data-ttu-id="d70af-132">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d70af-132">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="d70af-133">Jakmile příkazové prostředí ukáže, že aplikace začala, přejděte na [https://localhost:5001](https://localhost:5001)adresu.</span><span class="sxs-lookup"><span data-stu-id="d70af-133">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="d70af-134">Kliknutím na **přijmout** přijměte zásady ochrany osobních údajů a souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="d70af-134">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="d70af-135">Tato aplikace nemá uchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="d70af-135">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="d70af-136">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="d70af-136">Edit a Razor page</span></span>

<span data-ttu-id="d70af-137">Otevřete *pages/index. cshtml* a upravte stránku pomocí následujícího zvýrazněného kódu:</span><span class="sxs-lookup"><span data-stu-id="d70af-137">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="d70af-138">Vyhledejte a ověřte, zda jsou změny zobrazeny. [https://localhost:5001](https://localhost:5001)</span><span class="sxs-lookup"><span data-stu-id="d70af-138">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d70af-139">Další postup</span><span class="sxs-lookup"><span data-stu-id="d70af-139">Next steps</span></span>

<span data-ttu-id="d70af-140">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="d70af-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d70af-141">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="d70af-141">Create a web app project.</span></span>
> * <span data-ttu-id="d70af-142">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d70af-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="d70af-143">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="d70af-143">Run the project.</span></span>
> * <span data-ttu-id="d70af-144">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="d70af-144">Make a change.</span></span>

<span data-ttu-id="d70af-145">Další informace o ASP.NET Core najdete v tématu doporučený postup výuky v úvodu:</span><span class="sxs-lookup"><span data-stu-id="d70af-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
