---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: Krátký kurz, který vytvoří a spustí základní aplikaci Hello World pomocí ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 5/15/2019
uid: getting-started
ms.openlocfilehash: 9227dcfbc84376d9d73bc6fc0dd76085779acae1
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610306"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="54205-103">Kurz: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54205-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="54205-104">Tento kurz ukazuje, jak používat rozhraní příkazového řádku .NET Core k vytvoření a spuštění webové aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54205-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="54205-105">Se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="54205-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="54205-106">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="54205-106">Create a web app project.</span></span>
> * <span data-ttu-id="54205-107">Důvěřujete certifikátu vývoje.</span><span class="sxs-lookup"><span data-stu-id="54205-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="54205-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="54205-108">Run the app.</span></span>
> * <span data-ttu-id="54205-109">Úprava stránky syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="54205-109">Edit a Razor page.</span></span>

<span data-ttu-id="54205-110">Na konci máte spuštěn v místním počítači pomocí aplikace pracovní web app.</span><span class="sxs-lookup"><span data-stu-id="54205-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="54205-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="54205-112">Prerequisites</span></span>

* [<span data-ttu-id="54205-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="54205-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="54205-114">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="54205-114">Create a web app project</span></span>

<span data-ttu-id="54205-115">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="54205-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

### <a name="trust-the-development-certificate"></a><span data-ttu-id="54205-116">Důvěřovat certifikátu vývoj</span><span class="sxs-lookup"><span data-stu-id="54205-116">Trust the development certificate</span></span>

<span data-ttu-id="54205-117">Důvěřujete certifikátu vývoj HTTPS:</span><span class="sxs-lookup"><span data-stu-id="54205-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="54205-118">Windows</span><span class="sxs-lookup"><span data-stu-id="54205-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="54205-119">Ve výstupu předchozího příkazu se zobrazí následující dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="54205-119">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

<span data-ttu-id="54205-121">Vyberte **Ano** Pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.</span><span class="sxs-lookup"><span data-stu-id="54205-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="54205-122">macOS</span><span class="sxs-lookup"><span data-stu-id="54205-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="54205-123">Ve výstupu předchozího příkazu se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="54205-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="54205-124">*Byla vyžádána důvěřující vývojářský certifikát HTTPS. Pokud certifikát není důvěryhodný jsme se spusťte následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="54205-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="54205-125">Tento příkaz vás může vyzvat k zadání hesla k instalaci certifikátu v řetězci klíčů systému.</span><span class="sxs-lookup"><span data-stu-id="54205-125">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="54205-126">Zadejte svoje heslo, pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.</span><span class="sxs-lookup"><span data-stu-id="54205-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="54205-127">Linux</span><span class="sxs-lookup"><span data-stu-id="54205-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="54205-128">Subsystém Windows pro Linux, najdete v části [certifikátu důvěřovat HTTPS ze subsystému Windows pro Linux](xref:security/enforcing-ssl#wsl).</span><span class="sxs-lookup"><span data-stu-id="54205-128">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="54205-129">O tom, jak důvěřovat certifikátu protokolu HTTPS vývoj naleznete v dokumentaci k vaší distribuci Linuxu.</span><span class="sxs-lookup"><span data-stu-id="54205-129">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="54205-130">Další informace najdete v tématu [důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="54205-130">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="54205-131">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="54205-131">Run the app</span></span>

<span data-ttu-id="54205-132">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="54205-132">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="54205-133">Po příkazového okna označuje, aplikace byla spuštěna, přejděte do [ https://localhost:5001 ](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="54205-133">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="54205-134">Klikněte na tlačítko **přijmout** přijměte zásady ochrany osobních údajů a soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="54205-134">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="54205-135">Tato aplikace nemá uchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="54205-135">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="54205-136">Úprava stránky syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="54205-136">Edit a Razor page</span></span>

<span data-ttu-id="54205-137">Otevřít *Pages/Index.cshtml* a upravovat na stránce s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="54205-137">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="54205-138">Přejděte do [ https://localhost:5001 ](https://localhost:5001)a ověřte změny jsou zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="54205-138">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="54205-139">Další kroky</span><span class="sxs-lookup"><span data-stu-id="54205-139">Next steps</span></span>

<span data-ttu-id="54205-140">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="54205-140">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="54205-141">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="54205-141">Create a web app project.</span></span>
> * <span data-ttu-id="54205-142">Důvěřujete certifikátu vývoje.</span><span class="sxs-lookup"><span data-stu-id="54205-142">Trust the development certificate.</span></span>
> * <span data-ttu-id="54205-143">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="54205-143">Run the project.</span></span>
> * <span data-ttu-id="54205-144">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="54205-144">Make a change.</span></span>

<span data-ttu-id="54205-145">Další informace o ASP.NET Core najdete v tématu Doporučené výuky v úvodu:</span><span class="sxs-lookup"><span data-stu-id="54205-145">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
