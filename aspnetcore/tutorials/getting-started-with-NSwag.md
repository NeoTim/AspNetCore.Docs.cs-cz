---
title: Začínáme s NSwag a ASP.NET Core
author: zuckerthoben
description: Naučte se, jak pomocí NSwag vygenerovat dokumentaci a stránky s nápovědu pro ASP.NET Core webové rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/21/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 23927e6ce0a7b29ce3f32d4e7f7d3f234257ca9b
ms.sourcegitcommit: eb2fe5ad2e82fab86ca952463af8d017ba659b25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416159"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="c3201-103">Začínáme s NSwag a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3201-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="c3201-104">Od [Christoph Nienaber](https://twitter.com/zuckerthoben), [Portoriko Suter](https://rsuter.com)a [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="c3201-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c3201-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3201-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="c3201-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3201-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="c3201-107">NSwag nabízí následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="c3201-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="c3201-108">Schopnost využít uživatelské rozhraní Swagger a generátor Swagger.</span><span class="sxs-lookup"><span data-stu-id="c3201-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="c3201-109">Flexibilní možnosti generování kódu.</span><span class="sxs-lookup"><span data-stu-id="c3201-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="c3201-110">Pomocí NSwag nepotřebujete existující rozhraní API&mdash;můžete použít rozhraní API třetích stran, která zahrnují Swagger a generují implementaci klienta.</span><span class="sxs-lookup"><span data-stu-id="c3201-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="c3201-111">NSwag vám umožňuje urychlit vývojový cyklus a snadno se přizpůsobit změnám rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c3201-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="c3201-112">Registrace middlewaru NSwag</span><span class="sxs-lookup"><span data-stu-id="c3201-112">Register the NSwag middleware</span></span>

