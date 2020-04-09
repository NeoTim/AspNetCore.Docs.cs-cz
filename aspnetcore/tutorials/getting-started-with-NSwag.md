---
title: Začínáme s NSwag a ASP.NET Core
author: zuckerthoben
description: Naučte se, jak pomocí NSwag generovat dokumentaci a stránky nápovědy pro ASP.NET základní webové rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 3eae5d3c66204a10806a8036c8f114af6c501b2c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666052"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="05b4d-103">Začínáme s NSwag a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05b4d-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="05b4d-104">[Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), a [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="05b4d-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="05b4d-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="05b4d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="05b4d-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="05b4d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="05b4d-107">NSwag nabízí následující funkce:</span><span class="sxs-lookup"><span data-stu-id="05b4d-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="05b4d-108">Schopnost využít Swagger UI a Swagger generátor.</span><span class="sxs-lookup"><span data-stu-id="05b4d-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="05b4d-109">Flexibilní možnosti generování kódu.</span><span class="sxs-lookup"><span data-stu-id="05b4d-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="05b4d-110">S NSwag, nepotřebujete existující rozhraní&mdash;API můžete použít rozhraní API třetích stran, které obsahují Swagger a generovat implementaci klienta.</span><span class="sxs-lookup"><span data-stu-id="05b4d-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="05b4d-111">NSwag umožňuje urychlit vývojový cyklus a snadno se přizpůsobit změnám rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="05b4d-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="05b4d-112">Zaregistrujte middleware NSwag</span><span class="sxs-lookup"><span data-stu-id="05b4d-112">Register the NSwag middleware</span></span>

