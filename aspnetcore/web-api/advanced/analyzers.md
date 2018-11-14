---
title: Použití webového rozhraní API analyzátorů
author: pranavkm
description: Další informace o analyzátorech webové rozhraní API v Microsoft.AspNetCore.Mvc.Api.Analyzers.
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 11/13/2018
uid: web-api/advanced/analyzers
ms.openlocfilehash: 89424d89ec2b3125fd3c6b7c86fed2d292b153e6
ms.sourcegitcommit: f202864efca81a72ea7120c0692940c40d9d0630
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635362"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="3d504-103">Použití webového rozhraní API analyzátorů</span><span class="sxs-lookup"><span data-stu-id="3d504-103">Use web API analyzers</span></span>

<span data-ttu-id="3d504-104">ASP.NET Core 2.2 zavádí [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) balíček NuGet obsahující analyzátory pro webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3d504-104">ASP.NET Core 2.2 introduces the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package containing analyzers for web APIs.</span></span> <span data-ttu-id="3d504-105">Analyzátory práci s řadiči opatřen poznámkou <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, při vytváření [API konvence](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="3d504-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [API conventions](xref:web-api/advanced/conventions).</span></span>

## <a name="package-installation"></a><span data-ttu-id="3d504-106">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="3d504-106">Package installation</span></span>

<span data-ttu-id="3d504-107">`Microsoft.AspNetCore.Mvc.Api.Analyzers` lze přidat pomocí jednoho z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="3d504-107">`Microsoft.AspNetCore.Mvc.Api.Analyzers` can be added with one of the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3d504-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d504-108">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3d504-109">Z **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="3d504-109">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="3d504-110">Přejděte na **zobrazení** > **jiných Windows** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="3d504-110">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="3d504-111">Přejděte do adresáře, ve kterém *ApiConventions.csproj* soubor existuje.</span><span class="sxs-lookup"><span data-stu-id="3d504-111">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="3d504-112">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3d504-112">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* <span data-ttu-id="3d504-113">Z **spravovat balíčky NuGet** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="3d504-113">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="3d504-114">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="3d504-114">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
  * <span data-ttu-id="3d504-115">Nastavte **zdroj balíčku** do "nuget.org".</span><span class="sxs-lookup"><span data-stu-id="3d504-115">Set the **Package source** to "nuget.org".</span></span>
  * <span data-ttu-id="3d504-116">Do vyhledávacího pole zadejte "Microsoft.AspNetCore.Mvc.Api.Analyzers".</span><span class="sxs-lookup"><span data-stu-id="3d504-116">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
  * <span data-ttu-id="3d504-117">Vyberte balíček "Microsoft.AspNetCore.Mvc.Api.Analyzers" z **Procházet** kartě a klikněte na tlačítko **nainstalovat**.</span><span class="sxs-lookup"><span data-stu-id="3d504-117">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the **Browse** tab and click **Install**.</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3d504-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3d504-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3d504-119">Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...** .</span><span class="sxs-lookup"><span data-stu-id="3d504-119">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="3d504-120">Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org".</span><span class="sxs-lookup"><span data-stu-id="3d504-120">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="3d504-121">Do vyhledávacího pole zadejte "Microsoft.AspNetCore.Mvc.Api.Analyzers".</span><span class="sxs-lookup"><span data-stu-id="3d504-121">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="3d504-122">Vyberte v podokně výsledků "Microsoft.AspNetCore.Mvc.Api.Analyzers" balíček a klikněte na tlačítko **přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="3d504-122">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3d504-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3d504-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3d504-124">Spuštěním následujícího příkazu z **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="3d504-124">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3d504-125">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3d504-125">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3d504-126">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3d504-126">Run the following command:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a><span data-ttu-id="3d504-127">Analyzátory pro vytváření rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3d504-127">Analyzers for API conventions</span></span>

<span data-ttu-id="3d504-128">Otevřít dokumenty rozhraní API obsahují stavové kódy a typů odpovědi, které můžou vrátit akci.</span><span class="sxs-lookup"><span data-stu-id="3d504-128">Open API documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="3d504-129">V ASP.NET Core MVC, atributy, jako <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> a <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> se používají k dokumentu akci.</span><span class="sxs-lookup"><span data-stu-id="3d504-129">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="3d504-130"><xref:tutorials/web-api-help-pages-using-swagger> obsahuje další podrobnosti o dokumentaci rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3d504-130"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your API.</span></span>

<span data-ttu-id="3d504-131">Jeden z analyzátory v balíčku zkontroluje řadiče opatřen poznámkou <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> a identifikuje akce, které není úplně dokumentu jejich odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3d504-131">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="3d504-132">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3d504-132">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

<span data-ttu-id="3d504-133">Předchozí akci dokumenty HTTP 200 úspěch návratový typ, ale ne dokumentu stavový kód chyby HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="3d504-133">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="3d504-134">Analyzátor ohlásí chybí dokumentaci stavový kód HTTP 404, jako varování.</span><span class="sxs-lookup"><span data-stu-id="3d504-134">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="3d504-135">Je k dispozici možnost tento problém vyřešit.</span><span class="sxs-lookup"><span data-stu-id="3d504-135">An option to fix the problem is provided.</span></span>
