---
title: Začínáme se službou NSwag a ASP.NET Core
author: zuckerthoben
description: Další informace o použití službou NSwag generovat dokumentaci a stránky pro webovému rozhraní API ASP.NET Core nápovědy.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/30/2018
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: af07ad771c582cfad80f297748c3c1049ff4a7d6
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419404"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="51669-103">Začínáme se službou NSwag a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51669-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="51669-104">Podle [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), a [společnosti Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="51669-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="51669-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51669-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="51669-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51669-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="51669-107">Službou NSwag nabízí následující funkce:</span><span class="sxs-lookup"><span data-stu-id="51669-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="51669-108">Možnost využít uživatelské rozhraní Swagger a Swagger generátoru.</span><span class="sxs-lookup"><span data-stu-id="51669-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="51669-109">Možnosti generování flexibilní kódu.</span><span class="sxs-lookup"><span data-stu-id="51669-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="51669-110">Se službou NSwag, není nutné existujícího rozhraní API&mdash;můžete použít rozhraní API třetích stran, která začlenit Swagger a generovat implementace klienta.</span><span class="sxs-lookup"><span data-stu-id="51669-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="51669-111">Službou NSwag umožňuje urychlení cyklu vývoje a snadno reagovat na změny rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="51669-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="51669-112">Zaregistrujte middleware službou NSwag</span><span class="sxs-lookup"><span data-stu-id="51669-112">Register the NSwag middleware</span></span>

<span data-ttu-id="51669-113">Zaregistrujte službou NSwag middlewaru, který má být:</span><span class="sxs-lookup"><span data-stu-id="51669-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="51669-114">Generovat specifikaci Swaggeru pro rozhraní API implementované webu.</span><span class="sxs-lookup"><span data-stu-id="51669-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="51669-115">Poskytování uživatelského rozhraní Swagger pro procházení a testování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="51669-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="51669-116">Použít [službou NSwag](https://github.com/RSuter/NSwag) middleware ASP.NET Core, nainstalujte [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="51669-116">To use the [NSwag](https://github.com/RSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="51669-117">Tento balíček obsahuje middleware pro generování a obsluhovat specifikace Swaggeru, uživatelské rozhraní Swagger (v2 a v3), a [uživatelského rozhraní ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="51669-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="51669-118">Použijte jednu z následujících dvou přístupů k instalaci balíčku NuGet službou NSwag:</span><span class="sxs-lookup"><span data-stu-id="51669-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="51669-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51669-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="51669-120">Z **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="51669-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="51669-121">Přejděte na **zobrazení** > **jiných Windows** > **Konzola správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="51669-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="51669-122">Přejděte do adresáře, ve kterém *TodoApi.csproj* soubor existuje</span><span class="sxs-lookup"><span data-stu-id="51669-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="51669-123">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="51669-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="51669-124">Z **spravovat balíčky NuGet** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="51669-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="51669-125">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="51669-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="51669-126">Nastavte **zdroj balíčku** do "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="51669-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="51669-127">Do vyhledávacího pole zadejte "NSwag.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="51669-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="51669-128">Vyberte balíček "NSwag.AspNetCore" z **Procházet** kartě a klikněte na tlačítko **instalace**</span><span class="sxs-lookup"><span data-stu-id="51669-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="51669-129">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="51669-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="51669-130">Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="51669-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="51669-131">Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="51669-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="51669-132">Do vyhledávacího pole zadejte "NSwag.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="51669-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="51669-133">Vyberte v podokně výsledků "NSwag.AspNetCore" balíček a klikněte na tlačítko **přidat balíček**</span><span class="sxs-lookup"><span data-stu-id="51669-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="51669-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51669-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="51669-135">Spuštěním následujícího příkazu z **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="51669-135">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="51669-136">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="51669-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="51669-137">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="51669-137">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="51669-138">Přidejte a nakonfigurujte Swagger middleware</span><span class="sxs-lookup"><span data-stu-id="51669-138">Add and configure Swagger middleware</span></span>

 <span data-ttu-id="51669-139">Přidat a nakonfigurovat Swagger ve vaší aplikaci ASP.NET Core pomocí provádí následující kroky v `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="51669-139">Add and configure Swagger in your ASP.NET Core app by performing the following steps in the `Startup` class:</span></span>

* <span data-ttu-id="51669-140">Importujte následující obory názvů:</span><span class="sxs-lookup"><span data-stu-id="51669-140">Import the following namespaces:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

* <span data-ttu-id="51669-141">V `ConfigureServices` metoda, registraci k požadovaným službám Swaggeru:</span><span class="sxs-lookup"><span data-stu-id="51669-141">In the `ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="51669-142">V `Configure` metoda, povolí middleware pro poskytování generované specifikace Swagger a uživatelské rozhraní Swagger:</span><span class="sxs-lookup"><span data-stu-id="51669-142">In the `Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="51669-143">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51669-143">Launch the app.</span></span> <span data-ttu-id="51669-144">Přejděte do:</span><span class="sxs-lookup"><span data-stu-id="51669-144">Navigate to:</span></span>
  * <span data-ttu-id="51669-145">`http://localhost:<port>/swagger` Chcete-li zobrazit uživatelské rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="51669-145">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="51669-146">`http://localhost:<port>/swagger/v1/swagger.json` Chcete-li zobrazit specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="51669-146">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="51669-147">Generování kódu</span><span class="sxs-lookup"><span data-stu-id="51669-147">Code generation</span></span>

