---
title: Začínáme s Swashbuckle a ASP.NET Core
author: zuckerthoben
description: Přečtěte si, jak přidat Swashbuckle do projektu ASP.NET core web API pro integraci uživatelského rozhraní Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: da848ef9c5fa85f5186d1b6f0a6111d8c8d069c4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661299"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="08ee3-103">Začínáme s Swashbuckle a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08ee3-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="08ee3-104">Podle [Shayne Boyer](https://twitter.com/spboyer) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="08ee3-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="08ee3-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="08ee3-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="08ee3-106">Swashbuckle má tři hlavní komponenty:</span><span class="sxs-lookup"><span data-stu-id="08ee3-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="08ee3-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): objektový model Swagger `SwaggerDocument` a middleware, které zveřejňují objekty jako koncové body JSON.</span><span class="sxs-lookup"><span data-stu-id="08ee3-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="08ee3-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generátor Swagger, `SwaggerDocument` který vytváří objekty přímo z vašich tras, řadičů a modelů.</span><span class="sxs-lookup"><span data-stu-id="08ee3-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="08ee3-109">Obvykle funguje tak, že v kombinaci s middlewarem koncových bodů Swaggeru automaticky zveřejňuje kód JSON pro Swagger.</span><span class="sxs-lookup"><span data-stu-id="08ee3-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="08ee3-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): vložená verze nástroje Swagger UI.</span><span class="sxs-lookup"><span data-stu-id="08ee3-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="08ee3-111">Interpretuje JSON pro Swagger v podobě propracovaného, přizpůsobitelného prostředí pro popis funkcí webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="08ee3-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="08ee3-112">Jeho součástí je i integrovaný správce testů pro veřejné metody.</span><span class="sxs-lookup"><span data-stu-id="08ee3-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="08ee3-113">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="08ee3-113">Package installation</span></span>

<span data-ttu-id="08ee3-114">Swashbuckle lze přidat s následujícími přístupy:</span><span class="sxs-lookup"><span data-stu-id="08ee3-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="08ee3-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08ee3-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="08ee3-116">Z okna **Konzoly správce balíčků:**</span><span class="sxs-lookup"><span data-stu-id="08ee3-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="08ee3-117">Přejít na **zobrazit** > další**konzolu Správce balíčků systému** **Windows** > </span><span class="sxs-lookup"><span data-stu-id="08ee3-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="08ee3-118">Přejděte do adresáře, ve kterém existuje soubor *TodoApi.csproj.*</span><span class="sxs-lookup"><span data-stu-id="08ee3-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="08ee3-119">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="08ee3-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* <span data-ttu-id="08ee3-120">V dialogovém okně **Spravovat balíčky NuGet:**</span><span class="sxs-lookup"><span data-stu-id="08ee3-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="08ee3-121">Klikněte pravým tlačítkem myši na projekt v **Průzkumníku** > řešení**Spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="08ee3-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="08ee3-122">Nastavit **zdroj balíčku** na "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="08ee3-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="08ee3-123">Ujistěte se, že je povolena možnost Zahrnout předběžnou verzi.</span><span class="sxs-lookup"><span data-stu-id="08ee3-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="08ee3-124">Do vyhledávacího pole zadejte "Swashbuckle.AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="08ee3-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="08ee3-125">Na kartě **Procházet** vyberte nejnovější balíček Swashbuckle.AspNetCore a klikněte na **Instalovat.**</span><span class="sxs-lookup"><span data-stu-id="08ee3-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="08ee3-126">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="08ee3-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="08ee3-127">Klikněte pravým tlačítkem myši na složku *Balíčky* v **panelu řešení** > **Přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="08ee3-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="08ee3-128">Nastavení rozbalovacího okna **Zdroj** v okně **Přidat balíčky** na hodnotu "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="08ee3-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="08ee3-129">Ujistěte se, že je povolena možnost Zobrazit předběžné verze balíčků.</span><span class="sxs-lookup"><span data-stu-id="08ee3-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="08ee3-130">Do vyhledávacího pole zadejte "Swashbuckle.AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="08ee3-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="08ee3-131">V podokně výsledků vyberte nejnovější balíček "Swashbuckle.AspNetCore" a klepněte na tlačítko **Přidat balíček.**</span><span class="sxs-lookup"><span data-stu-id="08ee3-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="08ee3-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="08ee3-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="08ee3-133">Spusťte následující příkaz z **integrovaného terminálu**:</span><span class="sxs-lookup"><span data-stu-id="08ee3-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="08ee3-134">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="08ee3-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="08ee3-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="08ee3-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="08ee3-136">Přidání a konfigurace middlewaru Swagger</span><span class="sxs-lookup"><span data-stu-id="08ee3-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="08ee3-137">Ve `Startup` třídě importujte následující obor názvů `OpenApiInfo` a použijte třídu:</span><span class="sxs-lookup"><span data-stu-id="08ee3-137">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="08ee3-138">Přidejte generátor Swagger do kolekce `Startup.ConfigureServices` služeb v metodě:</span><span class="sxs-lookup"><span data-stu-id="08ee3-138">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

