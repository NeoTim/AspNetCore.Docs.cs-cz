---
title: Začínáme se službou NSwag a ASP.NET Core
author: zuckerthoben
description: Další informace o použití službou NSwag generovat dokumentaci a stránky pro webovému rozhraní API ASP.NET Core nápovědy.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/18/2018
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 8af5bed1e042c4f6d83043b05084c51b3064a548
ms.sourcegitcommit: ea215df889e89db44037a6ac2f01baede0450da9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53595357"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="1c548-103">Začínáme se službou NSwag a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c548-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="1c548-104">Podle [Christoph Nienaber](https://twitter.com/zuckerthoben) a [Rico Suter](https://rsuter.com)</span><span class="sxs-lookup"><span data-stu-id="1c548-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://rsuter.com)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1c548-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c548-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="1c548-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c548-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="1c548-107">Zaregistrujte middlewares službou NSwag na:</span><span class="sxs-lookup"><span data-stu-id="1c548-107">Register the NSwag middlewares to:</span></span>

* <span data-ttu-id="1c548-108">Generovat specifikaci Swaggeru pro rozhraní API implementované webu.</span><span class="sxs-lookup"><span data-stu-id="1c548-108">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="1c548-109">Poskytování uživatelského rozhraní Swagger pro procházení a testování webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1c548-109">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="1c548-110">Použít [službou NSwag](https://github.com/RSuter/NSwag) middlewares ASP.NET Core, nainstalujte [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="1c548-110">To use the [NSwag](https://github.com/RSuter/NSwag) ASP.NET Core middlewares, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="1c548-111">Tento balíček obsahuje middlewares generovat a slouží specifikace Swaggeru, uživatelské rozhraní Swagger (v2 a v3), a [uživatelského rozhraní ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="1c548-111">This package contains the middlewares to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="1c548-112">Kromě toho má důrazně doporučujeme používat vaší službou NSwag možnosti generování kódu.</span><span class="sxs-lookup"><span data-stu-id="1c548-112">Additionally, it's highly recommended to make use of NSwag's code generation capabilities.</span></span> <span data-ttu-id="1c548-113">Vyberte jednu z následujících možností využít možnosti generování kódu:</span><span class="sxs-lookup"><span data-stu-id="1c548-113">Choose one of the following options to use the code generation capabilities:</span></span>

