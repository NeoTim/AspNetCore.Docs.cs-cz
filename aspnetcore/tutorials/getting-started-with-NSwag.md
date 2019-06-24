---
title: Začínáme se službou NSwag a ASP.NET Core
author: zuckerthoben
description: Další informace o použití službou NSwag generovat dokumentaci a stránky pro webovému rozhraní API ASP.NET Core nápovědy.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/21/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: c5b2dc47328d6d3c271a87579fa8c300109bd734
ms.sourcegitcommit: 06a455d63ff7d6b571ca832e8117f4ac9d646baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316559"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="368d4-103">Začínáme se službou NSwag a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="368d4-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="368d4-104">Podle [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), a [společnosti Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="368d4-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="368d4-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="368d4-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="368d4-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="368d4-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="368d4-107">Službou NSwag nabízí následující funkce:</span><span class="sxs-lookup"><span data-stu-id="368d4-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="368d4-108">Možnost využít uživatelské rozhraní Swagger a Swagger generátoru.</span><span class="sxs-lookup"><span data-stu-id="368d4-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="368d4-109">Možnosti generování flexibilní kódu.</span><span class="sxs-lookup"><span data-stu-id="368d4-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="368d4-110">Se službou NSwag, není nutné existujícího rozhraní API&mdash;můžete použít rozhraní API třetích stran, která začlenit Swagger a generovat implementace klienta.</span><span class="sxs-lookup"><span data-stu-id="368d4-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="368d4-111">Službou NSwag umožňuje urychlení cyklu vývoje a snadno reagovat na změny rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="368d4-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="368d4-112">Zaregistrujte middleware službou NSwag</span><span class="sxs-lookup"><span data-stu-id="368d4-112">Register the NSwag middleware</span></span>

<span data-ttu-id="368d4-113">Zaregistrujte službou NSwag middlewaru, který má být:</span><span class="sxs-lookup"><span data-stu-id="368d4-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="368d4-114">Generovat specifikaci Swaggeru pro rozhraní API implementované webu.</span><span class="sxs-lookup"><span data-stu-id="368d4-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="368d4-115">Poskytování uživatelského rozhraní Swagger pro procházení a testování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="368d4-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="368d4-116">Použít [službou NSwag](https://github.com/RicoSuter/NSwag) middleware ASP.NET Core, nainstalujte [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="368d4-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="368d4-117">Tento balíček obsahuje middleware pro generování a obsluhovat specifikace Swaggeru, uživatelské rozhraní Swagger (v2 a v3), a [uživatelského rozhraní ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="368d4-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="368d4-118">Použijte jednu z následujících dvou přístupů k instalaci balíčku NuGet službou NSwag:</span><span class="sxs-lookup"><span data-stu-id="368d4-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="368d4-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="368d4-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="368d4-120">Z **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="368d4-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="368d4-121">Přejděte na **zobrazení** > **jiných Windows** > **Konzola správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="368d4-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="368d4-122">Přejděte do adresáře, ve kterém *TodoApi.csproj* soubor existuje</span><span class="sxs-lookup"><span data-stu-id="368d4-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="368d4-123">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="368d4-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="368d4-124">Z **spravovat balíčky NuGet** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="368d4-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="368d4-125">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="368d4-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="368d4-126">Nastavte **zdroj balíčku** do "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="368d4-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="368d4-127">Do vyhledávacího pole zadejte "NSwag.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="368d4-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="368d4-128">Vyberte balíček "NSwag.AspNetCore" z **Procházet** kartě a klikněte na tlačítko **instalace**</span><span class="sxs-lookup"><span data-stu-id="368d4-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="368d4-129">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="368d4-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="368d4-130">Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="368d4-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="368d4-131">Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="368d4-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="368d4-132">Do vyhledávacího pole zadejte "NSwag.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="368d4-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="368d4-133">Vyberte v podokně výsledků "NSwag.AspNetCore" balíček a klikněte na tlačítko **přidat balíček**</span><span class="sxs-lookup"><span data-stu-id="368d4-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="368d4-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="368d4-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="368d4-135">Spuštěním následujícího příkazu z **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="368d4-135">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="368d4-136">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="368d4-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="368d4-137">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="368d4-137">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="368d4-138">Přidejte a nakonfigurujte Swagger middleware</span><span class="sxs-lookup"><span data-stu-id="368d4-138">Add and configure Swagger middleware</span></span>

<span data-ttu-id="368d4-139">Přidat a nakonfigurovat Swagger ve vaší aplikaci ASP.NET Core pomocí následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="368d4-139">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="368d4-140">V `Startup.ConfigureServices` metoda, registraci k požadovaným službám Swaggeru:</span><span class="sxs-lookup"><span data-stu-id="368d4-140">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="368d4-141">V `Startup.Configure` metoda, povolí middleware pro poskytování generované specifikace Swagger a uživatelské rozhraní Swagger:</span><span class="sxs-lookup"><span data-stu-id="368d4-141">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="368d4-142">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="368d4-142">Launch the app.</span></span> <span data-ttu-id="368d4-143">Přejděte do:</span><span class="sxs-lookup"><span data-stu-id="368d4-143">Navigate to:</span></span>
  * <span data-ttu-id="368d4-144">`http://localhost:<port>/swagger` Chcete-li zobrazit uživatelské rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="368d4-144">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="368d4-145">`http://localhost:<port>/swagger/v1/swagger.json` Chcete-li zobrazit specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="368d4-145">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="368d4-146">Generování kódu</span><span class="sxs-lookup"><span data-stu-id="368d4-146">Code generation</span></span>

<span data-ttu-id="368d4-147">Můžete využít možnosti generování kódu vaší službou NSwag výběrem jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="368d4-147">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="368d4-148">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; desktopové aplikace Windows pro generování kódu klienta pro rozhraní API v C# nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="368d4-148">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="368d4-149">[NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) balíčky NuGet pro generování kódu v projektu.</span><span class="sxs-lookup"><span data-stu-id="368d4-149">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="368d4-150">Službou NSwag z [příkazového řádku](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="368d4-150">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="368d4-151">[NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/MSBuild) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="368d4-151">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/MSBuild) NuGet package.</span></span>
* <span data-ttu-id="368d4-152">[Unchase OpenAPI (Swagger) připojenou službu](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; službě Visual Studio připojené ke generování kódu klienta pro rozhraní API v C# nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="368d4-152">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; a Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="368d4-153">Také vygeneruje C# řadiče pro OpenAPI služby se službou NSwag.</span><span class="sxs-lookup"><span data-stu-id="368d4-153">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="368d4-154">Generování kódu s NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="368d4-154">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="368d4-155">Nainstalujte NSwagStudio podle pokynů uvedených v [úložiště NSwagStudio GitHub](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="368d4-155">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="368d4-156">Spusťte NSwagStudio a zadejte *swagger.json* adresy URL v souboru **adresa URL specifikace Swaggeru** textového pole.</span><span class="sxs-lookup"><span data-stu-id="368d4-156">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="368d4-157">Například *http://localhost:44354/swagger/v1/swagger.json* .</span><span class="sxs-lookup"><span data-stu-id="368d4-157">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="368d4-158">Klikněte na tlačítko **vytvořit místní kopii** pro vygenerování JSON s reprezentací specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="368d4-158">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Vytvořit místní kopii specifikace Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="368d4-160">V **výstupy** oblast, klikněte na tlačítko **CSharp klienta** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="368d4-160">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="368d4-161">V závislosti na váš projekt, můžete také zvolit **TypeScript klienta** nebo **Kontroleru webového rozhraní API CSharp**.</span><span class="sxs-lookup"><span data-stu-id="368d4-161">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="368d4-162">Pokud vyberete **Kontroleru webového rozhraní API CSharp**, specifikace služby znovu sestaví služby slouží jako reverzní generace.</span><span class="sxs-lookup"><span data-stu-id="368d4-162">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="368d4-163">Klikněte na tlačítko **generovat výstupy** vytvoří kompletní C# implementace klienta *TodoApi.NSwag* projektu.</span><span class="sxs-lookup"><span data-stu-id="368d4-163">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="368d4-164">Chcete-li zobrazit kód klienta vygenerovaný, klikněte na tlačítko **CSharp klienta** kartu:</span><span class="sxs-lookup"><span data-stu-id="368d4-164">To see the generated client code, click the **CSharp Client** tab:</span></span>

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
> <span data-ttu-id="368d4-165">C# Generování kódu klienta na základě výběru v **nastavení** kartu. Změňte nastavení a provádět úlohy, například přejmenování výchozí obor názvů a generování synchronní metody.</span><span class="sxs-lookup"><span data-stu-id="368d4-165">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="368d4-166">Zkopírujte vygenerovaný C# kód do souboru v projektu klienta, který bude využívat rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="368d4-166">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="368d4-167">Spusťte využívající webové rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="368d4-167">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="368d4-168">Přizpůsobení dokumentace k rozhraní API</span><span class="sxs-lookup"><span data-stu-id="368d4-168">Customize API documentation</span></span>

<span data-ttu-id="368d4-169">Swagger poskytuje možnosti pro dokumentace objektového modelu pro usnadnění spotřeby webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="368d4-169">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="368d4-170">Informace o rozhraní API a popis</span><span class="sxs-lookup"><span data-stu-id="368d4-170">API info and description</span></span>

<span data-ttu-id="368d4-171">V `Startup.ConfigureServices` metody akce konfigurace předán `AddSwaggerDocument` přidá informace, jako je vytváření, licence a popis metody:</span><span class="sxs-lookup"><span data-stu-id="368d4-171">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="368d4-172">Uživatelské rozhraní Swagger zobrazí informace na verzi:</span><span class="sxs-lookup"><span data-stu-id="368d4-172">The Swagger UI displays the version's information:</span></span>

![Uživatelské rozhraní swagger s informací o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="368d4-174">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="368d4-174">XML comments</span></span>

<span data-ttu-id="368d4-175">Povolit komentáře XML, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="368d4-175">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="368d4-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="368d4-176">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="368d4-177">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **upravit < project_name > .csproj**.</span><span class="sxs-lookup"><span data-stu-id="368d4-177">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="368d4-178">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="368d4-178">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="368d4-179">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**</span><span class="sxs-lookup"><span data-stu-id="368d4-179">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="368d4-180">Zkontrolujte **soubor dokumentace XML** pole v rámci **výstup** část **sestavení** kartu</span><span class="sxs-lookup"><span data-stu-id="368d4-180">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="368d4-181">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="368d4-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="368d4-182">Z *oblasti řešení*, stiskněte klávesu **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="368d4-182">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="368d4-183">Přejděte do **nástroje** > **upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="368d4-183">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="368d4-184">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="368d4-184">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="368d4-185">Otevřít **možnosti projektu** dialogového okna > **sestavení** > **kompilátoru**</span><span class="sxs-lookup"><span data-stu-id="368d4-185">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="368d4-186">Zkontrolujte **generovat dokumentaci xml** pole v rámci **Obecné možnosti** oddílu</span><span class="sxs-lookup"><span data-stu-id="368d4-186">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="368d4-187">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="368d4-187">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="368d4-188">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="368d4-188">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="368d4-189">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="368d4-189">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="368d4-190">Vzhledem k tomu používá službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nelze odvodit, co dělá akce a návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="368d4-190">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="368d4-191">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="368d4-191">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="368d4-192">Vrátí předchozí akce `IActionResult`, ale uvnitř akce vrací buď [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) nebo [chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*).</span><span class="sxs-lookup"><span data-stu-id="368d4-192">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="368d4-193">Použití anotací dat zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="368d4-193">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="368d4-194">Uspořádání akce s následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="368d4-194">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

 <span data-ttu-id="368d4-195">Vzhledem k tomu používá službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [ActionResult\<T >](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), ji pouze odvodit návratový typ určené `T`.</span><span class="sxs-lookup"><span data-stu-id="368d4-195">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="368d4-196">Nelze automaticky odvodit další možné návratové typy.</span><span class="sxs-lookup"><span data-stu-id="368d4-196">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="368d4-197">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="368d4-197">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="368d4-198">Vrátí předchozí akce `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="368d4-198">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="368d4-199">V akci, vrací [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="368d4-199">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="368d4-200">Protože kontroler je doplněn [[objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut, [chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*) odpovědi je také možné,.</span><span class="sxs-lookup"><span data-stu-id="368d4-200">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="368d4-201">Další informace najdete v tématu [odpovědi HTTP 400 automatické](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="368d4-201">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="368d4-202">Použití anotací dat zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="368d4-202">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="368d4-203">Uspořádání akce s následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="368d4-203">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="368d4-204">V ASP.NET Core 2.2 nebo vyšší, můžete místo explicitně upravení jednotlivé akce s použít konvence `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="368d4-204">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="368d4-205">Další informace naleznete v tématu <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="368d4-205">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="368d4-206">Generátoru Swagger můžete nyní přesně popište tuto akci a vygenerovaný klienti věděli, co se zobrazí při volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="368d4-206">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="368d4-207">Jako doporučení uspořádání všechny akce s těmito atributy.</span><span class="sxs-lookup"><span data-stu-id="368d4-207">As a recommendation, decorate all actions with these attributes.</span></span>

<span data-ttu-id="368d4-208">Pokyny pro jaké odpovědi protokolu HTTP, by měla vrátit akce rozhraní API najdete v článku [specifikaci RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="368d4-208">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
