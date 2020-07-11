---
title: Nástroje pro ASP.NET CoreBlazor
author: guardrex
description: Přečtěte si informace o nástrojích dostupných pro sestavování Blazor aplikací.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 0bd38d8d16365a80d7954c860a4e20e2280c36b2
ms.sourcegitcommit: e216e8f4afa21215dc38124c28d5ee19f5ed7b1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86239605"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="d0834-103">Nástroje pro ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="d0834-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="d0834-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0834-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="d0834-105">Nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .</span><span class="sxs-lookup"><span data-stu-id="d0834-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="d0834-106">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="d0834-106">Create a new project.</span></span>

1. <span data-ttu-id="d0834-107">Vyberte možnost \*\* Blazor aplikace\*\*.</span><span class="sxs-lookup"><span data-stu-id="d0834-107">Select **Blazor App**.</span></span> <span data-ttu-id="d0834-108">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d0834-108">Select **Next**.</span></span>

1. <span data-ttu-id="d0834-109">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="d0834-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d0834-110">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="d0834-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d0834-111">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d0834-111">Select **Create**.</span></span>

1. <span data-ttu-id="d0834-112">Pro Blazor WebAssembly prostředí vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="d0834-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d0834-113">Pro Blazor Server prostředí vyberte šablonu \*\* Blazor Server aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="d0834-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d0834-114">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d0834-114">Select **Create**.</span></span>

   <span data-ttu-id="d0834-115">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="d0834-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d0834-116">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="d0834-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="d0834-117">Další informace o důvěřování certifikátu pro vývoj ASP.NET Core HTTPS najdete v tématu <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="d0834-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="d0834-118">Nainstalujte nejnovější verzi sady [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="d0834-118">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="d0834-119">Pokud jste dříve nainstalovali sadu SDK, můžete zjistit nainstalovanou verzi spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="d0834-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="d0834-120">Nainstalujte nejnovější verzi [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="d0834-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="d0834-121">Nainstalujte nejnovější [rozšíření C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="d0834-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="d0834-122">Pro Blazor WebAssembly prostředí spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="d0834-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="d0834-123">Pro Blazor Server prostředí spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="d0834-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="d0834-124">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="d0834-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d0834-125">Otevřete `WebApplication1` složku v Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d0834-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="d0834-126">Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu.</span><span class="sxs-lookup"><span data-stu-id="d0834-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="d0834-127">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="d0834-127">Select **Yes**.</span></span>

1. <span data-ttu-id="d0834-128">Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .</span><span class="sxs-lookup"><span data-stu-id="d0834-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="d0834-129">Důvěryhodnost vývojového certifikátu</span><span class="sxs-lookup"><span data-stu-id="d0834-129">Trust a development certificate</span></span>

<span data-ttu-id="d0834-130">Neexistuje žádný centralizovaný způsob, jak důvěřovat certifikátu v systému Linux.</span><span class="sxs-lookup"><span data-stu-id="d0834-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="d0834-131">Obvykle se přijme jedna z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d0834-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="d0834-132">Vylučte adresu URL aplikace v seznamu vyloučení v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d0834-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="d0834-133">Důvěřovat všem certifikátům podepsaným svým držitelem pro `localhost` .</span><span class="sxs-lookup"><span data-stu-id="d0834-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="d0834-134">Přidejte certifikát do seznamu důvěryhodných certifikátů v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d0834-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="d0834-135">Další informace najdete v pokynech, které poskytuje prohlížeč a Linux distribuce.</span><span class="sxs-lookup"><span data-stu-id="d0834-135">For more information, see the guidance provided by your browser and Linux distro.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="d0834-136">Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="d0834-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="d0834-137">Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.</span><span class="sxs-lookup"><span data-stu-id="d0834-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d0834-138">Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="d0834-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="d0834-139">Pro Blazor WebAssembly prostředí vyberte šablonu \*\* Blazor WebAssembly aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="d0834-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d0834-140">Pro Blazor Server prostředí vyberte šablonu \*\* Blazor Server aplikace\*\* .</span><span class="sxs-lookup"><span data-stu-id="d0834-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d0834-141">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d0834-141">Select **Next**.</span></span>

   <span data-ttu-id="d0834-142">Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="d0834-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d0834-143">Potvrďte, že **ověřování** je nastaveno na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="d0834-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d0834-144">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d0834-144">Select **Next**.</span></span>

1. <span data-ttu-id="d0834-145">Do pole **název projektu** název aplikace `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="d0834-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="d0834-146">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d0834-146">Select **Create**.</span></span>

1. <span data-ttu-id="d0834-147">**Run**  >  Chcete-li spustit aplikaci *bez ladicího programu*, vyberte možnost spustit**bez ladění** .</span><span class="sxs-lookup"><span data-stu-id="d0834-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="d0834-148">Spusťte aplikaci pomocí **příkazového**  >  **počátečního ladění** nebo tlačítka Spustit (&#9654;), aby se aplikace spustila *s ladicím programem*.</span><span class="sxs-lookup"><span data-stu-id="d0834-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="d0834-149">Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte.</span><span class="sxs-lookup"><span data-stu-id="d0834-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d0834-150">Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d0834-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="d0834-151">Další informace o důvěřování certifikátu pro vývoj ASP.NET Core HTTPS najdete v tématu <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="d0834-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
