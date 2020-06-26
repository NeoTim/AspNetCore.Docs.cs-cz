---
title: Začínáme s swashbuckle a ASP.NET Core
author: zuckerthoben
description: Naučte se, jak přidat swashbuckle do projektu webového rozhraní API ASP.NET Core pro integraci uživatelského rozhraní Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 00b42243e45c97c12ad2a4f97dff4a17b7bbb002
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403401"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="b7fd8-103">Začínáme s swashbuckle a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7fd8-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="b7fd8-104">Od [Shayne Boyer](https://twitter.com/spboyer) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b7fd8-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="b7fd8-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7fd8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b7fd8-106">Swashbuckle má tři hlavní komponenty:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="b7fd8-107">[Swashbuckle. AspNetCore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): objektový model a middleware pro objekty Swagger k vystavení `SwaggerDocument` objektů jako koncových bodů JSON.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="b7fd8-108">[Swashbuckle. AspNetCore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generátor Swagger, který vytváří `SwaggerDocument` objekty přímo z vašich tras, řadičů a modelů.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="b7fd8-109">Obvykle funguje tak, že v kombinaci s middlewarem koncových bodů Swaggeru automaticky zveřejňuje kód JSON pro Swagger.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="b7fd8-110">[Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): vložená verze nástroje uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="b7fd8-111">Interpretuje JSON pro Swagger v podobě propracovaného, přizpůsobitelného prostředí pro popis funkcí webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="b7fd8-112">Jeho součástí je i integrovaný správce testů pro veřejné metody.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="b7fd8-113">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="b7fd8-113">Package installation</span></span>

<span data-ttu-id="b7fd8-114">Swashbuckle je možné přidat s následujícími přístupy:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="b7fd8-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7fd8-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7fd8-116">V okně **konzoly Správce balíčků** :</span><span class="sxs-lookup"><span data-stu-id="b7fd8-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="b7fd8-117">Přejít k **zobrazení**  >  **jiné**  >  **konzoly Správce balíčků** Windows</span><span class="sxs-lookup"><span data-stu-id="b7fd8-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="b7fd8-118">Přejděte do adresáře, ve kterém existuje soubor *TodoApi. csproj.*</span><span class="sxs-lookup"><span data-stu-id="b7fd8-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="b7fd8-119">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* <span data-ttu-id="b7fd8-120">V dialogovém okně **Spravovat balíčky NuGet** :</span><span class="sxs-lookup"><span data-stu-id="b7fd8-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="b7fd8-121">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**  >  **Spravovat balíčky NuGet** .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="b7fd8-122">Nastavte **zdroj balíčku** na "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="b7fd8-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="b7fd8-123">Ujistěte se, že je povolená možnost zahrnout předběžné verze.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="b7fd8-124">Do vyhledávacího pole zadejte "swashbuckle. AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="b7fd8-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="b7fd8-125">Na kartě **Procházet** vyberte nejnovější balíček "swashbuckle. AspNetCore" a klikněte na **instalovat** .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7fd8-126">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="b7fd8-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b7fd8-127">Klikněte pravým tlačítkem na složku *balíčky* v **oblast řešení**  >  **Přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="b7fd8-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="b7fd8-128">Nastavte rozevírací seznam pro **zdroj** okna **Přidat balíčky** na "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="b7fd8-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="b7fd8-129">Ujistěte se, že je povolená možnost Zobrazit balíčky předběžných verzí.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="b7fd8-130">Do vyhledávacího pole zadejte "swashbuckle. AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="b7fd8-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="b7fd8-131">V podokně výsledků vyberte nejnovější balíček "swashbuckle. AspNetCore" a klikněte na **Přidat balíček** .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="b7fd8-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7fd8-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b7fd8-133">Z **integrovaného terminálu**spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="b7fd8-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7fd8-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b7fd8-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="b7fd8-136">Přidat a nakonfigurovat middleware Swagger</span><span class="sxs-lookup"><span data-stu-id="b7fd8-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="b7fd8-137">Ve `Startup` třídě importujte následující obor názvů pro použití `OpenApiInfo` třídy:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-137">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="b7fd8-138">Přidejte generátor Swagger do kolekce služeb v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-138">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

<span data-ttu-id="b7fd8-139">V `Startup.Configure` metodě povolte middleware pro poskytování generovaného dokumentu JSON a uživatelského rozhraní Swagger:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-139">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