<span data-ttu-id="05b4d-113">Zaregistrujte middleware NSwag na:</span><span class="sxs-lookup"><span data-stu-id="05b4d-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="05b4d-114">Vygenerujte specifikaci Swagger pro implementované webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="05b4d-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="05b4d-115">Slouží Swagger uživatelskérozhraní procházet a testovat webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="05b4d-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="05b4d-116">Chcete-li použít [middleware NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core, nainstalujte balíček [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="05b4d-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="05b4d-117">Tento balíček obsahuje middleware pro generování a poskytování specifikace Swagger, Swagger UI (v2 a v3) a [ReDoc UI](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="05b4d-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="05b4d-118">K instalaci balíčku NSwag NuGet použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="05b4d-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05b4d-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b4d-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05b4d-120">Z okna **Konzoly správce balíčků:**</span><span class="sxs-lookup"><span data-stu-id="05b4d-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="05b4d-121">Přejít na **zobrazit** > další**konzolu Správce balíčků systému** **Windows** > </span><span class="sxs-lookup"><span data-stu-id="05b4d-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="05b4d-122">Přejděte do adresáře, ve kterém existuje soubor *TodoApi.csproj.*</span><span class="sxs-lookup"><span data-stu-id="05b4d-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="05b4d-123">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="05b4d-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="05b4d-124">V dialogovém okně **Spravovat balíčky NuGet:**</span><span class="sxs-lookup"><span data-stu-id="05b4d-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="05b4d-125">Klikněte pravým tlačítkem myši na projekt v **Průzkumníku** > řešení**Spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="05b4d-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="05b4d-126">Nastavit **zdroj balíčku** na "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="05b4d-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="05b4d-127">Do vyhledávacího pole zadejte "NSwag.AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="05b4d-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="05b4d-128">Na kartě **Procházet** vyberte balíček NSwag.AspNetCore a klikněte na **Instalovat.**</span><span class="sxs-lookup"><span data-stu-id="05b4d-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05b4d-129">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="05b4d-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05b4d-130">Klikněte pravým tlačítkem myši na složku *Balíčky* v **panelu řešení** > **Přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="05b4d-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="05b4d-131">Nastavení rozbalovacího okna **Zdroj** v okně **Přidat balíčky** na hodnotu "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="05b4d-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="05b4d-132">Do vyhledávacího pole zadejte "NSwag.AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="05b4d-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="05b4d-133">V podokně výsledků vyberte balíček NSwag.AspNetCore a klikněte na **Přidat balíček.**</span><span class="sxs-lookup"><span data-stu-id="05b4d-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="05b4d-134">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="05b4d-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="05b4d-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="05b4d-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="05b4d-136">Přidání a konfigurace middlewaru Swagger</span><span class="sxs-lookup"><span data-stu-id="05b4d-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="05b4d-137">Přidejte a nakonfigurujte Swagger v aplikaci ASP.NET Core provedením následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="05b4d-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="05b4d-138">V `Startup.ConfigureServices` metodě zaregistrujte požadované služby Swagger:</span><span class="sxs-lookup"><span data-stu-id="05b4d-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="05b4d-139">V `Startup.Configure` metodě povolte middleware pro obsluhu generované specifikace Swagger a ui Swagger:</span><span class="sxs-lookup"><span data-stu-id="05b4d-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="05b4d-140">Spustit aplikaci</span><span class="sxs-lookup"><span data-stu-id="05b4d-140">Launch the app.</span></span> <span data-ttu-id="05b4d-141">Přejděte na:</span><span class="sxs-lookup"><span data-stu-id="05b4d-141">Navigate to:</span></span>
  * <span data-ttu-id="05b4d-142">`http://localhost:<port>/swagger`zobrazíte ui swagger.</span><span class="sxs-lookup"><span data-stu-id="05b4d-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="05b4d-143">`http://localhost:<port>/swagger/v1/swagger.json`zobrazíte specifikaci Swagger.</span><span class="sxs-lookup"><span data-stu-id="05b4d-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="05b4d-144">Generování kódu</span><span class="sxs-lookup"><span data-stu-id="05b4d-144">Code generation</span></span>

<span data-ttu-id="05b4d-145">Můžete využít funkce generování kódu NSwag výběrem jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="05b4d-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="05b4d-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; desktopová aplikace pro Windows pro generování kódu klienta ROZHRANÍ API v C# nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="05b4d-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="05b4d-147">[Balíčky NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet pro generování kódu uvnitř projektu.</span><span class="sxs-lookup"><span data-stu-id="05b4d-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="05b4d-148">NSwag z [příkazového řádku](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="05b4d-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="05b4d-149">Balíček [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet.</span><span class="sxs-lookup"><span data-stu-id="05b4d-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="05b4d-150">[Unchase OpenAPI (Swagger) Připojená služba](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; Visual Studio připojená služba pro generování kódu klienta rozhraní API v jazyce C# nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="05b4d-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; a Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="05b4d-151">Také generuje řadiče C# pro služby OpenAPI s NSwag.</span><span class="sxs-lookup"><span data-stu-id="05b4d-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="05b4d-152">Generovat kód s NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="05b4d-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="05b4d-153">Nainstalujte NSwagStudio podle pokynů v [úložišti NSwagStudio GitHub](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="05b4d-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span> <span data-ttu-id="05b4d-154">Na stránce vydání NSwag si můžete stáhnout xcopy verzi, kterou lze spustit bez oprávnění k instalaci a administrátoru.</span><span class="sxs-lookup"><span data-stu-id="05b4d-154">On the NSwag release page you can download an xcopy version which can be started without installation and admin privileges.</span></span>
* <span data-ttu-id="05b4d-155">Spusťte NSwagStudio a zadejte adresu URL souboru *swagger.json* do textového pole **URL specifikace Swagger.**</span><span class="sxs-lookup"><span data-stu-id="05b4d-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="05b4d-156">Například *http://localhost:44354/swagger/v1/swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="05b4d-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="05b4d-157">Kliknutím na tlačítko **Vytvořit místní kopii** vygenerujete reprezentaci JSON specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="05b4d-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Vytvořit místní kopii specifikace Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="05b4d-159">V oblasti **Výstupy** klikněte na zaškrtávací políčko **CSharp Client** .</span><span class="sxs-lookup"><span data-stu-id="05b4d-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="05b4d-160">V závislosti na projektu můžete také zvolit **TypeScript Client** nebo **CSharp Web API Controller**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="05b4d-161">Pokud vyberete **CSharp Web API Controller**, specifikace služby znovu obnoví službu, která slouží jako reverzní generace.</span><span class="sxs-lookup"><span data-stu-id="05b4d-161">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="05b4d-162">Kliknutím na **generovat výstupy** vytvoříte úplnou implementaci klienta Jazyka C# projektu *TodoApi.NSwag.*</span><span class="sxs-lookup"><span data-stu-id="05b4d-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="05b4d-163">Pokud chcete zobrazit generovaný kód klienta, klikněte na kartu **CSharp Client:**</span><span class="sxs-lookup"><span data-stu-id="05b4d-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

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
> <span data-ttu-id="05b4d-164">Klientský kód jazyka C# je generován na základě výběrů na kartě **Nastavení.**</span><span class="sxs-lookup"><span data-stu-id="05b4d-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="05b4d-165">Zkopírujte vygenerovaný kód Jazyka C# do souboru v klientském projektu, který bude využívat rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="05b4d-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="05b4d-166">Začněte spotřebovávat webové rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="05b4d-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="05b4d-167">Přizpůsobení dokumentace k rozhraní API</span><span class="sxs-lookup"><span data-stu-id="05b4d-167">Customize API documentation</span></span>

<span data-ttu-id="05b4d-168">Swagger poskytuje možnosti pro dokumentaci objektového modelu pro usnadnění spotřeby webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="05b4d-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="05b4d-169">Informace a popis rozhraní API</span><span class="sxs-lookup"><span data-stu-id="05b4d-169">API info and description</span></span>

<span data-ttu-id="05b4d-170">V `Startup.ConfigureServices` metodě akce konfigurace předané metodě `AddSwaggerDocument` přidá informace, jako je autor, licence a popis:</span><span class="sxs-lookup"><span data-stu-id="05b4d-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="05b4d-171">Swagger UI zobrazuje informace o verzi:</span><span class="sxs-lookup"><span data-stu-id="05b4d-171">The Swagger UI displays the version's information:</span></span>

![Swagger UI s informacemi o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="05b4d-173">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="05b4d-173">XML comments</span></span>

<span data-ttu-id="05b4d-174">Chcete-li povolit komentáře XML, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="05b4d-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05b4d-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b4d-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="05b4d-176">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **upravit <project_name>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="05b4d-177">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="05b4d-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="05b4d-178">Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **Vlastnosti.**</span><span class="sxs-lookup"><span data-stu-id="05b4d-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="05b4d-179">Zaškrtněte **políčko Soubor dokumentace XML** v části **Výstup** na kartě **Sestavení.**</span><span class="sxs-lookup"><span data-stu-id="05b4d-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05b4d-180">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="05b4d-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="05b4d-181">V *panelu řešení*stiskněte **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="05b4d-181">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="05b4d-182">Přejděte na **Nástroje** > **upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="05b4d-183">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="05b4d-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="05b4d-184">Otevření dialogového **okna Možnosti projektu** > **kompilátor** **sestavení** ></span><span class="sxs-lookup"><span data-stu-id="05b4d-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="05b4d-185">Zaškrtněte políčko **Generovat dokumentaci XML** v části **Obecné možnosti.**</span><span class="sxs-lookup"><span data-stu-id="05b4d-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-cli"></a>[<span data-ttu-id="05b4d-186">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="05b4d-186">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="05b4d-187">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="05b4d-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="05b4d-188">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="05b4d-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="05b4d-189">Vzhledem k tomu, že NSwag používá [reflexe](/dotnet/csharp/programming-guide/concepts/reflection)a doporučený návratový typ pro akce webového rozhraní API je [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nelze odvodit, co vaše akce dělá a co vrátí.</span><span class="sxs-lookup"><span data-stu-id="05b4d-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="05b4d-190">Uvažujte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="05b4d-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="05b4d-191">Předchozí akce vrátí `IActionResult`, ale uvnitř akce je vrácení buď [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) nebo [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span><span class="sxs-lookup"><span data-stu-id="05b4d-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="05b4d-192">Pomocí datových anotací sdělte klientům, které kódy stavu HTTP je známo, že tato akce vrací.</span><span class="sxs-lookup"><span data-stu-id="05b4d-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="05b4d-193">Označte akci následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="05b4d-193">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="05b4d-194">Vzhledem k tomu, že NSwag používá [reflection](/dotnet/csharp/programming-guide/concepts/reflection)a doporučený návratový typ pro akce webového rozhraní API je [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), může pouze odvodit návratový typ definovaný . `T`</span><span class="sxs-lookup"><span data-stu-id="05b4d-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="05b4d-195">Nelze automaticky odvodit jiné možné typy vrácení.</span><span class="sxs-lookup"><span data-stu-id="05b4d-195">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="05b4d-196">Uvažujte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="05b4d-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="05b4d-197">Předchozí akce vrátí `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="05b4d-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="05b4d-198">Uvnitř akce se vrací [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="05b4d-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="05b4d-199">Vzhledem k [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) tomu, že řadič má atribut, [badrequest](xref:System.Web.Http.ApiController.BadRequest*) odpověď je možné také.</span><span class="sxs-lookup"><span data-stu-id="05b4d-199">Since the controller has the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="05b4d-200">Další informace naleznete [v tématu Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="05b4d-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="05b4d-201">Pomocí datových anotací sdělte klientům, které kódy stavu HTTP je známo, že tato akce vrací.</span><span class="sxs-lookup"><span data-stu-id="05b4d-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="05b4d-202">Označte akci následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="05b4d-202">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="05b4d-203">V ASP.NET jádrem 2.2 nebo novějším můžete použít konvence `[ProducesResponseType]`namísto explicitního zdobení jednotlivých akcí pomocí .</span><span class="sxs-lookup"><span data-stu-id="05b4d-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="05b4d-204">Další informace naleznete v tématu <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="05b4d-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="05b4d-205">Generátor Swagger nyní můžete přesně popsat tuto akci a generované klienti vědí, co obdrží při volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="05b4d-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="05b4d-206">Jako doporučení označte všechny akce těmito atributy.</span><span class="sxs-lookup"><span data-stu-id="05b4d-206">As a recommendation, mark all actions with these attributes.</span></span>

<span data-ttu-id="05b4d-207">Pokyny k tomu, jaké odpovědi HTTP by měly být vráceny vašim akcím rozhraní API, naleznete ve [specifikaci RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="05b4d-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