<span data-ttu-id="08ee3-139">V `Startup.Configure` metodě povolte middleware pro obsluhu generovaného dokumentu JSON a ui Swagger:</span><span class="sxs-lookup"><span data-stu-id="08ee3-139">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

<span data-ttu-id="08ee3-140">Předchozí `UseSwaggerUI` volání metody umožňuje [middleware statického souboru](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="08ee3-140">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="08ee3-141">Pokud cílíte na rozhraní .NET Framework nebo .NET Core 1.x, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="08ee3-141">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="08ee3-142">Spusťte aplikaci `http://localhost:<port>/swagger/v1/swagger.json`a přejděte na .</span><span class="sxs-lookup"><span data-stu-id="08ee3-142">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="08ee3-143">Vygenerovaný dokument popisující koncové body se zobrazí tak, jak je znázorněno ve [specifikaci Swagger (swagger.json).](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)</span><span class="sxs-lookup"><span data-stu-id="08ee3-143">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="08ee3-144">Swagger UI lze nalézt `http://localhost:<port>/swagger`na adrese .</span><span class="sxs-lookup"><span data-stu-id="08ee3-144">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="08ee3-145">Prozkoumejte rozhraní API prostřednictvím rozhraní Swagger A začleňte ho do jiných programů.</span><span class="sxs-lookup"><span data-stu-id="08ee3-145">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="08ee3-146">Chcete-li sloužit Swagger UI v`http://localhost:<port>/`kořenovém `RoutePrefix` adresáři aplikace ( ), nastavte vlastnost na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="08ee3-146">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="08ee3-147">Pokud používáte adresáře se službou IIS nebo reverzní proxy server, `./` nastavte koncový bod Swagger na relativní cestu pomocí předpony.</span><span class="sxs-lookup"><span data-stu-id="08ee3-147">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="08ee3-148">Například, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="08ee3-148">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="08ee3-149">Použití `/swagger/v1/swagger.json` pokyn aplikace hledat soubor JSON v pravé kořenové adresy URL (plus předpona trasy, pokud je použita).</span><span class="sxs-lookup"><span data-stu-id="08ee3-149">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="08ee3-150">Můžete například namísto `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` použít `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="08ee3-150">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="08ee3-151">Přizpůsobení a rozšíření</span><span class="sxs-lookup"><span data-stu-id="08ee3-151">Customize and extend</span></span>

<span data-ttu-id="08ee3-152">Swagger poskytuje možnosti pro dokumentaci objektového modelu a přizpůsobení ui tak, aby odpovídalo motivu.</span><span class="sxs-lookup"><span data-stu-id="08ee3-152">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="08ee3-153">Do `Startup` třídy přidejte následující obory názvů:</span><span class="sxs-lookup"><span data-stu-id="08ee3-153">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="08ee3-154">Informace a popis rozhraní API</span><span class="sxs-lookup"><span data-stu-id="08ee3-154">API info and description</span></span>

<span data-ttu-id="08ee3-155">Akce konfigurace předaná metodě `AddSwaggerGen` přidává informace, jako je autor, licence a popis:</span><span class="sxs-lookup"><span data-stu-id="08ee3-155">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="08ee3-156">Swagger UI zobrazuje informace o verzi:</span><span class="sxs-lookup"><span data-stu-id="08ee3-156">The Swagger UI displays the version's information:</span></span>

![Swagger UI s informacemi o verzi: popis, autor a další odkaz](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="08ee3-158">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="08ee3-158">XML comments</span></span>

<span data-ttu-id="08ee3-159">Komentáře XML lze povolit pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="08ee3-159">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="08ee3-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08ee3-160">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="08ee3-161">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **upravit <project_name>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="08ee3-161">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="08ee3-162">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="08ee3-162">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="08ee3-163">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **příkaz Vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="08ee3-163">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="08ee3-164">Zaškrtněte **políčko Soubor dokumentace XML** v části **Výstup** na kartě **Sestavení.**</span><span class="sxs-lookup"><span data-stu-id="08ee3-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="08ee3-165">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="08ee3-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="08ee3-166">V *panelu řešení*stiskněte **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="08ee3-166">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="08ee3-167">Přejděte na **Nástroje** > **upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="08ee3-167">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="08ee3-168">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="08ee3-168">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="08ee3-169">Otevření dialogového **okna Možnosti projektu** > **kompilátor** **sestavení** ></span><span class="sxs-lookup"><span data-stu-id="08ee3-169">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="08ee3-170">Zaškrtněte políčko **Generovat dokumentaci XML** v části **Obecné možnosti.**</span><span class="sxs-lookup"><span data-stu-id="08ee3-170">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="08ee3-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="08ee3-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="08ee3-172">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="08ee3-172">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="08ee3-173">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="08ee3-173">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="08ee3-174">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="08ee3-174">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="08ee3-175">Povolení komentářů XML poskytuje ladicí informace pro nezdokumentované veřejné typy a členy.</span><span class="sxs-lookup"><span data-stu-id="08ee3-175">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="08ee3-176">Nedokumentované typy a členy jsou označeny varovnou zprávou.</span><span class="sxs-lookup"><span data-stu-id="08ee3-176">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="08ee3-177">Například následující zpráva označuje porušení kódu upozornění 1591:</span><span class="sxs-lookup"><span data-stu-id="08ee3-177">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="08ee3-178">Chcete-li potlačit upozornění pro celý projekt, definujte seznam kódů upozornění oddělených středníkem, který chcete v souboru projektu ignorovat.</span><span class="sxs-lookup"><span data-stu-id="08ee3-178">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="08ee3-179">Připojení kódy upozornění `$(NoWarn);` použije [c# výchozí hodnoty](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) příliš.</span><span class="sxs-lookup"><span data-stu-id="08ee3-179">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="08ee3-180">Chcete-li potlačit upozornění pouze pro určité členy, uzavřete kód v #pragma direktivy preprocesoru [upozornění.](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning)</span><span class="sxs-lookup"><span data-stu-id="08ee3-180">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="08ee3-181">Tento přístup je užitečný pro kód, který by neměl být vystaven prostřednictvím dokumentů rozhraní API. V následujícím příkladu je kód upozornění CS1591 `Program` ignorován pro celou třídu.</span><span class="sxs-lookup"><span data-stu-id="08ee3-181">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="08ee3-182">Vynucení kódu upozornění je obnoveno na konci definice třídy.</span><span class="sxs-lookup"><span data-stu-id="08ee3-182">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="08ee3-183">Zadejte více kódů upozornění se seznamem odděleným čárkami.</span><span class="sxs-lookup"><span data-stu-id="08ee3-183">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="08ee3-184">Nakonfigurujte Swagger tak, aby používal soubor XML, který je generován s předchozími pokyny.</span><span class="sxs-lookup"><span data-stu-id="08ee3-184">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="08ee3-185">U operačních systémů Linux nebo mimo operační systémy Windows mohou být názvy souborů a cesty rozlišována malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="08ee3-185">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="08ee3-186">Například soubor *TodoApi.XML* je platný v systému Windows, ale ne CentOS.</span><span class="sxs-lookup"><span data-stu-id="08ee3-186">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

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

<span data-ttu-id="08ee3-187">V předchozím kódu [reflection](/dotnet/csharp/programming-guide/concepts/reflection) se používá k vytvoření názvu souboru XML odpovídající ho projektu webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="08ee3-187">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="08ee3-188">Vlastnost [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) se používá k vytvoření cesty k souboru XML.</span><span class="sxs-lookup"><span data-stu-id="08ee3-188">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="08ee3-189">Některé funkce Swagger (například schémata vstupních parametrů nebo http metody a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML.</span><span class="sxs-lookup"><span data-stu-id="08ee3-189">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="08ee3-190">U většiny funkcí, konkrétně souhrnů metod a popisů parametrů a kódů odpovědí, je použití souboru XML povinné.</span><span class="sxs-lookup"><span data-stu-id="08ee3-190">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="08ee3-191">Můžete k akci přidat také komentáře uvozené třemi lomítky, a vylepšit tak Swagger UI tím, že přidáte popis do hlavičky oddílu.</span><span class="sxs-lookup"><span data-stu-id="08ee3-191">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="08ee3-192">Nad `Delete` akci přidejte [ \<souhrnný prvek>:](/dotnet/csharp/programming-guide/xmldoc/summary)</span><span class="sxs-lookup"><span data-stu-id="08ee3-192">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="08ee3-193">Swagger ui zobrazí vnitřní text prvku předchozího `<summary>` kódu:</span><span class="sxs-lookup"><span data-stu-id="08ee3-193">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Swagger UI zobrazující komentář XML 'Odstraní konkrétní TodoItem.'](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="08ee3-196">UI je řízeno generované schéma JSON:</span><span class="sxs-lookup"><span data-stu-id="08ee3-196">The UI is driven by the generated JSON schema:</span></span>

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

<span data-ttu-id="08ee3-197">Přidejte [ \<poznámky](/dotnet/csharp/programming-guide/xmldoc/remarks)>`Create` prvek do dokumentace metody akce.</span><span class="sxs-lookup"><span data-stu-id="08ee3-197">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="08ee3-198">Doplňuje informace uvedené `<summary>` v prvku a poskytuje robustnější Swagger uI.</span><span class="sxs-lookup"><span data-stu-id="08ee3-198">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="08ee3-199">Obsah `<remarks>` prvku se může skládat z textu, JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="08ee3-199">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="08ee3-200">Všimněte si vylepšení ui s těmito dalšími komentáři:</span><span class="sxs-lookup"><span data-stu-id="08ee3-200">Notice the UI enhancements with these additional comments:</span></span>

![Swagger UI s dalšíkomentáře zobrazeny](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="08ee3-202">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="08ee3-202">Data annotations</span></span>

<span data-ttu-id="08ee3-203">Označte model atributy, které se nacházejí v oboru názvů [System.ComponentModel.DataAnnotations,](/dotnet/api/system.componentmodel.dataannotations) které pomáhají řídit součásti uživatelského uživatelského uživatelského nastavení Swagger.</span><span class="sxs-lookup"><span data-stu-id="08ee3-203">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="08ee3-204">Přidejte `[Required]` atribut `Name` do vlastnosti třídy: `TodoItem`</span><span class="sxs-lookup"><span data-stu-id="08ee3-204">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="08ee3-205">Přítomnost tohoto atributu změní chování ui a změní základní schéma JSON:</span><span class="sxs-lookup"><span data-stu-id="08ee3-205">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="08ee3-206">Přidejte `[Produces("application/json")]` atribut do řadiče rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="08ee3-206">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="08ee3-207">Jeho účelem je prohlásit, že akce správce podporují typ obsahu odpovědi *aplikace/json*:</span><span class="sxs-lookup"><span data-stu-id="08ee3-207">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="08ee3-208">Rozevírací nabídky **Typ obsahu odpovědi** vybere tento typ obsahu jako výchozí pro akce GET řadiče:</span><span class="sxs-lookup"><span data-stu-id="08ee3-208">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Swagger UI s výchozím typem obsahu odpovědi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="08ee3-210">S tím, jak se zvyšuje využití datových anotací ve webovém rozhraní API, se stránky nápovědy uživatelského rozhraní a rozhraní API stávají popisnějšími a užitečnějšími.</span><span class="sxs-lookup"><span data-stu-id="08ee3-210">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="08ee3-211">Popsat typy odpovědí</span><span class="sxs-lookup"><span data-stu-id="08ee3-211">Describe response types</span></span>

<span data-ttu-id="08ee3-212">Vývojáři, kteří konzumují webové&mdash;rozhraní API, se nejvíce zajímají o to, co je vráceno konkrétně typy odpovědí a kódy chyb (pokud nejsou standardní).</span><span class="sxs-lookup"><span data-stu-id="08ee3-212">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="08ee3-213">Typy odpovědí a kódy chyb jsou označeny v poznámkách XML a datových poznámkách.</span><span class="sxs-lookup"><span data-stu-id="08ee3-213">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="08ee3-214">Akce `Create` vrátí stavový kód HTTP 201 na úspěch.</span><span class="sxs-lookup"><span data-stu-id="08ee3-214">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="08ee3-215">Stavový kód HTTP 400 je vrácen, pokud je zaúčtovaný text požadavku null.</span><span class="sxs-lookup"><span data-stu-id="08ee3-215">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="08ee3-216">Bez řádné dokumentace v ui Swagger, spotřebitel postrádá znalosti o těchto očekávaných výsledků.</span><span class="sxs-lookup"><span data-stu-id="08ee3-216">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="08ee3-217">Tento problém opravte přidáním zvýrazněných řádků v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="08ee3-217">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="08ee3-218">Swagger UI nyní jasně dokumentuje očekávané kódy odpovědi HTTP:</span><span class="sxs-lookup"><span data-stu-id="08ee3-218">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Swagger UI zobrazující popis třídy odezvy POST "Vrátí nově vytvořenou položku todo" a "400 - Pokud je položka null" pro stavový kód a důvod v části Zprávy odpovědi](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="08ee3-220">V ASP.NET Jádrem 2.2 nebo novějším lze konvence použít jako `[ProducesResponseType]`alternativu k explicitnímu zdobení jednotlivých akcí pomocí .</span><span class="sxs-lookup"><span data-stu-id="08ee3-220">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="08ee3-221">Další informace naleznete v tématu <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="08ee3-221">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="08ee3-222">Přizpůsobení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="08ee3-222">Customize the UI</span></span>

<span data-ttu-id="08ee3-223">Základní ui je funkční a reprezentativní.</span><span class="sxs-lookup"><span data-stu-id="08ee3-223">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="08ee3-224">Stránky dokumentace rozhraní API by však měly představovat vaši značku nebo motiv.</span><span class="sxs-lookup"><span data-stu-id="08ee3-224">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="08ee3-225">Branding komponent Swashbuckle vyžaduje přidání prostředků pro poskytování statických souborů a vytváření struktury složek pro hostování těchto souborů.</span><span class="sxs-lookup"><span data-stu-id="08ee3-225">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="08ee3-226">Pokud cílíte na rozhraní .NET Framework nebo .NET Core 1.x, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet:</span><span class="sxs-lookup"><span data-stu-id="08ee3-226">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="08ee3-227">Předchozí balíček NuGet je již nainstalován, pokud cílení .NET Core 2.x a pomocí [metabalíčku](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="08ee3-227">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="08ee3-228">Povolit middleware statického souboru:</span><span class="sxs-lookup"><span data-stu-id="08ee3-228">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="08ee3-229">Získejte obsah složky *dist* z [úložiště Swagger UI GitHub](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span><span class="sxs-lookup"><span data-stu-id="08ee3-229">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="08ee3-230">Tato složka obsahuje potřebné prostředky pro stránku swagger ui.</span><span class="sxs-lookup"><span data-stu-id="08ee3-230">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="08ee3-231">Vytvořte složku *wwwroot/swagger/ui* a zkopírujte do ní obsah složky *dist.*</span><span class="sxs-lookup"><span data-stu-id="08ee3-231">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="08ee3-232">Vytvořte soubor *custom.css* v *wwwroot/swagger/ui*s následujícím css pro přizpůsobení záhlaví stránky:</span><span class="sxs-lookup"><span data-stu-id="08ee3-232">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="08ee3-233">Odkaz *na custom.css* v souboru *index.html* uvnitř složky ui, po všech dalších souborech CSS:</span><span class="sxs-lookup"><span data-stu-id="08ee3-233">Reference *custom.css* in the *index.html* file inside ui folder, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="08ee3-234">Přejděte na stránku `http://localhost:<port>/swagger/ui/index.html` *index.html* na adrese .</span><span class="sxs-lookup"><span data-stu-id="08ee3-234">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="08ee3-235">Zadejte `https://localhost:<port>/swagger/v1/swagger.json` do textového pole záhlaví a klikněte na tlačítko **Prozkoumat.**</span><span class="sxs-lookup"><span data-stu-id="08ee3-235">Enter `https://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="08ee3-236">Výsledná stránka vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="08ee3-236">The resulting page looks as follows:</span></span>

![Swagger UI s vlastním názvem záhlaví](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="08ee3-238">Je tu mnohem víc, co můžete udělat s stránkou.</span><span class="sxs-lookup"><span data-stu-id="08ee3-238">There's much more you can do with the page.</span></span> <span data-ttu-id="08ee3-239">Podívejte se na úplné možnosti pro prostředky ui v [úložišti Swagger UI GitHub](https://github.com/swagger-api/swagger-ui).</span><span class="sxs-lookup"><span data-stu-id="08ee3-239">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
