---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: c9cd5e05f52c8bdefa931adc654087dac91e2f05
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317754"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="cfbf6-103">Kurz: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cfbf6-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="cfbf6-104">V tomto kurzu se dozvíte, jak používat rozhraní příkazového řádku .NET Core k vytvoření a spuštění webové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="cfbf6-105">Naučíte se:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cfbf6-106">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-106">Create a web app project.</span></span>
> * <span data-ttu-id="cfbf6-107">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="cfbf6-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-108">Run the app.</span></span>
> * <span data-ttu-id="cfbf6-109">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="cfbf6-109">Edit a Razor page.</span></span>

<span data-ttu-id="cfbf6-110">Na konci budete mít na svém místním počítači spuštěnou funkční webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka webové aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="cfbf6-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="cfbf6-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="cfbf6-113">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="cfbf6-113">Create a web app project</span></span>

<span data-ttu-id="cfbf6-114">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="cfbf6-115">Předchozí příkaz:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-115">The preceding command:</span></span>

* <span data-ttu-id="cfbf6-116">Vytvoří novou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-116">Creates a new web app.</span></span>  
* <span data-ttu-id="cfbf6-117">Parametr vytvoří adresář s názvem aspnetcoreapp se zdrojovými soubory aplikace. `-o`</span><span class="sxs-lookup"><span data-stu-id="cfbf6-117">The `-o` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="cfbf6-118">Důvěřovat vývojovému certifikátu</span><span class="sxs-lookup"><span data-stu-id="cfbf6-118">Trust the development certificate</span></span>

<span data-ttu-id="cfbf6-119">Důvěřovat vývojovému certifikátu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-119">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="cfbf6-120">Windows</span><span class="sxs-lookup"><span data-stu-id="cfbf6-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="cfbf6-121">Předchozí příkaz zobrazí následující dialog:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-121">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

<span data-ttu-id="cfbf6-123">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .</span><span class="sxs-lookup"><span data-stu-id="cfbf6-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="cfbf6-124">macOS</span><span class="sxs-lookup"><span data-stu-id="cfbf6-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="cfbf6-125">Předchozí příkaz zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="cfbf6-126">*Byl požadován vztah důvěryhodnosti s vývojovým certifikátem HTTPS. Pokud certifikát ještě nedůvěřuje, spustíme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="cfbf6-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="cfbf6-127">Tento příkaz vás může vyzvat k zadání hesla pro instalaci certifikátu do systémového řetězce klíčů.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="cfbf6-128">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, zadejte své heslo.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="cfbf6-129">Linux</span><span class="sxs-lookup"><span data-stu-id="cfbf6-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="cfbf6-130">Informace o subsystému Windows pro Linux najdete v tématu [Trust certifikát HTTPS v subsystému Windows pro Linux](xref:security/enforcing-ssl#wsl).</span><span class="sxs-lookup"><span data-stu-id="cfbf6-130">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="cfbf6-131">Informace o tom, jak důvěřovat certifikátu vývoje HTTPS, najdete v dokumentaci k distribuci systému Linux.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-131">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="cfbf6-132">Další informace najdete v tématu [důvěryhodnost ASP.NET Core certifikát pro vývoj https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="cfbf6-132">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="cfbf6-133">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="cfbf6-133">Run the app</span></span>

<span data-ttu-id="cfbf6-134">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-134">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="cfbf6-135">Jakmile příkazové prostředí ukáže, že aplikace začala, přejděte na [https://localhost:5001](https://localhost:5001)adresu.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-135">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="cfbf6-136">Kliknutím na **přijmout** přijměte zásady ochrany osobních údajů a souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-136">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="cfbf6-137">Tato aplikace nemá uchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-137">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="cfbf6-138">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="cfbf6-138">Edit a Razor page</span></span>

<span data-ttu-id="cfbf6-139">Otevřete *pages/index. cshtml* a upravte a uložte stránku s následujícím zvýrazněným označením:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-139">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="cfbf6-140">[https://localhost:5001](https://localhost:5001)Vyhledejte, aktualizujte stránku a ověřte, zda jsou zobrazeny změny.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-140">Browse to [https://localhost:5001](https://localhost:5001), refresh the page and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cfbf6-141">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cfbf6-141">Next steps</span></span>

<span data-ttu-id="cfbf6-142">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-142">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cfbf6-143">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-143">Create a web app project.</span></span>
> * <span data-ttu-id="cfbf6-144">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-144">Trust the development certificate.</span></span>
> * <span data-ttu-id="cfbf6-145">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-145">Run the project.</span></span>
> * <span data-ttu-id="cfbf6-146">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="cfbf6-146">Make a change.</span></span>

<span data-ttu-id="cfbf6-147">Další informace o ASP.NET Core najdete v tématu doporučený postup výuky v úvodu:</span><span class="sxs-lookup"><span data-stu-id="cfbf6-147">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
