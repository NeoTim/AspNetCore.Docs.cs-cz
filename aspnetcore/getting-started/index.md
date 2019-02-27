---
title: Začínáme s ASP.NET Core
author: rick-anderson
description: 'Rychlý kurz, který vytvoří a spustí jednoduchou aplikaci Hello World pomocí ASP.NET Core.'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2019
uid: getting-started
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="34063-103">Kurz: Začínáme s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34063-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="34063-104">Tento návod ukazuje, jak vytvořit webovou aplikaci ASP.NET jádro pomocí rozhraní příkazového řádku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="34063-104">This tutorial shows how to use the .NET Core command-line interface to create an ASP.NET Core web app.</span></span>

<span data-ttu-id="34063-105">Se dozvíte, jak:</span><span class="sxs-lookup"><span data-stu-id="34063-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="34063-106">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="34063-106">Create a web app project.</span></span>
> * <span data-ttu-id="34063-107">Povolte místní HTTPS.</span><span class="sxs-lookup"><span data-stu-id="34063-107">Enable local HTTPS.</span></span>
> * <span data-ttu-id="34063-108">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="34063-108">Run the app.</span></span>
> * <span data-ttu-id="34063-109">Úprava stránky syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="34063-109">Edit a Razor page.</span></span>

<span data-ttu-id="34063-110">Na konci máte spuštěn v místním počítači pomocí aplikace pracovní web app.</span><span class="sxs-lookup"><span data-stu-id="34063-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Domovská stránka aplikace](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="34063-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="34063-112">Prerequisites</span></span>

* [<span data-ttu-id="34063-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="34063-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="34063-114">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="34063-114">Create a web app project</span></span>

<span data-ttu-id="34063-115">Otevřete příkazové prostředí a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="34063-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a><span data-ttu-id="34063-116">Povolit místní HTTPS</span><span class="sxs-lookup"><span data-stu-id="34063-116">Enable local HTTPS</span></span>

<span data-ttu-id="34063-117">Důvěřujete certifikátu vývoj HTTPS:</span><span class="sxs-lookup"><span data-stu-id="34063-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="34063-118">Windows</span><span class="sxs-lookup"><span data-stu-id="34063-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="34063-119">Ve výstupu předchozího příkazu se zobrazí následující dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="34063-119">The preceding command displays the following dialog:</span></span>

![Dialogové okno upozornění zabezpečení](~/getting-started/_static/cert.png)

<span data-ttu-id="34063-121">Vyberte **Ano** Pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.</span><span class="sxs-lookup"><span data-stu-id="34063-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="34063-122">macOS</span><span class="sxs-lookup"><span data-stu-id="34063-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="34063-123">Ve výstupu předchozího příkazu se zobrazí následující zpráva:</span><span class="sxs-lookup"><span data-stu-id="34063-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="34063-124">*Byla vyžádána důvěřující vývojářský certifikát HTTPS. Pokud certifikát není důvěryhodný provedeme následující příkaz:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span><span class="sxs-lookup"><span data-stu-id="34063-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span></span>
 
<span data-ttu-id="34063-125">Tento příkaz příkaz vás může vyzvat k zadání hesla k instalaci certifikátu v řetězci klíčů systému.</span><span class="sxs-lookup"><span data-stu-id="34063-125">This command command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="34063-126">Zadejte svoje heslo, pokud vyjádříte souhlas s důvěřovat certifikátu vývoje.</span><span class="sxs-lookup"><span data-stu-id="34063-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="34063-127">Linux</span><span class="sxs-lookup"><span data-stu-id="34063-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="34063-128">O tom, jak důvěřovat certifikátu protokolu HTTPS vývoj naleznete v dokumentaci k vaší distribuci Linuxu.</span><span class="sxs-lookup"><span data-stu-id="34063-128">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="34063-129">Další informace najdete v tématu [důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="34063-129">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="34063-130">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="34063-130">Run the app</span></span>

<span data-ttu-id="34063-131">Spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="34063-131">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="34063-132">Po příkazového okna označuje, aplikace byla spuštěna, přejděte do [ https://localhost:5001 ](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="34063-132">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="34063-133">Klikněte na tlačítko **přijmout** přijměte zásady ochrany osobních údajů a soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="34063-133">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="34063-134">Tato aplikace nemá uchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="34063-134">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="34063-135">Úprava stránky syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="34063-135">Edit a Razor page</span></span>

<span data-ttu-id="34063-136">Otevřít *Pages/Index.cshtml* a upravovat na stránce s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="34063-136">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="34063-137">Přejděte do [ https://localhost:5001 ](https://localhost:5001)a ověřte změny jsou zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="34063-137">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="34063-138">Další kroky</span><span class="sxs-lookup"><span data-stu-id="34063-138">Next steps</span></span>

<span data-ttu-id="34063-139">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="34063-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="34063-140">Vytvoření projektu webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="34063-140">Create a web app project.</span></span>
> * <span data-ttu-id="34063-141">Povolte místní HTTPS.</span><span class="sxs-lookup"><span data-stu-id="34063-141">Enable local HTTPS.</span></span>
> * <span data-ttu-id="34063-142">Spusťte projekt.</span><span class="sxs-lookup"><span data-stu-id="34063-142">Run the project.</span></span>
> * <span data-ttu-id="34063-143">Proveďte změnu.</span><span class="sxs-lookup"><span data-stu-id="34063-143">Make a change.</span></span>

<span data-ttu-id="34063-144">Další informace o ASP.NET Core najdete v tématu Doporučené výuky v úvodu:</span><span class="sxs-lookup"><span data-stu-id="34063-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
