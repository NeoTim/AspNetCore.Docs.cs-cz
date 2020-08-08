---
title: Použití analyzátorů webového rozhraní API
author: pranavkm
description: Přečtěte si o balíčku ASP.NET Core MVC Web API Analyzer.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/analyzers
ms.openlocfilehash: 571046052dbe131e9cdcf981aaee0921ed8c2ea1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021845"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="4670a-103">Použití analyzátorů webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4670a-103">Use web API analyzers</span></span>

<span data-ttu-id="4670a-104">ASP.NET Core 2,2 a novější poskytuje balíček pro analyzátory MVC, který je určený pro použití s projekty webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4670a-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="4670a-105">Analyzátory <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> při sestavování [konvencí webového rozhraní API](xref:web-api/advanced/conventions)pracují s řadiči, které jsou v systému poznámy.</span><span class="sxs-lookup"><span data-stu-id="4670a-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="4670a-106">Balíček analyzátory vás upozorní na jakoukoli akci kontroleru, která:</span><span class="sxs-lookup"><span data-stu-id="4670a-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="4670a-107">Vrátí nedeklarovaný stavový kód.</span><span class="sxs-lookup"><span data-stu-id="4670a-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="4670a-108">Vrátí nedeklarovaný výsledek úspěch.</span><span class="sxs-lookup"><span data-stu-id="4670a-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="4670a-109">Dokumentuje stavový kód, který se nevrátí.</span><span class="sxs-lookup"><span data-stu-id="4670a-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="4670a-110">Zahrnuje kontrolu explicitního ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="4670a-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="4670a-111">Odkaz na balíček analyzátoru</span><span class="sxs-lookup"><span data-stu-id="4670a-111">Reference the analyzer package</span></span>

<span data-ttu-id="4670a-112">V ASP.NET Core 3,0 nebo novějších jsou analyzátory zahrnuté v .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="4670a-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="4670a-113">Chcete-li povolit analyzátor v projektu, zahrňte `IncludeOpenAPIAnalyzers` vlastnost do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="4670a-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="4670a-114">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="4670a-114">Package installation</span></span>

<span data-ttu-id="4670a-115">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. API. analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) s jedním z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="4670a-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="4670a-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4670a-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4670a-117">V okně **konzoly Správce balíčků** :</span><span class="sxs-lookup"><span data-stu-id="4670a-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="4670a-118">Přejít k **zobrazení** > **jiné** > **konzoly Správce balíčků**Windows.</span><span class="sxs-lookup"><span data-stu-id="4670a-118">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="4670a-119">Přejděte do adresáře, ve kterém existuje soubor *ApiConventions. csproj* .</span><span class="sxs-lookup"><span data-stu-id="4670a-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="4670a-120">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4670a-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4670a-121">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4670a-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4670a-122">Klikněte pravým tlačítkem na složku *balíčky* v **oblast řešení** > **Přidat balíčky...**.</span><span class="sxs-lookup"><span data-stu-id="4670a-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="4670a-123">Nastavte rozevírací seznam pro **zdroj** okna **Přidat balíčky** na hodnotu "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="4670a-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="4670a-124">Do vyhledávacího pole zadejte Microsoft. AspNetCore. Mvc. API. Analyzer.</span><span class="sxs-lookup"><span data-stu-id="4670a-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="4670a-125">V podokně výsledků vyberte balíček Microsoft. AspNetCore. Mvc. API. Analyzer a klikněte na **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="4670a-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="4670a-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4670a-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4670a-127">Z **integrovaného terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4670a-127">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="4670a-128">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4670a-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4670a-129">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4670a-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="4670a-130">Analyzátory pro konvence webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4670a-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="4670a-131">Dokumenty OpenAPI obsahují stavové kódy a typy odezvy, které může akce vracet.</span><span class="sxs-lookup"><span data-stu-id="4670a-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="4670a-132">Ve ASP.NET Core MVC jsou atributy jako <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> a <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> použity k dokumentaci akce.</span><span class="sxs-lookup"><span data-stu-id="4670a-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="4670a-133"><xref:tutorials/web-api-help-pages-using-swagger>přejdete k podrobnostem o dokumentaci k webovému rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4670a-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="4670a-134">Jeden z analyzátorů v balíčku kontroluje řadiče s poznámkami <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> a identifikuje akce, které neobsahují odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4670a-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="4670a-135">Uvažujte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="4670a-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="4670a-136">Předchozí akce dokumentuje návratový typ úspěšného protokolu HTTP 200, ale nedokumentuje stavový kód chyby HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="4670a-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="4670a-137">Analyzátor nahlásí chybějící dokumentaci pro stavový kód HTTP 404 jako upozornění.</span><span class="sxs-lookup"><span data-stu-id="4670a-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="4670a-138">K dispozici je možnost, jak problém vyřešit.</span><span class="sxs-lookup"><span data-stu-id="4670a-138">An option to fix the problem is provided.</span></span>

![vyhlášení výstrahy analyzátorem](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="4670a-140">Další materiály</span><span class="sxs-lookup"><span data-stu-id="4670a-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
