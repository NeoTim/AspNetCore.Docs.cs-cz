---
title: Začínáme s Swashbuckle a ASP.NET Core
author: zuckerthoben
description: Zjistěte, jak přidat do projektu ASP.NET Core webové rozhraní API integrovat uživatelské rozhraní Swagger Swashbuckle.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 9229b4536c3d5090e640de71357c728ddbd5dcc3
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862340"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="301d2-103">Začínáme s Swashbuckle a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="301d2-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="301d2-104">Podle [Shayne Boyer](https://twitter.com/spboyer) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="301d2-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="301d2-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="301d2-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="301d2-106">Existují tři hlavní komponenty pro Swashbuckle:</span><span class="sxs-lookup"><span data-stu-id="301d2-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="301d2-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): Swagger objektový model a middlewarem ke zveřejnění `SwaggerDocument` objekty jako koncové body JSON.</span><span class="sxs-lookup"><span data-stu-id="301d2-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="301d2-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): Generátor Swagger, který vytváří `SwaggerDocument` objekty přímo z tras, kontrolerů a modely.</span><span class="sxs-lookup"><span data-stu-id="301d2-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="301d2-109">Obvykle se zkombinuje s middlewarem koncového bodu Swaggeru k automaticky vystavení dat JSON pro Swagger.</span><span class="sxs-lookup"><span data-stu-id="301d2-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="301d2-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): embedded verze nástroje pro uživatelské rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="301d2-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="301d2-111">Interpretuje JSON pro Swagger k vytváření bohatých a přizpůsobitelných prostředí pro popis funkce webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="301d2-111">It interprets Swagger JSON to build a rich, customizable experience for describing the Web API functionality.</span></span> <span data-ttu-id="301d2-112">Zahrnuje integrované testovací postroje pro veřejné metody.</span><span class="sxs-lookup"><span data-stu-id="301d2-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="301d2-113">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="301d2-113">Package installation</span></span>

<span data-ttu-id="301d2-114">Swashbuckle lze přidat pomocí následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="301d2-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="301d2-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="301d2-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="301d2-116">Z **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="301d2-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="301d2-117">Přejděte na **zobrazení** > **jiných Windows** > **Konzola správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="301d2-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="301d2-118">Přejděte do adresáře, ve kterém *TodoApi.csproj* soubor existuje</span><span class="sxs-lookup"><span data-stu-id="301d2-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="301d2-119">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="301d2-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* <span data-ttu-id="301d2-120">Z **spravovat balíčky NuGet** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="301d2-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="301d2-121">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="301d2-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="301d2-122">Nastavte **zdroj balíčku** do "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="301d2-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="301d2-123">Do vyhledávacího pole zadejte "Swashbuckle.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="301d2-123">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="301d2-124">Vyberte balíček "Swashbuckle.AspNetCore" z **Procházet** kartě a klikněte na tlačítko **instalace**</span><span class="sxs-lookup"><span data-stu-id="301d2-124">Select the "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="301d2-125">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="301d2-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="301d2-126">Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="301d2-126">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="301d2-127">Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="301d2-127">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="301d2-128">Do vyhledávacího pole zadejte "Swashbuckle.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="301d2-128">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="301d2-129">Vyberte v podokně výsledků "Swashbuckle.AspNetCore" balíček a klikněte na tlačítko **přidat balíček**</span><span class="sxs-lookup"><span data-stu-id="301d2-129">Select the "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="301d2-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="301d2-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="301d2-131">Spuštěním následujícího příkazu z **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="301d2-131">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="301d2-132">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="301d2-132">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="301d2-133">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="301d2-133">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="301d2-134">Přidejte a nakonfigurujte Swagger middleware</span><span class="sxs-lookup"><span data-stu-id="301d2-134">Add and configure Swagger middleware</span></span>

<span data-ttu-id="301d2-135">Přidání generátoru Swagger do kolekce služby `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="301d2-135">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

<span data-ttu-id="301d2-136">Importovat následující obor názvů pro použití `Info` třídy:</span><span class="sxs-lookup"><span data-stu-id="301d2-136">Import the following namespace to use the `Info` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="301d2-137">V `Startup.Configure` metoda, povolí middleware pro poskytování generované dokumentů JSON a uživatelské rozhraní Swagger:</span><span class="sxs-lookup"><span data-stu-id="301d2-137">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

<span data-ttu-id="301d2-138">Předchozí `UseSwaggerUI` umožňuje volání metody [Middleware statické soubory](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="301d2-138">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="301d2-139">Pokud je zaměřen na rozhraní .NET Framework nebo .NET Core 1.x, přidejte [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="301d2-139">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="301d2-140">Spusťte aplikaci a přejděte do `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="301d2-140">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="301d2-141">Vygenerovaný dokument popisující koncových bodů se zobrazí, jak je znázorněno v [Swagger specifikace (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="301d2-141">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="301d2-142">Uživatelské rozhraní Swagger lze nalézt v `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="301d2-142">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="301d2-143">Prozkoumejte rozhraní API přes uživatelské rozhraní Swagger a začlení jej v jiných aplikacích.</span><span class="sxs-lookup"><span data-stu-id="301d2-143">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="301d2-144">K poskytování uživatelského rozhraní Swagger kořenové aplikace (`http://localhost:<port>/`), můžete nastavit `RoutePrefix` vlastnost na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="301d2-144">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="301d2-145">Pokud do relativní cesty pomocí adresáře pomocí služby IIS nebo reverzního proxy serveru, nastavení koncového bodu Swaggeru `./` předponu.</span><span class="sxs-lookup"><span data-stu-id="301d2-145">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="301d2-146">Například `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="301d2-146">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="301d2-147">Pomocí `/swagger/v1/swagger.json` Instruuje aplikaci upravovat pro soubor JSON true kořenové adresy URL (plus předponu trasy, pokud se používá).</span><span class="sxs-lookup"><span data-stu-id="301d2-147">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="301d2-148">Například použít `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` místo `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="301d2-148">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="301d2-149">Přizpůsobení a rozšíření</span><span class="sxs-lookup"><span data-stu-id="301d2-149">Customize and extend</span></span>

<span data-ttu-id="301d2-150">Swagger poskytuje možnosti pro dokumentace objektového modelu a přizpůsobení uživatelského rozhraní tak, aby odpovídala motivu.</span><span class="sxs-lookup"><span data-stu-id="301d2-150">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="301d2-151">Informace o rozhraní API a popis</span><span class="sxs-lookup"><span data-stu-id="301d2-151">API info and description</span></span>

<span data-ttu-id="301d2-152">Konfigurace akce předán `AddSwaggerGen` přidá informace, jako je vytváření, licence a popis metody:</span><span class="sxs-lookup"><span data-stu-id="301d2-152">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="301d2-153">Uživatelské rozhraní Swagger zobrazí informace na verzi:</span><span class="sxs-lookup"><span data-stu-id="301d2-153">The Swagger UI displays the version's information:</span></span>

![Uživatelské rozhraní swagger s informací o verzi: popis, vytvářet a další odkaz](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="301d2-155">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="301d2-155">XML comments</span></span>

<span data-ttu-id="301d2-156">Komentáře XML se dá nastavit pomocí následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="301d2-156">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="301d2-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="301d2-157">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="301d2-158">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **upravit < project_name > .csproj**.</span><span class="sxs-lookup"><span data-stu-id="301d2-158">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="301d2-159">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="301d2-159">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="301d2-160">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="301d2-160">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="301d2-161">Zkontrolujte **soubor dokumentace XML** pole v rámci **výstup** část **sestavení** kartu.</span><span class="sxs-lookup"><span data-stu-id="301d2-161">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="301d2-162">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="301d2-162">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="301d2-163">Z *oblasti řešení*, stiskněte klávesu **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="301d2-163">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="301d2-164">Přejděte do **nástroje** > **upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="301d2-164">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="301d2-165">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="301d2-165">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="301d2-166">Otevřít **možnosti projektu** dialogového okna > **sestavení** > **kompilátoru**</span><span class="sxs-lookup"><span data-stu-id="301d2-166">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="301d2-167">Zkontrolujte **generovat dokumentaci xml** pole v rámci **Obecné možnosti** oddílu</span><span class="sxs-lookup"><span data-stu-id="301d2-167">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="301d2-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="301d2-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="301d2-169">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="301d2-169">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="301d2-170">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="301d2-170">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="301d2-171">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="301d2-171">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="301d2-172">Povolení komentáře XML poskytuje informace o ladění pro nedokumentované veřejné typy a členy.</span><span class="sxs-lookup"><span data-stu-id="301d2-172">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="301d2-173">Nezdokumentovaný typy a členy jsou označeny upozornění.</span><span class="sxs-lookup"><span data-stu-id="301d2-173">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="301d2-174">Například následující zpráva označuje porušení kód upozornění. 1591:</span><span class="sxs-lookup"><span data-stu-id="301d2-174">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="301d2-175">Chcete-li potlačit upozornění na úrovni projektu, definujte středníkem oddělený seznam kódů upozornění v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="301d2-175">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="301d2-176">Přidávání kódů upozornění, aby `$(NoWarn);` příliš použije výchozí hodnoty jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="301d2-176">Appending the warning codes to `$(NoWarn);` applies the C# default values too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="301d2-177">Potlačit upozornění jenom pro konkrétní členy, uzavřete kód v [varování #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) direktivy preprocesoru.</span><span class="sxs-lookup"><span data-stu-id="301d2-177">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="301d2-178">Tento přístup je užitečný pro kód, který by neměly být vystaveny prostřednictvím dokumentace rozhraní API. V následujícím příkladu se ignoruje kód upozornění CS1591 pro celou `Program` třídy.</span><span class="sxs-lookup"><span data-stu-id="301d2-178">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="301d2-179">Vynucení kód upozornění je obnovena na konci definice třídy.</span><span class="sxs-lookup"><span data-stu-id="301d2-179">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="301d2-180">Zadejte více kódů upozornění s čárkami oddělený seznam.</span><span class="sxs-lookup"><span data-stu-id="301d2-180">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="301d2-181">Nakonfigurujte Swagger pro použití generovaného souboru XML.</span><span class="sxs-lookup"><span data-stu-id="301d2-181">Configure Swagger to use the generated XML file.</span></span> <span data-ttu-id="301d2-182">Pro operační systémy než Windows nebo Linuxem názvů a cest souborů lze malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="301d2-182">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="301d2-183">Například *TodoApi.XML* souboru je platný pro Windows, ale ne CentOS.</span><span class="sxs-lookup"><span data-stu-id="301d2-183">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="301d2-184">V předchozím kódu [reflexe](/dotnet/csharp/programming-guide/concepts/reflection) sloužící k sestavení, projekt webového rozhraní API odpovídající název souboru XML.</span><span class="sxs-lookup"><span data-stu-id="301d2-184">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the Web API project.</span></span> <span data-ttu-id="301d2-185">[AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory#System_AppContext_BaseDirectory) vlastnost se používá ke konstrukci cestu k souboru XML.</span><span class="sxs-lookup"><span data-stu-id="301d2-185">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory#System_AppContext_BaseDirectory) property is used to construct a path to the XML file.</span></span>

<span data-ttu-id="301d2-186">Přidávání komentářů třemi lomítky akci rozšiřuje uživatelské rozhraní Swagger tak, že přidáte popis hlavičku oddílu.</span><span class="sxs-lookup"><span data-stu-id="301d2-186">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="301d2-187">Přidat [ \<summary >](/dotnet/csharp/programming-guide/xmldoc/summary) element výše `Delete` akce:</span><span class="sxs-lookup"><span data-stu-id="301d2-187">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="301d2-188">Vnitřní text předchozí kód zobrazí uživatelské rozhraní Swagger `<summary>` element:</span><span class="sxs-lookup"><span data-stu-id="301d2-188">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Zobrazuje komentář XML odstraní konkrétní TodoItem. uživatelské rozhraní swagger](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="301d2-191">Generované schéma JSON vychází uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="301d2-191">The UI is driven by the generated JSON schema:</span></span>

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

<span data-ttu-id="301d2-192">Přidat [ \<Poznámky >](/dotnet/csharp/programming-guide/xmldoc/remarks) elementu `Create` dokumentaci metody akce.</span><span class="sxs-lookup"><span data-stu-id="301d2-192">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="301d2-193">Doplňující informace uvedené v `<summary>` elementu a poskytuje výkonnější uživatelské rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="301d2-193">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="301d2-194">`<remarks>` Obsah elementu se může skládat z textu, JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="301d2-194">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="301d2-195">Všimněte si, že se tyto další komentáře vylepšení uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="301d2-195">Notice the UI enhancements with these additional comments:</span></span>

![Uživatelské rozhraní swagger se zobrazí další komentáře](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="301d2-197">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="301d2-197">Data annotations</span></span>

<span data-ttu-id="301d2-198">Uspořádání s atributy, které jsou součástí modelu [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) oboru názvů, pomůže zajistit součásti uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="301d2-198">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="301d2-199">Přidat `[Required]` atribut `Name` vlastnost `TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="301d2-199">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="301d2-200">Přítomnost tento atribut se změní chování uživatelského rozhraní a změní podkladové schéma JSON:</span><span class="sxs-lookup"><span data-stu-id="301d2-200">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="301d2-201">Přidat `[Produces("application/json")]` atribut kontroleru rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="301d2-201">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="301d2-202">Jeho účelem je deklarovat, že akce kontroleru, podporují typ obsahu odpovědi z *application/json*:</span><span class="sxs-lookup"><span data-stu-id="301d2-202">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="301d2-203">**Typ obsahu odpovědi** rozevíracího seznamu vybere tento typ obsahu jako výchozí pro akce GET kontroleru:</span><span class="sxs-lookup"><span data-stu-id="301d2-203">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Uživatelské rozhraní swagger s výchozím typem obsahu odpovědi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="301d2-205">Popisný a užitečné stránky nápovědy, jak se zvyšuje využití datových poznámek v rozhraní Web API, uživatelské rozhraní a rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="301d2-205">As the usage of data annotations in the Web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="301d2-206">Popis typů odpovědi</span><span class="sxs-lookup"><span data-stu-id="301d2-206">Describe response types</span></span>

<span data-ttu-id="301d2-207">Využívání vývojáři jsou nejvíce zajímají co bude vráceno&mdash;konkrétně typů odpovědi a chybové kódy (Pokud není standard).</span><span class="sxs-lookup"><span data-stu-id="301d2-207">Consuming developers are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="301d2-208">V poznámkách komentáře a data XML jsou rozlišeny typů odpovědi a kódy chyb.</span><span class="sxs-lookup"><span data-stu-id="301d2-208">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="301d2-209">`Create` Akce v případě úspěchu vrátí stavový kód HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="301d2-209">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="301d2-210">Stavový kód HTTP 400 je vrácena, když textu odeslaného požadavku má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="301d2-210">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="301d2-211">Bez správnou dokumentaci v Uživatelském rozhraní Swagger nemá příjemce znalost těchto očekávaných výsledků.</span><span class="sxs-lookup"><span data-stu-id="301d2-211">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="301d2-212">Tento problém vyřešit přidáním zvýrazněné řádky v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="301d2-212">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="301d2-213">Uživatelské rozhraní Swagger teď jasně dokumenty očekávané kódy odpovědí protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="301d2-213">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Swagger UI zobrazení popisu třídy odpověď POST 'Vrátí nově vytvořenou položku seznamu úkolů' a '400 - Pokud položka má hodnotu null' stavový kód a důvod v odpovědích](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customize-the-ui"></a><span data-ttu-id="301d2-215">Přizpůsobení uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="301d2-215">Customize the UI</span></span>

<span data-ttu-id="301d2-216">Stock uživatelského rozhraní je funkční a přístupné.</span><span class="sxs-lookup"><span data-stu-id="301d2-216">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="301d2-217">Stránky dokumentace rozhraní API by měla představovat však vaší značce nebo motivu.</span><span class="sxs-lookup"><span data-stu-id="301d2-217">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="301d2-218">Branding komponenty Swashbuckle vyžaduje přidání těchto materiálů k doručování statických souborů a sestavování strukturu složek pro hostování těchto souborů.</span><span class="sxs-lookup"><span data-stu-id="301d2-218">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="301d2-219">Pokud je zaměřen na rozhraní .NET Framework nebo .NET Core 1.x, přidejte [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) do projektu balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="301d2-219">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="301d2-220">Pokud cílí na .NET Core je již nainstalována předchozí balíček NuGet 2.x a použití [Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="301d2-220">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="301d2-221">Povolte Middleware se statickými soubory:</span><span class="sxs-lookup"><span data-stu-id="301d2-221">Enable Static File Middleware:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

<span data-ttu-id="301d2-222">Získat obsah *dist* složky [úložiště GitHub uživatelského rozhraní Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span><span class="sxs-lookup"><span data-stu-id="301d2-222">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="301d2-223">Tato složka obsahuje prostředky potřebné pro stránka uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="301d2-223">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="301d2-224">Vytvoření *wwwroot/swagger/uživatelského rozhraní* složky a zkopírujte do něj obsah *dist* složky.</span><span class="sxs-lookup"><span data-stu-id="301d2-224">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="301d2-225">Vytvoření *custom.css* souboru *wwwroot/swagger/uživatelského rozhraní*, pomocí následující šablony stylů CSS pro přizpůsobení záhlaví stránky:</span><span class="sxs-lookup"><span data-stu-id="301d2-225">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="301d2-226">Referenční dokumentace *custom.css* v *index.html* souboru po další soubory šablon stylů CSS:</span><span class="sxs-lookup"><span data-stu-id="301d2-226">Reference *custom.css* in the *index.html* file, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="301d2-227">Přejděte *index.html* na stránce `http://localhost:<port>/swagger/ui/index.html`.</span><span class="sxs-lookup"><span data-stu-id="301d2-227">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="301d2-228">Zadejte `http://localhost:<port>/swagger/v1/swagger.json` hlavičky textového pole a klikněte na **prozkoumat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="301d2-228">Enter `http://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="301d2-229">Výsledný stránka vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="301d2-229">The resulting page looks as follows:</span></span>

![Uživatelské rozhraní swagger s názvem vlastní hlavičky](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="301d2-231">Je mnohem více že můžete provést na stránce.</span><span class="sxs-lookup"><span data-stu-id="301d2-231">There's much more you can do with the page.</span></span> <span data-ttu-id="301d2-232">Zobrazit všechny funkce pro prostředky uživatelského rozhraní na [úložiště GitHub uživatelského rozhraní Swagger](https://github.com/swagger-api/swagger-ui).</span><span class="sxs-lookup"><span data-stu-id="301d2-232">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