<span data-ttu-id="51669-148">Můžete využít možnosti generování kódu vaší službou NSwag výběrem jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="51669-148">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="51669-149">[NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio) &ndash; desktopové aplikace Windows pro generování kódu klienta pro rozhraní API v C# nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="51669-149">[NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="51669-150">[NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) balíčky NuGet pro generování kódu v projektu.</span><span class="sxs-lookup"><span data-stu-id="51669-150">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="51669-151">Službou NSwag z [příkazového řádku](https://github.com/NSwag/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="51669-151">NSwag from the [command line](https://github.com/NSwag/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="51669-152">[NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="51669-152">The [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) NuGet package.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="51669-153">Generování kódu s NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="51669-153">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="51669-154">Nainstalujte NSwagStudio podle pokynů uvedených v [úložiště NSwagStudio GitHub](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="51669-154">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="51669-155">Spusťte NSwagStudio a zadejte *swagger.json* adresy URL v souboru **adresa URL specifikace Swaggeru** textového pole.</span><span class="sxs-lookup"><span data-stu-id="51669-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="51669-156">Například *http://localhost:44354/swagger/v1/swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="51669-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="51669-157">Klikněte na tlačítko **vytvořit místní kopii** pro vygenerování JSON s reprezentací specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="51669-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Vytvořit místní kopii specifikace Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="51669-159">V **výstupy** oblast, klikněte na tlačítko **CSharp klienta** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="51669-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="51669-160">V závislosti na váš projekt, můžete také zvolit **TypeScript klienta** nebo **Kontroleru webového rozhraní API CSharp**.</span><span class="sxs-lookup"><span data-stu-id="51669-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="51669-161">Pokud vyberete **Kontroleru webového rozhraní API CSharp**, specifikace služby znovu sestaví služby slouží jako reverzní generace.</span><span class="sxs-lookup"><span data-stu-id="51669-161">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="51669-162">Klikněte na tlačítko **generovat výstupy** vytvoří kompletní C# implementace klienta *TodoApi.NSwag* projektu.</span><span class="sxs-lookup"><span data-stu-id="51669-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="51669-163">Chcete-li zobrazit kód klienta vygenerovaný, klikněte na tlačítko **CSharp klienta** kartu:</span><span class="sxs-lookup"><span data-stu-id="51669-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="51669-164">C# Generování kódu klienta na základě výběru v **nastavení** kartu. Změňte nastavení a provádět úlohy, například přejmenování výchozí obor názvů a generování synchronní metody.</span><span class="sxs-lookup"><span data-stu-id="51669-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="51669-165">Zkopírujte vygenerovaný C# kód do souboru v projektu klienta, který bude využívat rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="51669-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="51669-166">Spusťte využívající webové rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="51669-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="51669-167">Přizpůsobení dokumentace k rozhraní API</span><span class="sxs-lookup"><span data-stu-id="51669-167">Customize API documentation</span></span>