<span data-ttu-id="c3201-113">Zaregistrujte middleware NSwag do:</span><span class="sxs-lookup"><span data-stu-id="c3201-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="c3201-114">Vygenerujte specifikaci Swagger pro implementované webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c3201-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="c3201-115">Slouží jako uživatelské rozhraní Swagger k procházení a testování webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c3201-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="c3201-116">Pokud chcete použít middleware ASP.NET Core [NSwag](https://github.com/RicoSuter/NSwag) , nainstalujte balíček NuGet [NSwag. AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) .</span><span class="sxs-lookup"><span data-stu-id="c3201-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="c3201-117">Tento balíček obsahuje middleware k vygenerování a obsluze specifikace Swagger, uživatelského rozhraní Swagger (v2 a V3) a [uživatelského rozhraní ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="c3201-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="c3201-118">K instalaci balíčku NuGet NSwag použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c3201-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c3201-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3201-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c3201-120">V okně **konzoly Správce balíčků** :</span><span class="sxs-lookup"><span data-stu-id="c3201-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="c3201-121">Přejít na **zobrazení** > **Další Windows** > **konzole správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="c3201-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="c3201-122">Přejděte do adresáře, ve kterém existuje soubor *TodoApi. csproj.*</span><span class="sxs-lookup"><span data-stu-id="c3201-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="c3201-123">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c3201-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="c3201-124">V dialogovém okně **Spravovat balíčky NuGet** :</span><span class="sxs-lookup"><span data-stu-id="c3201-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="c3201-125">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** > **Spravovat balíčky NuGet** .</span><span class="sxs-lookup"><span data-stu-id="c3201-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="c3201-126">Nastavte **zdroj balíčku** na "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="c3201-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="c3201-127">Do vyhledávacího pole zadejte "NSwag. AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="c3201-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="c3201-128">Na kartě **Procházet** vyberte balíček NSwag. AspNetCore a klikněte na **nainstalovat** .</span><span class="sxs-lookup"><span data-stu-id="c3201-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c3201-129">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c3201-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c3201-130">Klikněte pravým tlačítkem na složku *balíčky* v **oblast řešení** > **Přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="c3201-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="c3201-131">Nastavte rozevírací seznam pro **zdroj** okna **Přidat balíčky** na "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="c3201-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="c3201-132">Do vyhledávacího pole zadejte "NSwag. AspNetCore".</span><span class="sxs-lookup"><span data-stu-id="c3201-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="c3201-133">V podokně výsledků vyberte balíček NSwag. AspNetCore a klikněte na **Přidat balíček** .</span><span class="sxs-lookup"><span data-stu-id="c3201-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c3201-134">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="c3201-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c3201-135">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c3201-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="c3201-136">Přidat a nakonfigurovat middleware Swagger</span><span class="sxs-lookup"><span data-stu-id="c3201-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="c3201-137">Pomocí následujících kroků přidejte a nakonfigurujte Swagger v aplikaci ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c3201-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="c3201-138">V metodě `Startup.ConfigureServices` zaregistrujte požadované služby Swagger:</span><span class="sxs-lookup"><span data-stu-id="c3201-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="c3201-139">V metodě `Startup.Configure` povolte middleware pro poskytování vygenerované specifikace Swagger a uživatelského rozhraní Swagger:</span><span class="sxs-lookup"><span data-stu-id="c3201-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="c3201-140">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c3201-140">Launch the app.</span></span> <span data-ttu-id="c3201-141">Přejít na:</span><span class="sxs-lookup"><span data-stu-id="c3201-141">Navigate to:</span></span>
  * <span data-ttu-id="c3201-142">`http://localhost:<port>/swagger` k zobrazení uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="c3201-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="c3201-143">`http://localhost:<port>/swagger/v1/swagger.json` se zobrazí specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="c3201-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="c3201-144">Generování kódu</span><span class="sxs-lookup"><span data-stu-id="c3201-144">Code generation</span></span>

<span data-ttu-id="c3201-145">Můžete využít možnosti generování kódu NSwag výběrem jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="c3201-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="c3201-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; desktopovou aplikaci pro Windows pro generování kódu klienta rozhraní C# API v nástroji nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="c3201-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="c3201-147">Balíčky NuGet [NSwag. strategii. CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag. strategii. TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) pro generování kódu v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="c3201-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="c3201-148">NSwag z [příkazového řádku](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="c3201-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="c3201-149">Balíček NuGet [NSwag. MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild)</span><span class="sxs-lookup"><span data-stu-id="c3201-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="c3201-150">[Připojená služba Unchase openapi (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; připojená služba sady Visual Studio pro generování kódu klienta rozhraní API C# v nástroji nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="c3201-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; a Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="c3201-151">Také generuje C# řadiče pro služby openapi Services pomocí NSwag.</span><span class="sxs-lookup"><span data-stu-id="c3201-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="c3201-152">Generování kódu pomocí NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="c3201-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="c3201-153">Nainstalujte NSwagStudio podle pokynů v [úložišti GitHub NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="c3201-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="c3201-154">Spusťte NSwagStudio a v textovém poli **Adresa URL specifikace Swagger** zadejte adresu URL souboru *Swagger. JSON* .</span><span class="sxs-lookup"><span data-stu-id="c3201-154">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="c3201-155">Například *http://localhost:44354/swagger/v1/swagger.json* .</span><span class="sxs-lookup"><span data-stu-id="c3201-155">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="c3201-156">Kliknutím na tlačítko **vytvořit místní kopii** vygenerujete reprezentaci JSON vaší specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="c3201-156">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Vytvořit místní kopii specifikace Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="c3201-158">V oblasti **výstupy** klikněte na zaškrtávací políčko **klient CSharp** .</span><span class="sxs-lookup"><span data-stu-id="c3201-158">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="c3201-159">V závislosti na vašem projektu můžete také zvolit **klienta TypeScript** nebo **KONTROLER webového rozhraní API CSharp**.</span><span class="sxs-lookup"><span data-stu-id="c3201-159">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="c3201-160">Vyberete-li možnost **kontroler webového rozhraní API CSharp**, specifikace služby znovu sestaví službu a slouží jako reverzní generace.</span><span class="sxs-lookup"><span data-stu-id="c3201-160">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="c3201-161">Kliknutím na **generovat výstupy** vytvořte úplnou C# implementaci klienta projektu *TodoApi. NSwag* .</span><span class="sxs-lookup"><span data-stu-id="c3201-161">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="c3201-162">Pokud chcete zobrazit generovaný kód klienta, klikněte na kartu **klient CSharp** :</span><span class="sxs-lookup"><span data-stu-id="c3201-162">To see the generated client code, click the **CSharp Client** tab:</span></span>

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
> <span data-ttu-id="c3201-163">Kód C# klienta se generuje na základě výběrů na kartě **Nastavení** . Upravte nastavení tak, aby se prováděly úlohy, jako je například výchozí přejmenování oboru názvů a synchronní metoda generování.</span><span class="sxs-lookup"><span data-stu-id="c3201-163">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="c3201-164">Zkopírujte vygenerovaný C# kód do souboru v klientském projektu, který bude spotřebovávat rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c3201-164">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="c3201-165">Začněte spotřebovávat webové rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="c3201-165">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="c3201-166">Přizpůsobení dokumentace k rozhraní API</span><span class="sxs-lookup"><span data-stu-id="c3201-166">Customize API documentation</span></span>

<span data-ttu-id="c3201-167">Swagger poskytuje možnosti pro dokumentaci modelu objektu, aby se usnadnila spotřeba webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c3201-167">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="c3201-168">Informace a popis rozhraní API</span><span class="sxs-lookup"><span data-stu-id="c3201-168">API info and description</span></span>

<span data-ttu-id="c3201-169">V metodě `Startup.ConfigureServices` přidá konfigurační akce předaná metodě `AddSwaggerDocument` informace, jako je autor, licence a popis:</span><span class="sxs-lookup"><span data-stu-id="c3201-169">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="c3201-170">Uživatelské rozhraní Swagger zobrazuje informace o verzi:</span><span class="sxs-lookup"><span data-stu-id="c3201-170">The Swagger UI displays the version's information:</span></span>

![Uživatelské rozhraní Swagger s informacemi o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="c3201-172">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="c3201-172">XML comments</span></span>

<span data-ttu-id="c3201-173">Chcete-li povolit komentáře XML, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="c3201-173">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c3201-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3201-174">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="c3201-175">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **upravit < PROJECT_NAME >. csproj**.</span><span class="sxs-lookup"><span data-stu-id="c3201-175">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="c3201-176">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="c3201-176">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="c3201-177">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="c3201-177">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="c3201-178">V části **výstup** na kartě **sestavení** zaškrtněte políčko **soubor dokumentace XML** .</span><span class="sxs-lookup"><span data-stu-id="c3201-178">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c3201-179">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c3201-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="c3201-180">V *oblast řešení*stiskněte **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="c3201-180">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="c3201-181">Přejděte na **nástroje** > **Upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="c3201-181">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="c3201-182">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="c3201-182">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="c3201-183">Otevřete dialogové okno **Možnosti projektu** > **kompilátor** **sestavení** ></span><span class="sxs-lookup"><span data-stu-id="c3201-183">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="c3201-184">V části **Obecné možnosti** zaškrtněte políčko **generovat dokumentaci XML** .</span><span class="sxs-lookup"><span data-stu-id="c3201-184">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c3201-185">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="c3201-185">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c3201-186">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="c3201-186">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="c3201-187">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="c3201-187">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="c3201-188">Vzhledem k tomu, že NSwag používá [reflexi](/dotnet/csharp/programming-guide/concepts/reflection)a doporučený návratový typ pro akce webového rozhraní API je [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nemůže odvodit, jakou akci dělá a co vrátí.</span><span class="sxs-lookup"><span data-stu-id="c3201-188">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="c3201-189">Vezměte v úvahu následující příklad:</span><span class="sxs-lookup"><span data-stu-id="c3201-189">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="c3201-190">Předchozí akce vrátí `IActionResult`, ale v akci, která vrací hodnotu [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) nebo [důvodu chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*).</span><span class="sxs-lookup"><span data-stu-id="c3201-190">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="c3201-191">Pomocí datových poznámek můžete klientům sdělit, které stavové kódy HTTP má tato akce vrátit.</span><span class="sxs-lookup"><span data-stu-id="c3201-191">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="c3201-192">Naupravujte akci následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="c3201-192">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c3201-193">Vzhledem k tomu, že NSwag používá [reflexi](/dotnet/csharp/programming-guide/concepts/reflection)a doporučený návratový typ pro akce webového rozhraní API je [ActionResult\<t >](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), může odvodit jenom návratový typ definovaný `T`.</span><span class="sxs-lookup"><span data-stu-id="c3201-193">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="c3201-194">Nemůžete automaticky odvodit jiné možné návratové typy.</span><span class="sxs-lookup"><span data-stu-id="c3201-194">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="c3201-195">Vezměte v úvahu následující příklad:</span><span class="sxs-lookup"><span data-stu-id="c3201-195">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="c3201-196">Předchozí akce vrátí `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="c3201-196">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="c3201-197">V rámci akce vrátí [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="c3201-197">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="c3201-198">Vzhledem k tomu, že kontroler je upraven pomocí atributu [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) , je možné, že je také k dispozici odpověď [důvodu chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*) .</span><span class="sxs-lookup"><span data-stu-id="c3201-198">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="c3201-199">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="c3201-199">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="c3201-200">Pomocí datových poznámek můžete klientům sdělit, které stavové kódy HTTP má tato akce vrátit.</span><span class="sxs-lookup"><span data-stu-id="c3201-200">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="c3201-201">Naupravujte akci následujícími atributy:</span><span class="sxs-lookup"><span data-stu-id="c3201-201">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="c3201-202">V ASP.NET Core 2,2 nebo novějších můžete místo explicitně upravení jednotlivé akce s `[ProducesResponseType]`použít konvence.</span><span class="sxs-lookup"><span data-stu-id="c3201-202">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="c3201-203">Další informace najdete v tématu <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="c3201-203">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="c3201-204">Generátor Swagger teď může tuto akci přesně popsat a vygenerovaných klientů věděli, co získají při volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="c3201-204">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="c3201-205">Jako doporučení seupravte všechny akce pomocí těchto atributů.</span><span class="sxs-lookup"><span data-stu-id="c3201-205">As a recommendation, decorate all actions with these attributes.</span></span>

<span data-ttu-id="c3201-206">Pokyny k vrácení odpovědí HTTP, které vaše akce rozhraní API musí vrátit, najdete v článku [specifikace RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="c3201-206">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