* <span data-ttu-id="1c548-114">Použití [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio), desktopové aplikace Windows pro generování klientského kódu v C# a TypeScript pro vaše rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1c548-114">Use [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio), a Windows desktop app for generating client code in C# and TypeScript for your API.</span></span>
* <span data-ttu-id="1c548-115">Použití [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) balíčky NuGet, se generování uvnitř projektu kódu.</span><span class="sxs-lookup"><span data-stu-id="1c548-115">Use the [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages to do code generation inside your project.</span></span>
* <span data-ttu-id="1c548-116">Použití službou NSwag z [příkazového řádku](https://github.com/NSwag/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="1c548-116">Use NSwag from the [command line](https://github.com/NSwag/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="1c548-117">Použití [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="1c548-117">Use the [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) NuGet package.</span></span>

## <a name="features"></a><span data-ttu-id="1c548-118">Funkce</span><span class="sxs-lookup"><span data-stu-id="1c548-118">Features</span></span>

<span data-ttu-id="1c548-119">Hlavním důvodem pro použití službou NSwag je schopnost pouze uživatelské rozhraní Swagger a generátoru Swagger, ale také vytvářecí využívají možnosti generování flexibilní kódu.</span><span class="sxs-lookup"><span data-stu-id="1c548-119">The main reason to use NSwag is the ability to not only introduce the Swagger UI and Swagger generator, but to also make use of the flexible code generation capabilities.</span></span> <span data-ttu-id="1c548-120">Není nutné existujícího rozhraní API&mdash;můžete použít rozhraní API třetích stran, která začlenit Swagger a nechat službou NSwag generovat implementace klienta.</span><span class="sxs-lookup"><span data-stu-id="1c548-120">You don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and let NSwag generate a client implementation.</span></span> <span data-ttu-id="1c548-121">V obou případech urychlené vývojový cyklus a snadno přizpůsobit změn rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1c548-121">Either way, the development cycle is expedited and you can more easily adapt to API changes.</span></span>

## <a name="package-installation"></a><span data-ttu-id="1c548-122">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="1c548-122">Package installation</span></span>

<span data-ttu-id="1c548-123">Balíček NuGet službou NSwag lze přidat pomocí následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="1c548-123">The NSwag NuGet package can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c548-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c548-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1c548-125">Z **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="1c548-125">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="1c548-126">Přejděte na **zobrazení** > **jiných Windows** > **Konzola správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="1c548-126">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="1c548-127">Přejděte do adresáře, ve kterém *TodoApi.csproj* soubor existuje</span><span class="sxs-lookup"><span data-stu-id="1c548-127">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="1c548-128">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1c548-128">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="1c548-129">Z **spravovat balíčky NuGet** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="1c548-129">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="1c548-130">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="1c548-130">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="1c548-131">Nastavte **zdroj balíčku** do "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="1c548-131">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="1c548-132">Do vyhledávacího pole zadejte "NSwag.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="1c548-132">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="1c548-133">Vyberte balíček "NSwag.AspNetCore" z **Procházet** kartě a klikněte na tlačítko **instalace**</span><span class="sxs-lookup"><span data-stu-id="1c548-133">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1c548-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1c548-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1c548-135">Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="1c548-135">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="1c548-136">Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="1c548-136">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="1c548-137">Do vyhledávacího pole zadejte "NSwag.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="1c548-137">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="1c548-138">Vyberte v podokně výsledků "NSwag.AspNetCore" balíček a klikněte na tlačítko **přidat balíček**</span><span class="sxs-lookup"><span data-stu-id="1c548-138">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1c548-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c548-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1c548-140">Spuštěním následujícího příkazu z **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="1c548-140">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1c548-141">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="1c548-141">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1c548-142">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1c548-142">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="1c548-143">Přidejte a nakonfigurujte Swagger middleware</span><span class="sxs-lookup"><span data-stu-id="1c548-143">Add and configure Swagger middleware</span></span>

<span data-ttu-id="1c548-144">Importujte následující obory názvů v `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="1c548-144">Import the following namespaces in the `Startup` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

<span data-ttu-id="1c548-145">V `Startup.ConfigureServices` metoda, registraci k požadovaným službám Swaggeru:</span><span class="sxs-lookup"><span data-stu-id="1c548-145">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span> 

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="1c548-146">V `Startup.Configure` metoda, povolí middleware pro poskytování generované specifikace Swagger a uživatelské rozhraní Swagger v3:</span><span class="sxs-lookup"><span data-stu-id="1c548-146">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI v3:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-10)]

<span data-ttu-id="1c548-147">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1c548-147">Launch the app.</span></span> <span data-ttu-id="1c548-148">Přejděte na `http://localhost:<port>/swagger` Chcete-li zobrazit uživatelské rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="1c548-148">Navigate to `http://localhost:<port>/swagger` to view the Swagger UI.</span></span> <span data-ttu-id="1c548-149">Přejděte do `http://localhost:<port>/swagger/v1/swagger.json` zobrazíte specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="1c548-149">Navigate to `http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="1c548-150">Generování kódu</span><span class="sxs-lookup"><span data-stu-id="1c548-150">Code generation</span></span>

### <a name="via-nswagstudio"></a><span data-ttu-id="1c548-151">Via NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="1c548-151">Via NSwagStudio</span></span>

* <span data-ttu-id="1c548-152">Nainstalujte NSwagStudio z oficiální [úložiště GitHub](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="1c548-152">Install NSwagStudio from the official [GitHub repository](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="1c548-153">Spusťte NSwagStudio.</span><span class="sxs-lookup"><span data-stu-id="1c548-153">Launch NSwagStudio.</span></span> <span data-ttu-id="1c548-154">Zadejte *swagger.json* adresy URL v souboru **adresa URL specifikace Swaggeru** textového pole a klikněte na tlačítko **vytvořit místní kopii** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="1c548-154">Enter the *swagger.json* file URL in the **Swagger Specification URL** textbox, and click the **Create local Copy** button.</span></span>
* <span data-ttu-id="1c548-155">Vyberte **CSharp klienta** typ výstupu klienta.</span><span class="sxs-lookup"><span data-stu-id="1c548-155">Select the **CSharp Client** client output type.</span></span> <span data-ttu-id="1c548-156">Další možnosti zahrnují **TypeScript klienta** a **Kontroleru webového rozhraní API CSharp**.</span><span class="sxs-lookup"><span data-stu-id="1c548-156">Other options include **TypeScript Client** and **CSharp Web API Controller**.</span></span> <span data-ttu-id="1c548-157">Použití Kontroleru webového rozhraní API je v podstatě obrácenou generace.</span><span class="sxs-lookup"><span data-stu-id="1c548-157">Using a Web API Controller is basically a reverse generation.</span></span> <span data-ttu-id="1c548-158">Specifikace služby používá k opětovnému sestavení služby.</span><span class="sxs-lookup"><span data-stu-id="1c548-158">It uses a specification of a service to rebuild the service.</span></span>
* <span data-ttu-id="1c548-159">Klikněte na tlačítko **generovat výstupy** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="1c548-159">Click the **Generate Outputs** button.</span></span> <span data-ttu-id="1c548-160">Na dokončení C# klienta implementace *TodoApi.NSwag* projekt je vytvořen.</span><span class="sxs-lookup"><span data-stu-id="1c548-160">A complete C# client implementation of the *TodoApi.NSwag* project is produced.</span></span> <span data-ttu-id="1c548-161">Klikněte na tlačítko **CSharp klienta** karty **výstupy** část kódu generovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="1c548-161">Click the **CSharp Client** tab of the **Outputs** section to see the generated client code:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable // Disable all warnings

    [System.CodeDom.Compiler.GeneratedCode("NSwag",
        "11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "http://localhost:50499";
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient()
        {
            _settings = new System.Lazy
                <Newtonsoft.Json.JsonSerializerSettings>(() =>
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
> <span data-ttu-id="1c548-162">Generování kódu klienta jazyka C# na základě definované v nastavení **nastavení** kartě **CSharp klienta** kartu. Změňte nastavení a provádět úlohy, například přejmenování výchozí obor názvů a generování synchronní metody.</span><span class="sxs-lookup"><span data-stu-id="1c548-162">The C# client code is generated based on settings defined in the **Settings** tab of the **CSharp Client** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="1c548-163">Zkopírujte vygenerovaný kód jazyka C# do souboru v projektu klienta (například [Xamarin.Forms](/xamarin/xamarin-forms/) aplikace).</span><span class="sxs-lookup"><span data-stu-id="1c548-163">Copy the generated C# code into a file in a client project (for example, a [Xamarin.Forms](/xamarin/xamarin-forms/) app).</span></span>
* <span data-ttu-id="1c548-164">Spusťte využívající webové rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="1c548-164">Start consuming the web API:</span></span>

```csharp
var todoClient = new TodoClient();

// Gets all to-dos from the API
var allTodos = await todoClient.GetAllAsync();

// Create a new TodoItem, and save it in the API
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

> [!NOTE]
> <span data-ttu-id="1c548-165">Základní adresu URL a/nebo klienta HTTP můžete vložit do rozhraní API klienta.</span><span class="sxs-lookup"><span data-stu-id="1c548-165">You can inject a base URL and/or a HTTP client into the API client.</span></span> <span data-ttu-id="1c548-166">Osvědčeným postupem je vždy [opakovaně používat HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/).</span><span class="sxs-lookup"><span data-stu-id="1c548-166">The best practice is to always [reuse the HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/).</span></span>

### <a name="other-ways-to-generate-client-code"></a><span data-ttu-id="1c548-167">Další možnosti, jak generovat kód klienta</span><span class="sxs-lookup"><span data-stu-id="1c548-167">Other ways to generate client code</span></span>

<span data-ttu-id="1c548-168">Můžete vygenerovat kód klienta jinými způsoby, další vhodné do svého pracovního postupu:</span><span class="sxs-lookup"><span data-stu-id="1c548-168">You can generate the client code in other ways, more suited to your workflow:</span></span>

* [<span data-ttu-id="1c548-169">MSBuild</span><span class="sxs-lookup"><span data-stu-id="1c548-169">MSBuild</span></span>](https://www.nuget.org/packages/NSwag.MSBuild/)

* [<span data-ttu-id="1c548-170">V kódu</span><span class="sxs-lookup"><span data-stu-id="1c548-170">In code</span></span>](https://github.com/NSwag/NSwag/wiki/SwaggerToCSharpClientGenerator)

* [<span data-ttu-id="1c548-171">Šablony T4</span><span class="sxs-lookup"><span data-stu-id="1c548-171">T4 templates</span></span>](https://github.com/NSwag/NSwag/wiki/T4)

## <a name="customize"></a><span data-ttu-id="1c548-172">Přizpůsobit</span><span class="sxs-lookup"><span data-stu-id="1c548-172">Customize</span></span>

<span data-ttu-id="1c548-173">Swagger poskytuje možnosti pro dokumentace objektového modelu pro usnadnění spotřeby webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1c548-173">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="1c548-174">Informace o rozhraní API a popis</span><span class="sxs-lookup"><span data-stu-id="1c548-174">API info and description</span></span>

<span data-ttu-id="1c548-175">V `Startup.Configure` metody akce konfigurace předán `UseSwagger` přidá informace, jako je vytváření, licence a popis metody:</span><span class="sxs-lookup"><span data-stu-id="1c548-175">In the `Startup.Configure` method, a configuration action passed to the `UseSwagger` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_UseSwagger)]

<span data-ttu-id="1c548-176">Uživatelské rozhraní Swagger zobrazí informace na verzi:</span><span class="sxs-lookup"><span data-stu-id="1c548-176">The Swagger UI displays the version's information:</span></span>

![Uživatelské rozhraní swagger s informací o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="1c548-178">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="1c548-178">XML comments</span></span>

<span data-ttu-id="1c548-179">Komentáře XML jsou povolené pomocí následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="1c548-179">XML comments are enabled with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio-xml"></a>[<span data-ttu-id="1c548-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c548-180">Visual Studio</span></span>](#tab/visual-studio-xml/)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="1c548-181">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **upravit < project_name > .csproj**.</span><span class="sxs-lookup"><span data-stu-id="1c548-181">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="1c548-182">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="1c548-182">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="1c548-183">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**</span><span class="sxs-lookup"><span data-stu-id="1c548-183">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="1c548-184">Zkontrolujte **soubor dokumentace XML** pole v rámci **výstup** část **sestavení** kartu</span><span class="sxs-lookup"><span data-stu-id="1c548-184">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac-xml"></a>[<span data-ttu-id="1c548-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1c548-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac-xml/)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="1c548-186">Z *oblasti řešení*, stiskněte klávesu **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="1c548-186">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="1c548-187">Přejděte do **nástroje** > **upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="1c548-187">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="1c548-188">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="1c548-188">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="1c548-189">Otevřít **možnosti projektu** dialogového okna > **sestavení** > **kompilátoru**</span><span class="sxs-lookup"><span data-stu-id="1c548-189">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="1c548-190">Zkontrolujte **generovat dokumentaci xml** pole v rámci **Obecné možnosti** oddílu</span><span class="sxs-lookup"><span data-stu-id="1c548-190">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="visual-studio-codetabvisual-studio-code-xml"></a>[<span data-ttu-id="1c548-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c548-191">Visual Studio Code</span></span>](#tab/visual-studio-code-xml/)

<span data-ttu-id="1c548-192">Ručně přidejte zvýrazněné řádky a *.csproj* souboru:</span><span class="sxs-lookup"><span data-stu-id="1c548-192">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="1c548-193">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="1c548-193">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="1c548-194">Používá se službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult).</span><span class="sxs-lookup"><span data-stu-id="1c548-194">NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult).</span></span> <span data-ttu-id="1c548-195">V důsledku toho službou NSwag nelze odvodit, co dělá akce a návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1c548-195">Consequently, NSwag can't infer what your action is doing and what it returns.</span></span> <span data-ttu-id="1c548-196">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1c548-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="1c548-197">Vrátí předchozí akce `IActionResult`, ale uvnitř akce vrací buď [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) nebo [chybného požadavku](/dotnet/api/system.web.http.apicontroller.badrequest).</span><span class="sxs-lookup"><span data-stu-id="1c548-197">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) or [BadRequest](/dotnet/api/system.web.http.apicontroller.badrequest).</span></span> <span data-ttu-id="1c548-198">Datové poznámky se používají zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="1c548-198">Data annotations are used to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="1c548-199">Uspořádání akce s následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="1c548-199">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1c548-200">Používá se službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [ActionResult\<T >](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1).</span><span class="sxs-lookup"><span data-stu-id="1c548-200">NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1).</span></span> <span data-ttu-id="1c548-201">V důsledku toho může službou NSwag pouze odvodit návratový typ určené `T`.</span><span class="sxs-lookup"><span data-stu-id="1c548-201">Consequently, NSwag can only infer the return type defined by `T`.</span></span> <span data-ttu-id="1c548-202">Nejde odvodit další možné návratové typy v akci.</span><span class="sxs-lookup"><span data-stu-id="1c548-202">Other possible return types in the action cannot be inferred.</span></span> <span data-ttu-id="1c548-203">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1c548-203">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="1c548-204">Vrátí předchozí akce `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="1c548-204">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="1c548-205">V akci, vrací [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="1c548-205">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="1c548-206">Protože kontroler je doplněn [[objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut, [chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*) odpovědi je také možné,.</span><span class="sxs-lookup"><span data-stu-id="1c548-206">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="1c548-207">Další informace najdete v tématu [odpovědi HTTP 400 automatické](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="1c548-207">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="1c548-208">Datové poznámky se používají zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="1c548-208">Data annotations are used to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="1c548-209">Uspořádání akce s následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="1c548-209">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="1c548-210">V ASP.NET Core 2.2 nebo vyšší, vytváření názvů může sloužit jako alternativu k upravení explicitně jednotlivé akce s `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="1c548-210">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="1c548-211">Další informace naleznete v tématu <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="1c548-211">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="1c548-212">Generátoru Swagger můžete nyní přesně popište tuto akci a vygenerovaný klienti věděli, co se zobrazí při volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="1c548-212">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="1c548-213">Upravení všechny akce s těmito atributy důrazně doporučujeme.</span><span class="sxs-lookup"><span data-stu-id="1c548-213">Decorating all actions with these attributes is highly recommended.</span></span> <span data-ttu-id="1c548-214">Pokyny pro jaké odpovědi protokolu HTTP, by měla vrátit akce rozhraní API najdete v článku [specifikaci RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="1c548-214">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
