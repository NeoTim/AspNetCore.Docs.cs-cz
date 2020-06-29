---
title: Začínáme s swashbuckle a ASP.NET Core
author: zuckerthoben
description: Naučte se, jak přidat swashbuckle do projektu webového rozhraní API ASP.NET Core pro integraci uživatelského rozhraní Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 0a47ed3338ebfbc5361a6082978d407543fb95c5
ms.sourcegitcommit: b06511252f165dd4590ba9b5beca4153fa220779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2020
ms.locfileid: "85459776"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="e74cd-103">Začínáme s swashbuckle a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e74cd-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="e74cd-104">Od [Shayne Boyer](https://twitter.com/spboyer) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e74cd-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e74cd-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e74cd-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e74cd-106">Swashbuckle má tři hlavní komponenty:</span><span class="sxs-lookup"><span data-stu-id="e74cd-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="e74cd-107">[Swashbuckle. AspNetCore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): objektový model a middleware pro objekty Swagger k vystavení `SwaggerDocument` objektů jako koncových bodů JSON.</span><span class="sxs-lookup"><span data-stu-id="e74cd-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="e74cd-108">[Swashbuckle. AspNetCore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generátor Swagger, který vytváří `SwaggerDocument` objekty přímo z vašich tras, řadičů a modelů.</span><span class="sxs-lookup"><span data-stu-id="e74cd-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="e74cd-109">Obvykle funguje tak, že v kombinaci s middlewarem koncových bodů Swaggeru automaticky zveřejňuje kód JSON pro Swagger.</span><span class="sxs-lookup"><span data-stu-id="e74cd-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="e74cd-110">[Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): vložená verze nástroje uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="e74cd-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="e74cd-111">Interpretuje JSON pro Swagger v podobě propracovaného, přizpůsobitelného prostředí pro popis funkcí webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e74cd-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="e74cd-112">Jeho součástí je i integrovaný správce testů pro veřejné metody.</span><span class="sxs-lookup"><span data-stu-id="e74cd-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="e74cd-113">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="e74cd-113">Package installation</span></span>

<span data-ttu-id="e74cd-114">Swashbuckle je možné přidat s následujícími přístupy:</span><span class="sxs-lookup"><span data-stu-id="e74cd-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="e74cd-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e74cd-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e74cd-116">V okně **konzoly Správce balíčků** :</span><span class="sxs-lookup"><span data-stu-id="e74cd-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="e74cd-117">Přejít k **zobrazení**  >  **jiné**  >  **konzoly Správce balíčků** Windows</span><span class="sxs-lookup"><span data-stu-id="e74cd-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="e74cd-118">Přejděte do adresáře, ve kterém existuje soubor *TodoApi. csproj.*</span><span class="sxs-lookup"><span data-stu-id="e74cd-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="e74cd-119">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e74cd-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.5.0
    ```

* <span data-ttu-id="e74cd-120">V dialogovém okně **Spravovat balíčky NuGet** :</span><span class="sxs-lookup"><span data-stu-id="e74cd-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="e74cd-121">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**  >  **Spravovat balíčky NuGet** .</span><span class="sxs-lookup"><span data-stu-id="e74cd-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="e74cd-122">Nastavte **zdroj balíčku** na "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="e74cd-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="e74cd-123">Ujistěte se, že je povolená možnost zahrnout předběžné verze.</span><span class="sxs-lookup"><span data-stu-id="e74cd-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="e74cd-124">Do vyhledávacího pole zadejte "swashbuckle. AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="e74cd-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="e74cd-125">Na kartě **Procházet** vyberte nejnovější balíček "swashbuckle. AspNetCore" a klikněte na **instalovat** .</span><span class="sxs-lookup"><span data-stu-id="e74cd-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e74cd-126">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e74cd-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e74cd-127">Klikněte pravým tlačítkem na složku *balíčky* v **oblast řešení**  >  **Přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="e74cd-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="e74cd-128">Nastavte rozevírací seznam pro **zdroj** okna **Přidat balíčky** na "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="e74cd-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="e74cd-129">Ujistěte se, že je povolená možnost Zobrazit balíčky předběžných verzí.</span><span class="sxs-lookup"><span data-stu-id="e74cd-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="e74cd-130">Do vyhledávacího pole zadejte "swashbuckle. AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="e74cd-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="e74cd-131">V podokně výsledků vyberte nejnovější balíček "swashbuckle. AspNetCore" a klikněte na **Přidat balíček** .</span><span class="sxs-lookup"><span data-stu-id="e74cd-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="e74cd-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e74cd-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e74cd-133">Z **integrovaného terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e74cd-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="e74cd-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e74cd-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e74cd-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e74cd-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="e74cd-136">Přidat a nakonfigurovat middleware Swagger</span><span class="sxs-lookup"><span data-stu-id="e74cd-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="e74cd-137">Přidejte generátor Swagger do kolekce služeb v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="e74cd-137">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

<span data-ttu-id="e74cd-138">V `Startup.Configure` metodě povolte middleware pro poskytování generovaného dokumentu JSON a uživatelského rozhraní Swagger:</span><span class="sxs-lookup"><span data-stu-id="e74cd-138">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e74cd-139">Swashbuckle spoléhá na <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> zjišťování tras a koncových bodů na MVC.</span><span class="sxs-lookup"><span data-stu-id="e74cd-139">Swashbuckle relies on MVC's <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> to discover the routes and endpoints.</span></span> <span data-ttu-id="e74cd-140">Pokud je volání projektu <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> , jsou trasy a koncové body zjišťovány automaticky.</span><span class="sxs-lookup"><span data-stu-id="e74cd-140">If the project calls <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, routes and endpoints are discovered automatically.</span></span> <span data-ttu-id="e74cd-141">Při volání <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> metody musí být metoda explicitně volána.</span><span class="sxs-lookup"><span data-stu-id="e74cd-141">When calling <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, the <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> method must be explicitly called.</span></span> <span data-ttu-id="e74cd-142">Další informace najdete v tématech [swashbuckle, ApiExplorer a Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span><span class="sxs-lookup"><span data-stu-id="e74cd-142">For more information, see [Swashbuckle, ApiExplorer, and Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span></span>

<span data-ttu-id="e74cd-143">Předchozí `UseSwaggerUI` volání metody povoluje [middleware statických souborů](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="e74cd-143">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="e74cd-144">Pokud cílíte .NET Framework nebo .NET Core 1. x, přidejte do projektu balíček NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .</span><span class="sxs-lookup"><span data-stu-id="e74cd-144">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="e74cd-145">Spusťte aplikaci a přejděte na adresu `http://localhost:<port>/swagger/v1/swagger.json` .</span><span class="sxs-lookup"><span data-stu-id="e74cd-145">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="e74cd-146">Vygenerovaný dokument popisující koncové body se zobrazí, jak je znázorněno ve [specifikaci Swagger (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="e74cd-146">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="e74cd-147">Uživatelské rozhraní Swagger najdete na adrese `http://localhost:<port>/swagger` .</span><span class="sxs-lookup"><span data-stu-id="e74cd-147">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="e74cd-148">Prozkoumejte rozhraní API prostřednictvím uživatelského rozhraní Swagger a zahrňte je do jiných programů.</span><span class="sxs-lookup"><span data-stu-id="e74cd-148">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="e74cd-149">Pro obsluhu uživatelského rozhraní Swagger v kořenovém adresáři aplikace ( `http://localhost:<port>/` ) nastavte `RoutePrefix` vlastnost na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="e74cd-149">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="e74cd-150">Pokud používáte adresáře se službou IIS nebo reverzním proxy serverem, nastavte koncový bod Swagger na relativní cestu pomocí `./` předpony.</span><span class="sxs-lookup"><span data-stu-id="e74cd-150">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="e74cd-151">Například, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="e74cd-151">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="e74cd-152">Pomocí nástroje `/swagger/v1/swagger.json` instruuje aplikaci, aby hledala soubor JSON na skutečném kořenu adresy URL (plus předpona trasy, pokud se používá).</span><span class="sxs-lookup"><span data-stu-id="e74cd-152">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="e74cd-153">Můžete například namísto `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` použít `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="e74cd-153">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

> [!NOTE]
> <span data-ttu-id="e74cd-154">Ve výchozím nastavení swashbuckle vygeneruje a zpřístupňuje JSON pro Swagger ve verzi 3,0 specifikace &mdash; oficiálně označované jako specifikace openapi.</span><span class="sxs-lookup"><span data-stu-id="e74cd-154">By default, Swashbuckle generates and exposes Swagger JSON in version 3.0 of the specification&mdash;officially called the OpenAPI Specification.</span></span> <span data-ttu-id="e74cd-155">Pro podporu zpětné kompatibility můžete místo toho použít k odhalení JSON ve formátu 2,0.</span><span class="sxs-lookup"><span data-stu-id="e74cd-155">To support backwards compatibility, you can opt into exposing JSON in the 2.0 format instead.</span></span> <span data-ttu-id="e74cd-156">Tento formát 2,0 je důležitý pro integrace, jako je Microsoft Power Apps a Microsoft Flow, které aktuálně podporují OpenAPI verze 2,0.</span><span class="sxs-lookup"><span data-stu-id="e74cd-156">This 2.0 format is important for integrations such as Microsoft Power Apps and Microsoft Flow that currently support OpenAPI version 2.0.</span></span> <span data-ttu-id="e74cd-157">Chcete-li se vyjádřit ke formátu 2,0, nastavte `SerializeAsV2` vlastnost v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e74cd-157">To opt into the 2.0 format, set the `SerializeAsV2` property in `Startup.Configure`:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a><span data-ttu-id="e74cd-158">Přizpůsobení a rozšiřování</span><span class="sxs-lookup"><span data-stu-id="e74cd-158">Customize and extend</span></span>

<span data-ttu-id="e74cd-159">Swagger poskytuje možnosti pro dokumentaci k objektovému modelu a přizpůsobení uživatelského rozhraní tak, aby odpovídalo vašemu motivu.</span><span class="sxs-lookup"><span data-stu-id="e74cd-159">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="e74cd-160">Do `Startup` třídy přidejte následující obory názvů:</span><span class="sxs-lookup"><span data-stu-id="e74cd-160">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="e74cd-161">Informace a popis rozhraní API</span><span class="sxs-lookup"><span data-stu-id="e74cd-161">API info and description</span></span>

<span data-ttu-id="e74cd-162">Akce konfigurace předaná `AddSwaggerGen` metodě přidá informace, jako je autor, licence a popis:</span><span class="sxs-lookup"><span data-stu-id="e74cd-162">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

<span data-ttu-id="e74cd-163">Ve `Startup` třídě importujte následující obor názvů pro použití `OpenApiInfo` třídy:</span><span class="sxs-lookup"><span data-stu-id="e74cd-163">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="e74cd-164">Pomocí `OpenApiInfo` třídy upravte informace zobrazené v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e74cd-164">Using the `OpenApiInfo` class, modify the information displayed in the UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="e74cd-165">Uživatelské rozhraní Swagger zobrazuje informace o verzi:</span><span class="sxs-lookup"><span data-stu-id="e74cd-165">The Swagger UI displays the version's information:</span></span>

![Uživatelské rozhraní Swagger s informacemi o verzi: Popis, autor a zobrazit další odkaz](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="e74cd-167">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="e74cd-167">XML comments</span></span>

<span data-ttu-id="e74cd-168">Komentáře XML lze povolit pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e74cd-168">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="e74cd-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e74cd-169">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="e74cd-170">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **upravit <PROJECT_NAME>. csproj**.</span><span class="sxs-lookup"><span data-stu-id="e74cd-170">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="e74cd-171">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e74cd-171">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="e74cd-172">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="e74cd-172">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="e74cd-173">V části **výstup** na kartě **sestavení** zaškrtněte políčko **soubor dokumentace XML** .</span><span class="sxs-lookup"><span data-stu-id="e74cd-173">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e74cd-174">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e74cd-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="e74cd-175">V *oblast řešení*stiskněte **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="e74cd-175">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="e74cd-176">Přejděte do **nástroje**  >  **Upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="e74cd-176">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="e74cd-177">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e74cd-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="e74cd-178">Otevření dialogového okna **Možnosti projektu** > **Build** > **kompilátor** sestavení</span><span class="sxs-lookup"><span data-stu-id="e74cd-178">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="e74cd-179">V části **Obecné možnosti** zaškrtněte políčko **generovat dokumentaci XML** .</span><span class="sxs-lookup"><span data-stu-id="e74cd-179">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="e74cd-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e74cd-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e74cd-181">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e74cd-181">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="e74cd-182">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e74cd-182">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e74cd-183">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e74cd-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="e74cd-184">Povolení komentářů XML poskytuje ladicí informace pro nedokumentované veřejné typy a členy.</span><span class="sxs-lookup"><span data-stu-id="e74cd-184">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="e74cd-185">Nedokumentované typy a členy jsou označeny varovnou zprávou.</span><span class="sxs-lookup"><span data-stu-id="e74cd-185">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="e74cd-186">Například následující zpráva indikuje porušení kódu upozornění 1591:</span><span class="sxs-lookup"><span data-stu-id="e74cd-186">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="e74cd-187">Chcete-li potlačit upozornění v rámci projektu, definujte seznam kódů upozornění oddělených středníkem, které mají být v souboru projektu ignorovány.</span><span class="sxs-lookup"><span data-stu-id="e74cd-187">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="e74cd-188">Připojení kódů upozornění k `$(NoWarn);` použití [výchozích hodnot jazyka C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .</span><span class="sxs-lookup"><span data-stu-id="e74cd-188">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="e74cd-189">Chcete-li potlačit upozornění pouze pro konkrétní členy, uveďte kód v direktivách preprocesoru [upozornění #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) .</span><span class="sxs-lookup"><span data-stu-id="e74cd-189">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="e74cd-190">Tento přístup je užitečný pro kód, který by neměl být zveřejněn prostřednictvím dokumentace rozhraní API. V následujícím příkladu je kód upozornění CS1591 ignorován pro celou `Program` třídu.</span><span class="sxs-lookup"><span data-stu-id="e74cd-190">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="e74cd-191">Vynucování kódu upozornění je obnoveno při zavření definice třídy.</span><span class="sxs-lookup"><span data-stu-id="e74cd-191">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="e74cd-192">Zadejte více kódů upozornění se seznamem odděleným čárkami.</span><span class="sxs-lookup"><span data-stu-id="e74cd-192">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="e74cd-193">Nakonfigurujte Swagger pro použití souboru XML, který je vygenerovaný předchozími pokyny.</span><span class="sxs-lookup"><span data-stu-id="e74cd-193">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="e74cd-194">Pro operační systémy Linux a jiné než Windows můžou názvy souborů a cesty rozlišovat velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="e74cd-194">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="e74cd-195">Například soubor *TodoApi.XML* je platný v systému Windows, ale nikoli CentOS.</span><span class="sxs-lookup"><span data-stu-id="e74cd-195">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="e74cd-196">V předchozím kódu se [reflexe](/dotnet/csharp/programming-guide/concepts/reflection) používá k sestavení názvu souboru XML, který odpovídá tomuto projektu webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e74cd-196">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="e74cd-197">Vlastnost [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) se používá k vytvoření cesty k souboru XML.</span><span class="sxs-lookup"><span data-stu-id="e74cd-197">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="e74cd-198">Některé funkce Swagger (například schémat vstupních parametrů nebo metody HTTP a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML.</span><span class="sxs-lookup"><span data-stu-id="e74cd-198">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="e74cd-199">Pro většinu funkcí, konkrétně souhrny metod a popisy parametrů a kódů odpovědí, je použití souboru XML povinné.</span><span class="sxs-lookup"><span data-stu-id="e74cd-199">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="e74cd-200">Můžete k akci přidat také komentáře uvozené třemi lomítky, a vylepšit tak Swagger UI tím, že přidáte popis do hlavičky oddílu.</span><span class="sxs-lookup"><span data-stu-id="e74cd-200">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="e74cd-201">Přidejte [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element nad `Delete` akci:</span><span class="sxs-lookup"><span data-stu-id="e74cd-201">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="e74cd-202">Uživatelské rozhraní Swagger zobrazí vnitřní text elementu předchozí kód `<summary>` :</span><span class="sxs-lookup"><span data-stu-id="e74cd-202">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Uživatelské rozhraní Swagger, které zobrazuje komentář XML, odstraní konkrétní TodoItem.](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="e74cd-205">Uživatelské rozhraní je založené na generovaném schématu JSON:</span><span class="sxs-lookup"><span data-stu-id="e74cd-205">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="e74cd-206">Přidejte [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element do dokumentace k `Create` metodě akce.</span><span class="sxs-lookup"><span data-stu-id="e74cd-206">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="e74cd-207">Doplňuje informace zadané v `<summary>` prvku a poskytuje robustnější uživatelské rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="e74cd-207">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="e74cd-208">`<remarks>`Obsah elementu se může skládat z textu, JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="e74cd-208">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="e74cd-209">Všimněte si vylepšení uživatelského rozhraní pomocí těchto dalších komentářů:</span><span class="sxs-lookup"><span data-stu-id="e74cd-209">Notice the UI enhancements with these additional comments:</span></span>

![Uživatelské rozhraní Swagger s dalšími zobrazenými komentáři](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="e74cd-211">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="e74cd-211">Data annotations</span></span>

<span data-ttu-id="e74cd-212">Označte model s atributy, které se nacházejí v oboru názvů [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , aby bylo možné lépe řídit součásti uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="e74cd-212">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="e74cd-213">Přidejte `[Required]` atribut do `Name` vlastnosti `TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="e74cd-213">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="e74cd-214">Přítomnost tohoto atributu mění chování uživatelského rozhraní a mění základní schéma JSON:</span><span class="sxs-lookup"><span data-stu-id="e74cd-214">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="e74cd-215">Přidejte `[Produces("application/json")]` atribut do kontroleru rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e74cd-215">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="e74cd-216">Jeho účelem je deklarovat, že akce kontroleru podporují typ obsahu odpovědi *Application/JSON*:</span><span class="sxs-lookup"><span data-stu-id="e74cd-216">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="e74cd-217">Rozevírací seznam **typ obsahu odpovědi** pro akce GET kontroleru vybere tento typ obsahu jako výchozí:</span><span class="sxs-lookup"><span data-stu-id="e74cd-217">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Uživatelské rozhraní Swagger s výchozím typem obsahu odpovědi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="e74cd-219">Díky nárůstu využití datových poznámek ve webovém rozhraní API se stránky s nápovědu pro uživatelské rozhraní a rozhraní API stanou výstižnější a užitečnější.</span><span class="sxs-lookup"><span data-stu-id="e74cd-219">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="e74cd-220">Popis typů odpovědí</span><span class="sxs-lookup"><span data-stu-id="e74cd-220">Describe response types</span></span>

<span data-ttu-id="e74cd-221">Vývojáři, kteří využívají webové rozhraní API, mají největší obavy s tím, co vrací &mdash; konkrétně typy odpovědí a kódy chyb (Pokud ne Standard).</span><span class="sxs-lookup"><span data-stu-id="e74cd-221">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="e74cd-222">Typy odpovědí a kódy chyb jsou označeny v komentářích XML a datových anotacích.</span><span class="sxs-lookup"><span data-stu-id="e74cd-222">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="e74cd-223">`Create`Akce vrátí stavový kód HTTP 201 při úspěchu.</span><span class="sxs-lookup"><span data-stu-id="e74cd-223">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="e74cd-224">Stavový kód HTTP 400 se vrátí, když text odeslaného požadavku má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="e74cd-224">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="e74cd-225">Bez správné dokumentace v uživatelském rozhraní Swagger nemá spotřebitel znalosti o těchto očekávaných výsledcích.</span><span class="sxs-lookup"><span data-stu-id="e74cd-225">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="e74cd-226">Opravte tento problém tak, že přidáte zvýrazněné řádky v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e74cd-226">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="e74cd-227">Uživatelské rozhraní Swagger teď jasně klade očekávané kódy odpovědí HTTP:</span><span class="sxs-lookup"><span data-stu-id="e74cd-227">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Uživatelské rozhraní Swagger, které zobrazuje popis třídy odpovědí POST, vrátí nově vytvořenou položku TODO ' a ' 400 – Pokud má položka hodnotu null pro stavový kód a důvod v rámci zpráv odpovědí.](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e74cd-229">V ASP.NET Core 2,2 nebo novějších lze konvence použít jako alternativu k explicitnímu Upraveníí jednotlivých akcí `[ProducesResponseType]` .</span><span class="sxs-lookup"><span data-stu-id="e74cd-229">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="e74cd-230">Další informace naleznete v tématu <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="e74cd-230">For more information, see <xref:web-api/advanced/conventions>.</span></span>

<span data-ttu-id="e74cd-231">Pro podporu `[ProducesResponseType]` dekorace nabízí balíček [swashbuckle. AspNetCore. anotace](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) rozšíření, která umožňují a rozšiřují metadata, schéma a parametry metadat.</span><span class="sxs-lookup"><span data-stu-id="e74cd-231">To support the `[ProducesResponseType]` decoration, the [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) package offers extensions to enable and enrich the response, schema, and parameter metadata.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="e74cd-232">Přizpůsobení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="e74cd-232">Customize the UI</span></span>

<span data-ttu-id="e74cd-233">Výchozí uživatelské rozhraní je funkční i předposíláno.</span><span class="sxs-lookup"><span data-stu-id="e74cd-233">The default UI is both functional and presentable.</span></span> <span data-ttu-id="e74cd-234">Stránky dokumentace k rozhraní API by však měly představovat vaši značku nebo motiv.</span><span class="sxs-lookup"><span data-stu-id="e74cd-234">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="e74cd-235">Branding komponent swashbuckle vyžaduje přidání prostředků k obsluze statických souborů a vytvoření struktury složek pro hostování těchto souborů.</span><span class="sxs-lookup"><span data-stu-id="e74cd-235">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="e74cd-236">Pokud cílíte .NET Framework nebo .NET Core 1. x, přidejte do projektu balíček NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) :</span><span class="sxs-lookup"><span data-stu-id="e74cd-236">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="e74cd-237">Předchozí balíček NuGet je již nainstalován, pokud cílíte na rozhraní .NET Core 2. x a použijete [Metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="e74cd-237">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="e74cd-238">Povolit middleware statických souborů:</span><span class="sxs-lookup"><span data-stu-id="e74cd-238">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="e74cd-239">Chcete-li vložit další šablony stylů CSS, přidejte je do složky *wwwroot* projektu a zadejte relativní cestu do možností middlewaru:</span><span class="sxs-lookup"><span data-stu-id="e74cd-239">To inject additional CSS stylesheets, add them to the project's *wwwroot* folder and specify the relative path in the middleware options:</span></span>

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```