<span data-ttu-id="51669-168">Swagger poskytuje možnosti pro dokumentace objektového modelu pro usnadnění spotřeby webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="51669-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="51669-169">Informace o rozhraní API a popis</span><span class="sxs-lookup"><span data-stu-id="51669-169">API info and description</span></span>

<span data-ttu-id="51669-170">V `Startup.ConfigureServices` metody akce konfigurace předán `AddSwaggerDocument` přidá informace, jako je vytváření, licence a popis metody:</span><span class="sxs-lookup"><span data-stu-id="51669-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="51669-171">Uživatelské rozhraní Swagger zobrazí informace na verzi:</span><span class="sxs-lookup"><span data-stu-id="51669-171">The Swagger UI displays the version's information:</span></span>

![Uživatelské rozhraní swagger s informací o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="51669-173">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="51669-173">XML comments</span></span>

<span data-ttu-id="51669-174">Povolit komentáře XML, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="51669-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="51669-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51669-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="51669-176">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **upravit < project_name > .csproj**.</span><span class="sxs-lookup"><span data-stu-id="51669-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="51669-177">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="51669-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="51669-178">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**</span><span class="sxs-lookup"><span data-stu-id="51669-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="51669-179">Zkontrolujte **soubor dokumentace XML** pole v rámci **výstup** část **sestavení** kartu</span><span class="sxs-lookup"><span data-stu-id="51669-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="51669-180">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="51669-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="51669-181">Z *oblasti řešení*, stiskněte klávesu **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="51669-181">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="51669-182">Přejděte do **nástroje** > **upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="51669-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="51669-183">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="51669-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="51669-184">Otevřít **možnosti projektu** dialogového okna > **sestavení** > **kompilátoru**</span><span class="sxs-lookup"><span data-stu-id="51669-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="51669-185">Zkontrolujte **generovat dokumentaci xml** pole v rámci **Obecné možnosti** oddílu</span><span class="sxs-lookup"><span data-stu-id="51669-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="51669-186">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="51669-186">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="51669-187">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="51669-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="51669-188">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="51669-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="51669-189">Vzhledem k tomu používá službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nelze odvodit, co dělá akce a návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="51669-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="51669-190">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="51669-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="51669-191">Vrátí předchozí akce `IActionResult`, ale uvnitř akce vrací buď [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) nebo [chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*).</span><span class="sxs-lookup"><span data-stu-id="51669-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="51669-192">Použití anotací dat zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="51669-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="51669-193">Uspořádání akce s následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="51669-193">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

 <span data-ttu-id="51669-194">Vzhledem k tomu používá službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [ActionResult\<T >](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), ji pouze odvodit návratový typ určené `T`.</span><span class="sxs-lookup"><span data-stu-id="51669-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="51669-195">Nelze automaticky odvodit další možné návratové typy.</span><span class="sxs-lookup"><span data-stu-id="51669-195">You can't automatically infer other possible return types.</span></span> 

<span data-ttu-id="51669-196">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="51669-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="51669-197">Vrátí předchozí akce `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="51669-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="51669-198">V akci, vrací [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="51669-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="51669-199">Protože kontroler je doplněn [[objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut, [chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*) odpovědi je také možné,.</span><span class="sxs-lookup"><span data-stu-id="51669-199">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="51669-200">Další informace najdete v tématu [odpovědi HTTP 400 automatické](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="51669-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="51669-201">Použití anotací dat zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="51669-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="51669-202">Uspořádání akce s následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="51669-202">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="51669-203">V ASP.NET Core 2.2 nebo vyšší, můžete místo explicitně upravení jednotlivé akce s použít konvence `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="51669-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="51669-204">Další informace naleznete v tématu <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="51669-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="51669-205">Generátoru Swagger můžete nyní přesně popište tuto akci a vygenerovaný klienti věděli, co se zobrazí při volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="51669-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="51669-206">Jako doporučení uspořádání všechny akce s těmito atributy.</span><span class="sxs-lookup"><span data-stu-id="51669-206">As a recommendation, decorate all actions with these attributes.</span></span>

<span data-ttu-id="51669-207">Pokyny pro jaké odpovědi protokolu HTTP, by měla vrátit akce rozhraní API najdete v článku [specifikaci RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="51669-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
