---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: 798f1ee87c05d886d8991e3f0230c8ebc6341ba8
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925103"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="07bf5-103">Kurz: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07bf5-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="07bf5-104">V tomto kurzu se dozvíte, jak používat rozhraní příkazového řádku .NET Core k vytvoření a spuštění webové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07bf5-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="07bf5-105">Naučíte se:</span><span class="sxs-lookup"><span data-stu-id="07bf5-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="07bf5-106">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="07bf5-106">Create a web app project.</span></span>
> * <span data-ttu-id="07bf5-107">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="07bf5-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="07bf5-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="07bf5-108">Run the app.</span></span>
> * <span data-ttu-id="07bf5-109">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="07bf5-109">Edit a Razor page.</span></span>

<span data-ttu-id="07bf5-110">Na konci budete mít na svém místním počítači spuštěnou funkční webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="07bf5-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka webové aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="07bf5-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="07bf5-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="07bf5-113">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="07bf5-113">Create a web app project</span></span>

<span data-ttu-id="07bf5-114">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="07bf5-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="07bf5-115">Předchozí příkaz:</span><span class="sxs-lookup"><span data-stu-id="07bf5-115">The preceding command:</span></span>

* <span data-ttu-id="07bf5-116">Vytvoří novou webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="07bf5-116">Creates a new web app.</span></span>  
* <span data-ttu-id="07bf5-117">Parametr vytvoří adresář s názvem aspnetcoreapp se zdrojovými soubory aplikace. `-o aspnetcoreapp`</span><span class="sxs-lookup"><span data-stu-id="07bf5-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="07bf5-118">Důvěřovat vývojovému certifikátu</span><span class="sxs-lookup"><span data-stu-id="07bf5-118">Trust the development certificate</span></span>

<span data-ttu-id="07bf5-119">Důvěřovat vývojovému certifikátu HTTPS:</span><span class="sxs-lookup"><span data-stu-id="07bf5-119">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="07bf5-120">Windows</span><span class="sxs-lookup"><span data-stu-id="07bf5-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="07bf5-121">Předchozí příkaz zobrazí následující dialog:</span><span class="sxs-lookup"><span data-stu-id="07bf5-121">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

<span data-ttu-id="07bf5-123">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, vyberte **Ano** .</span><span class="sxs-lookup"><span data-stu-id="07bf5-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="07bf5-124">macOS</span><span class="sxs-lookup"><span data-stu-id="07bf5-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="07bf5-125">Předchozí příkaz zobrazí následující zprávu:</span><span class="sxs-lookup"><span data-stu-id="07bf5-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="07bf5-126">*Byl požadován vztah důvěryhodnosti s vývojovým certifikátem HTTPS. Pokud certifikát ještě nedůvěřuje, spustíme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="07bf5-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="07bf5-127">Tento příkaz vás může vyzvat k zadání hesla pro instalaci certifikátu do systémového řetězce klíčů.</span><span class="sxs-lookup"><span data-stu-id="07bf5-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="07bf5-128">Pokud souhlasíte s tím, že certifikát pro vývoj důvěřujete, zadejte své heslo.</span><span class="sxs-lookup"><span data-stu-id="07bf5-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="07bf5-129">Linux</span><span class="sxs-lookup"><span data-stu-id="07bf5-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="07bf5-130">Informace o subsystému Windows pro Linux najdete v tématu [Trust certifikát HTTPS v subsystému Windows pro Linux](xref:security/enforcing-ssl#wsl).</span><span class="sxs-lookup"><span data-stu-id="07bf5-130">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="07bf5-131">Informace o tom, jak důvěřovat certifikátu vývoje HTTPS, najdete v dokumentaci k distribuci systému Linux.</span><span class="sxs-lookup"><span data-stu-id="07bf5-131">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="07bf5-132">Další informace najdete v tématu [důvěryhodnost ASP.NET Core certifikát pro vývoj https](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) .</span><span class="sxs-lookup"><span data-stu-id="07bf5-132">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="07bf5-133">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="07bf5-133">Run the app</span></span>

<span data-ttu-id="07bf5-134">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="07bf5-134">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="07bf5-135">Jakmile příkazové prostředí ukáže, že aplikace začala, přejděte na [https://localhost:5001](https://localhost:5001)adresu.</span><span class="sxs-lookup"><span data-stu-id="07bf5-135">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="07bf5-136">Úprava stránky Razor</span><span class="sxs-lookup"><span data-stu-id="07bf5-136">Edit a Razor page</span></span>

<span data-ttu-id="07bf5-137">Otevřete *pages/index. cshtml* a upravte a uložte stránku s následujícím zvýrazněným označením:</span><span class="sxs-lookup"><span data-stu-id="07bf5-137">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="07bf5-138">[https://localhost:5001](https://localhost:5001)Vyhledejte, aktualizujte stránku a ověřte, zda jsou zobrazeny změny.</span><span class="sxs-lookup"><span data-stu-id="07bf5-138">Browse to [https://localhost:5001](https://localhost:5001), refresh the page and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="07bf5-139">Další kroky</span><span class="sxs-lookup"><span data-stu-id="07bf5-139">Next steps</span></span>

<span data-ttu-id="07bf5-140">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="07bf5-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="07bf5-141">Vytvořte projekt webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="07bf5-141">Create a web app project.</span></span>
> * <span data-ttu-id="07bf5-142">Důvěřovat vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="07bf5-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="07bf5-143">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="07bf5-143">Run the project.</span></span>
> * <span data-ttu-id="07bf5-144">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="07bf5-144">Make a change.</span></span>

<span data-ttu-id="07bf5-145">Další informace o ASP.NET Core najdete v tématu doporučený postup výuky v úvodu:</span><span class="sxs-lookup"><span data-stu-id="07bf5-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