<span data-ttu-id="b7fd8-140">Předchozí `UseSwaggerUI` volání metody povoluje [middleware statických souborů](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="b7fd8-140">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="b7fd8-141">Pokud cílíte .NET Framework nebo .NET Core 1. x, přidejte do projektu balíček NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-141">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="b7fd8-142">Spusťte aplikaci a přejděte na adresu `http://localhost:<port>/swagger/v1/swagger.json` .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-142">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="b7fd8-143">Vygenerovaný dokument popisující koncové body se zobrazí, jak je znázorněno ve [specifikaci Swagger (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="b7fd8-143">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="b7fd8-144">Uživatelské rozhraní Swagger najdete na adrese `http://localhost:<port>/swagger` .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-144">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="b7fd8-145">Prozkoumejte rozhraní API prostřednictvím uživatelského rozhraní Swagger a zahrňte je do jiných programů.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-145">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="b7fd8-146">Pro obsluhu uživatelského rozhraní Swagger v kořenovém adresáři aplikace ( `http://localhost:<port>/` ) nastavte `RoutePrefix` vlastnost na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-146">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="b7fd8-147">Pokud používáte adresáře se službou IIS nebo reverzním proxy serverem, nastavte koncový bod Swagger na relativní cestu pomocí `./` předpony.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-147">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="b7fd8-148">Například, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-148">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="b7fd8-149">Pomocí nástroje `/swagger/v1/swagger.json` instruuje aplikaci, aby hledala soubor JSON na skutečném kořenu adresy URL (plus předpona trasy, pokud se používá).</span><span class="sxs-lookup"><span data-stu-id="b7fd8-149">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="b7fd8-150">Můžete například namísto `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` použít `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-150">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="b7fd8-151">Přizpůsobení a rozšiřování</span><span class="sxs-lookup"><span data-stu-id="b7fd8-151">Customize and extend</span></span>

<span data-ttu-id="b7fd8-152">Swagger poskytuje možnosti pro dokumentaci k objektovému modelu a přizpůsobení uživatelského rozhraní tak, aby odpovídalo vašemu motivu.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-152">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="b7fd8-153">Do `Startup` třídy přidejte následující obory názvů:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-153">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="b7fd8-154">Informace a popis rozhraní API</span><span class="sxs-lookup"><span data-stu-id="b7fd8-154">API info and description</span></span>

<span data-ttu-id="b7fd8-155">Akce konfigurace předaná `AddSwaggerGen` metodě přidá informace, jako je autor, licence a popis:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-155">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="b7fd8-156">Uživatelské rozhraní Swagger zobrazuje informace o verzi:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-156">The Swagger UI displays the version's information:</span></span>

![Uživatelské rozhraní Swagger s informacemi o verzi: Popis, autor a zobrazit další odkaz](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="b7fd8-158">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="b7fd8-158">XML comments</span></span>

<span data-ttu-id="b7fd8-159">Komentáře XML lze povolit pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-159">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="b7fd8-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7fd8-160">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="b7fd8-161">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **upravit <PROJECT_NAME>. csproj**.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-161">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="b7fd8-162">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="b7fd8-162">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="b7fd8-163">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-163">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="b7fd8-164">V části **výstup** na kartě **sestavení** zaškrtněte políčko **soubor dokumentace XML** .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b7fd8-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="b7fd8-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="b7fd8-166">V *oblast řešení*stiskněte **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-166">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="b7fd8-167">Přejděte do **nástroje**  >  **Upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-167">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="b7fd8-168">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="b7fd8-168">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="b7fd8-169">Otevření dialogového okna **Možnosti projektu** > **Build** > **kompilátor** sestavení</span><span class="sxs-lookup"><span data-stu-id="b7fd8-169">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="b7fd8-170">V části **Obecné možnosti** zaškrtněte políčko **generovat dokumentaci XML** .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-170">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="b7fd8-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7fd8-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b7fd8-172">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="b7fd8-172">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="b7fd8-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b7fd8-173">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b7fd8-174">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="b7fd8-174">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="b7fd8-175">Povolení komentářů XML poskytuje ladicí informace pro nedokumentované veřejné typy a členy.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-175">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="b7fd8-176">Nedokumentované typy a členy jsou označeny varovnou zprávou.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-176">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="b7fd8-177">Například následující zpráva indikuje porušení kódu upozornění 1591:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-177">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="b7fd8-178">Chcete-li potlačit upozornění v rámci projektu, definujte seznam kódů upozornění oddělených středníkem, které mají být v souboru projektu ignorovány.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-178">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="b7fd8-179">Připojení kódů upozornění k `$(NoWarn);` použití [výchozích hodnot jazyka C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-179">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="b7fd8-180">Chcete-li potlačit upozornění pouze pro konkrétní členy, uveďte kód v direktivách preprocesoru [upozornění #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-180">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="b7fd8-181">Tento přístup je užitečný pro kód, který by neměl být zveřejněn prostřednictvím dokumentace rozhraní API. V následujícím příkladu je kód upozornění CS1591 ignorován pro celou `Program` třídu.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-181">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="b7fd8-182">Vynucování kódu upozornění je obnoveno při zavření definice třídy.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-182">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="b7fd8-183">Zadejte více kódů upozornění se seznamem odděleným čárkami.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-183">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="b7fd8-184">Nakonfigurujte Swagger pro použití souboru XML, který je vygenerovaný předchozími pokyny.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-184">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="b7fd8-185">Pro operační systémy Linux a jiné než Windows můžou názvy souborů a cesty rozlišovat velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-185">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="b7fd8-186">Například soubor *TodoApi.XML* je platný v systému Windows, ale nikoli CentOS.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-186">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

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

<span data-ttu-id="b7fd8-187">V předchozím kódu se [reflexe](/dotnet/csharp/programming-guide/concepts/reflection) používá k sestavení názvu souboru XML, který odpovídá tomuto projektu webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-187">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="b7fd8-188">Vlastnost [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) se používá k vytvoření cesty k souboru XML.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-188">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="b7fd8-189">Některé funkce Swagger (například schémat vstupních parametrů nebo metody HTTP a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-189">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="b7fd8-190">Pro většinu funkcí, konkrétně souhrny metod a popisy parametrů a kódů odpovědí, je použití souboru XML povinné.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-190">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="b7fd8-191">Můžete k akci přidat také komentáře uvozené třemi lomítky, a vylepšit tak Swagger UI tím, že přidáte popis do hlavičky oddílu.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-191">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="b7fd8-192">Přidejte [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element nad `Delete` akci:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-192">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="b7fd8-193">Uživatelské rozhraní Swagger zobrazí vnitřní text elementu předchozí kód `<summary>` :</span><span class="sxs-lookup"><span data-stu-id="b7fd8-193">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Uživatelské rozhraní Swagger, které zobrazuje komentář XML, odstraní konkrétní TodoItem.](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="b7fd8-196">Uživatelské rozhraní je založené na generovaném schématu JSON:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-196">The UI is driven by the generated JSON schema:</span></span>

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

<span data-ttu-id="b7fd8-197">Přidejte [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element do dokumentace k `Create` metodě akce.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-197">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="b7fd8-198">Doplňuje informace zadané v `<summary>` prvku a poskytuje robustnější uživatelské rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-198">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="b7fd8-199">`<remarks>`Obsah elementu se může skládat z textu, JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-199">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="b7fd8-200">Všimněte si vylepšení uživatelského rozhraní pomocí těchto dalších komentářů:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-200">Notice the UI enhancements with these additional comments:</span></span>

![Uživatelské rozhraní Swagger s dalšími zobrazenými komentáři](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="b7fd8-202">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="b7fd8-202">Data annotations</span></span>

<span data-ttu-id="b7fd8-203">Označte model s atributy, které se nacházejí v oboru názvů [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , aby bylo možné lépe řídit součásti uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-203">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="b7fd8-204">Přidejte `[Required]` atribut do `Name` vlastnosti `TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-204">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="b7fd8-205">Přítomnost tohoto atributu mění chování uživatelského rozhraní a mění základní schéma JSON:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-205">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="b7fd8-206">Přidejte `[Produces("application/json")]` atribut do kontroleru rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-206">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="b7fd8-207">Jeho účelem je deklarovat, že akce kontroleru podporují typ obsahu odpovědi *Application/JSON*:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-207">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="b7fd8-208">Rozevírací seznam **typ obsahu odpovědi** pro akce GET kontroleru vybere tento typ obsahu jako výchozí:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-208">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Uživatelské rozhraní Swagger s výchozím typem obsahu odpovědi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="b7fd8-210">Díky nárůstu využití datových poznámek ve webovém rozhraní API se stránky s nápovědu pro uživatelské rozhraní a rozhraní API stanou výstižnější a užitečnější.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-210">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="b7fd8-211">Popis typů odpovědí</span><span class="sxs-lookup"><span data-stu-id="b7fd8-211">Describe response types</span></span>

<span data-ttu-id="b7fd8-212">Vývojáři, kteří využívají webové rozhraní API, mají největší obavy s tím, co vrací &mdash; konkrétně typy odpovědí a kódy chyb (Pokud ne Standard).</span><span class="sxs-lookup"><span data-stu-id="b7fd8-212">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="b7fd8-213">Typy odpovědí a kódy chyb jsou označeny v komentářích XML a datových anotacích.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-213">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="b7fd8-214">`Create`Akce vrátí stavový kód HTTP 201 při úspěchu.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-214">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="b7fd8-215">Stavový kód HTTP 400 se vrátí, když text odeslaného požadavku má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-215">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="b7fd8-216">Bez správné dokumentace v uživatelském rozhraní Swagger nemá spotřebitel znalosti o těchto očekávaných výsledcích.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-216">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="b7fd8-217">Opravte tento problém tak, že přidáte zvýrazněné řádky v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-217">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="b7fd8-218">Uživatelské rozhraní Swagger teď jasně klade očekávané kódy odpovědí HTTP:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-218">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Uživatelské rozhraní Swagger, které zobrazuje popis třídy odpovědí POST, vrátí nově vytvořenou položku TODO ' a ' 400 – Pokud má položka hodnotu null pro stavový kód a důvod v rámci zpráv odpovědí.](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b7fd8-220">V ASP.NET Core 2,2 nebo novějších lze konvence použít jako alternativu k explicitnímu Upraveníí jednotlivých akcí `[ProducesResponseType]` .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-220">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="b7fd8-221">Další informace naleznete v tématu <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-221">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="b7fd8-222">Přizpůsobení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="b7fd8-222">Customize the UI</span></span>

<span data-ttu-id="b7fd8-223">Uložené uživatelské rozhraní je funkční i předposílánelné.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-223">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="b7fd8-224">Stránky dokumentace k rozhraní API by však měly představovat vaši značku nebo motiv.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-224">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="b7fd8-225">Branding komponent swashbuckle vyžaduje přidání prostředků k obsluze statických souborů a vytvoření struktury složek pro hostování těchto souborů.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-225">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="b7fd8-226">Pokud cílíte .NET Framework nebo .NET Core 1. x, přidejte do projektu balíček NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) :</span><span class="sxs-lookup"><span data-stu-id="b7fd8-226">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="b7fd8-227">Předchozí balíček NuGet je již nainstalován, pokud cílíte na rozhraní .NET Core 2. x a použijete [Metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="b7fd8-227">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="b7fd8-228">Povolit middleware statických souborů:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-228">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="b7fd8-229">Získejte obsah složky *DIST* z [úložiště GitHub uživatelského rozhraní Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span><span class="sxs-lookup"><span data-stu-id="b7fd8-229">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="b7fd8-230">Tato složka obsahuje potřebné prostředky pro stránku uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-230">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="b7fd8-231">Vytvořte složku *wwwroot/Swagger/UI* a zkopírujte do ní obsah složky *DIST* .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-231">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="b7fd8-232">Vytvořte *vlastní soubor. CSS* v *wwwroot/Swagger/uživatelském rozhraní*s následující šablonou stylů CSS pro přizpůsobení záhlaví stránky:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-232">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="b7fd8-233">Odkaz na *vlastní soubor. CSS* v souboru *index.html* ve složce uživatelského rozhraní za všechny ostatní soubory CSS:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-233">Reference *custom.css* in the *index.html* file inside ui folder, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="b7fd8-234">Přejděte na stránku *index.html* na adrese `http://localhost:<port>/swagger/ui/index.html` .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-234">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="b7fd8-235">`https://localhost:<port>/swagger/v1/swagger.json`Do textového pole záhlaví zadejte a klikněte na tlačítko **prozkoumat** .</span><span class="sxs-lookup"><span data-stu-id="b7fd8-235">Enter `https://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="b7fd8-236">Výsledná stránka vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="b7fd8-236">The resulting page looks as follows:</span></span>

![Uživatelské rozhraní Swagger s vlastním nadpisem záhlaví](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="b7fd8-238">Stránka může dělat mnohem víc.</span><span class="sxs-lookup"><span data-stu-id="b7fd8-238">There's much more you can do with the page.</span></span> <span data-ttu-id="b7fd8-239">Podívejte se na úplné možnosti prostředků uživatelského rozhraní v [úložišti GitHub uživatelského rozhraní Swagger](https://github.com/swagger-api/swagger-ui).</span><span class="sxs-lookup"><span data-stu-id="b7fd8-239">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
