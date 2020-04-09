---
page_type: sample
description: Přečtěte si, jak přidat Swashbuckle do projektu ASP.NET core web API pro integraci uživatelského rozhraní Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659934"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="a88fa-102">Začínáme s Swashbuckle a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a88fa-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="a88fa-103">Při využívání webového rozhraní API může být pochopení jeho různých metod pro vývojáře náročné.</span><span class="sxs-lookup"><span data-stu-id="a88fa-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="a88fa-104">[Swagger](https://swagger.io/), také známý jako [OpenAPI](https://www.openapis.org/), řeší problém generování užitečné dokumentace a stránky nápovědy pro webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a88fa-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="a88fa-105">Poskytuje výhody, jako je interaktivní dokumentace, generování sady SDK klienta a zjistitelnost rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a88fa-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="a88fa-106">V této ukázce je zobrazeno [swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) implementace rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="a88fa-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="a88fa-107">Přidání a konfigurace middlewaru Swagger</span><span class="sxs-lookup"><span data-stu-id="a88fa-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="a88fa-108">V `Startup.Configure` metodě povolte middleware pro obsluhu generovaného dokumentu JSON a ui Swagger:</span><span class="sxs-lookup"><span data-stu-id="a88fa-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="a88fa-109">Předchozí `UseSwaggerUI` volání metody umožňuje [middleware statického souboru](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="a88fa-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="a88fa-110">Pokud cílíte na rozhraní .NET Framework nebo .NET Core 1.x, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="a88fa-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="a88fa-111">Spusťte aplikaci `http://localhost:<port>/swagger/v1/swagger.json`a přejděte na .</span><span class="sxs-lookup"><span data-stu-id="a88fa-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="a88fa-112">Vygenerovaný dokument popisující koncové body se zobrazí tak, jak je znázorněno ve [specifikaci Swagger (swagger.json).](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)</span><span class="sxs-lookup"><span data-stu-id="a88fa-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="a88fa-113">Swagger UI lze nalézt `http://localhost:<port>/swagger`na adrese .</span><span class="sxs-lookup"><span data-stu-id="a88fa-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="a88fa-114">Prozkoumejte rozhraní API prostřednictvím rozhraní Swagger A začleňte ho do jiných programů.</span><span class="sxs-lookup"><span data-stu-id="a88fa-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="a88fa-115">Chcete-li sloužit Swagger UI v`http://localhost:<port>/`kořenovém `RoutePrefix` adresáři aplikace ( ), nastavte vlastnost na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="a88fa-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="a88fa-116">Pokud používáte adresáře se službou IIS nebo reverzní proxy server, `./` nastavte koncový bod Swagger na relativní cestu pomocí předpony.</span><span class="sxs-lookup"><span data-stu-id="a88fa-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="a88fa-117">Například, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="a88fa-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="a88fa-118">Použití `/swagger/v1/swagger.json` pokyn aplikace hledat soubor JSON v pravé kořenové adresy URL (plus předpona trasy, pokud je použita).</span><span class="sxs-lookup"><span data-stu-id="a88fa-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="a88fa-119">Můžete například namísto `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` použít `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="a88fa-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="a88fa-120">Přizpůsobení a rozšíření</span><span class="sxs-lookup"><span data-stu-id="a88fa-120">Customize and extend</span></span>

<span data-ttu-id="a88fa-121">Swagger poskytuje možnosti pro dokumentaci objektového modelu a přizpůsobení ui tak, aby odpovídalo motivu.</span><span class="sxs-lookup"><span data-stu-id="a88fa-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="a88fa-122">Do `Startup` třídy přidejte následující obory názvů:</span><span class="sxs-lookup"><span data-stu-id="a88fa-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="a88fa-123">Informace a popis rozhraní API</span><span class="sxs-lookup"><span data-stu-id="a88fa-123">API info and description</span></span>

<span data-ttu-id="a88fa-124">Akce konfigurace předaná metodě `AddSwaggerGen` přidává informace, jako je autor, licence a popis:</span><span class="sxs-lookup"><span data-stu-id="a88fa-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="a88fa-125">Swagger UI zobrazuje informace o verzi:</span><span class="sxs-lookup"><span data-stu-id="a88fa-125">The Swagger UI displays the version's information:</span></span>

![Swagger UI s informacemi o verzi: popis, autor a další odkaz](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="a88fa-127">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="a88fa-127">XML comments</span></span>

<span data-ttu-id="a88fa-128">Komentáře XML lze povolit pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="a88fa-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="a88fa-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a88fa-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a88fa-130">Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **upravit <project_name>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="a88fa-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="a88fa-131">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="a88fa-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a88fa-132">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a88fa-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a88fa-133">V *panelu řešení*stiskněte **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="a88fa-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="a88fa-134">Přejděte na **Nástroje** > **upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="a88fa-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="a88fa-135">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="a88fa-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[<span data-ttu-id="a88fa-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a88fa-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a88fa-137">Ručně přidejte zvýrazněné řádky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="a88fa-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="a88fa-138">Povolení komentářů XML poskytuje ladicí informace pro nezdokumentované veřejné typy a členy.</span><span class="sxs-lookup"><span data-stu-id="a88fa-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="a88fa-139">Nedokumentované typy a členy jsou označeny varovnou zprávou.</span><span class="sxs-lookup"><span data-stu-id="a88fa-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="a88fa-140">Například následující zpráva označuje porušení kódu upozornění 1591:</span><span class="sxs-lookup"><span data-stu-id="a88fa-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="a88fa-141">Chcete-li potlačit upozornění pro celý projekt, definujte seznam kódů upozornění oddělených středníkem, který chcete v souboru projektu ignorovat.</span><span class="sxs-lookup"><span data-stu-id="a88fa-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="a88fa-142">Připojení kódy upozornění `$(NoWarn);` použije [c# výchozí hodnoty](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) příliš.</span><span class="sxs-lookup"><span data-stu-id="a88fa-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="a88fa-143">Chcete-li potlačit upozornění pouze pro určité členy, uzavřete kód v #pragma direktivy preprocesoru [upozornění.](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning)</span><span class="sxs-lookup"><span data-stu-id="a88fa-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="a88fa-144">Tento přístup je užitečný pro kód, který by neměl být vystaven prostřednictvím dokumentů rozhraní API. V následujícím příkladu je kód upozornění CS1591 `Program` ignorován pro celou třídu.</span><span class="sxs-lookup"><span data-stu-id="a88fa-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="a88fa-145">Vynucení kódu upozornění je obnoveno na konci definice třídy.</span><span class="sxs-lookup"><span data-stu-id="a88fa-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="a88fa-146">Zadejte více kódů upozornění se seznamem odděleným čárkami.</span><span class="sxs-lookup"><span data-stu-id="a88fa-146">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="a88fa-147">Nakonfigurujte Swagger tak, aby používal soubor XML, který je generován s předchozími pokyny.</span><span class="sxs-lookup"><span data-stu-id="a88fa-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="a88fa-148">U operačních systémů Linux nebo mimo operační systémy Windows mohou být názvy souborů a cesty rozlišována malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="a88fa-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="a88fa-149">Například soubor *TodoApi.XML* je platný v systému Windows, ale ne CentOS.</span><span class="sxs-lookup"><span data-stu-id="a88fa-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="a88fa-150">V předchozím kódu [reflection](/dotnet/csharp/programming-guide/concepts/reflection) se používá k vytvoření názvu souboru XML odpovídající ho projektu webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a88fa-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="a88fa-151">Vlastnost [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) se používá k vytvoření cesty k souboru XML.</span><span class="sxs-lookup"><span data-stu-id="a88fa-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="a88fa-152">Některé funkce Swagger (například schémata vstupních parametrů nebo http metody a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML.</span><span class="sxs-lookup"><span data-stu-id="a88fa-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="a88fa-153">U většiny funkcí, konkrétně souhrnů metod a popisů parametrů a kódů odpovědí, je použití souboru XML povinné.</span><span class="sxs-lookup"><span data-stu-id="a88fa-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="a88fa-154">Můžete k akci přidat také komentáře uvozené třemi lomítky, a vylepšit tak Swagger UI tím, že přidáte popis do hlavičky oddílu.</span><span class="sxs-lookup"><span data-stu-id="a88fa-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="a88fa-155">Nad `Delete` akci přidejte [ \<souhrnný prvek>:](/dotnet/csharp/programming-guide/xmldoc/summary)</span><span class="sxs-lookup"><span data-stu-id="a88fa-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="a88fa-156">Swagger ui zobrazí vnitřní text prvku předchozího `<summary>` kódu:</span><span class="sxs-lookup"><span data-stu-id="a88fa-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Swagger UI zobrazující komentář XML 'Odstraní konkrétní TodoItem.'](sample_images/triple-slash-comments.png)

<span data-ttu-id="a88fa-159">UI je řízeno generované schéma JSON:</span><span class="sxs-lookup"><span data-stu-id="a88fa-159">The UI is driven by the generated JSON schema:</span></span>

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
<span data-ttu-id="a88fa-160">Přidejte [ \<poznámky](/dotnet/csharp/programming-guide/xmldoc/remarks)>`Create` prvek do dokumentace metody akce.</span><span class="sxs-lookup"><span data-stu-id="a88fa-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="a88fa-161">Doplňuje informace uvedené `<summary>` v prvku a poskytuje robustnější Swagger uI.</span><span class="sxs-lookup"><span data-stu-id="a88fa-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="a88fa-162">Obsah `<remarks>` prvku se může skládat z textu, JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="a88fa-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="a88fa-163">Všimněte si vylepšení ui s těmito dalšími komentáři:</span><span class="sxs-lookup"><span data-stu-id="a88fa-163">Notice the UI enhancements with these additional comments:</span></span>

![Swagger UI s dalšíkomentáře zobrazeny](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="a88fa-165">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="a88fa-165">Data annotations</span></span>

<span data-ttu-id="a88fa-166">Označte model atributy, které se nacházejí v oboru názvů [System.ComponentModel.DataAnnotations,](/dotnet/api/system.componentmodel.dataannotations) které pomáhají řídit součásti uživatelského uživatelského uživatelského nastavení Swagger.</span><span class="sxs-lookup"><span data-stu-id="a88fa-166">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="a88fa-167">Přidejte `[Required]` atribut `Name` do vlastnosti třídy: `TodoItem`</span><span class="sxs-lookup"><span data-stu-id="a88fa-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="a88fa-168">Přítomnost tohoto atributu změní chování ui a změní základní schéma JSON:</span><span class="sxs-lookup"><span data-stu-id="a88fa-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="a88fa-169">Přidejte `[Produces("application/json")]` atribut do řadiče rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a88fa-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="a88fa-170">Jeho účelem je prohlásit, že akce správce podporují typ obsahu odpovědi *aplikace/json*:</span><span class="sxs-lookup"><span data-stu-id="a88fa-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="a88fa-171">Rozevírací nabídky **Typ obsahu odpovědi** vybere tento typ obsahu jako výchozí pro akce GET řadiče:</span><span class="sxs-lookup"><span data-stu-id="a88fa-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Swagger UI s výchozím typem obsahu odpovědi](sample_images/json-response-content-type.png)

<span data-ttu-id="a88fa-173">S tím, jak se zvyšuje využití datových anotací ve webovém rozhraní API, se stránky nápovědy uživatelského rozhraní a rozhraní API stávají popisnějšími a užitečnějšími.</span><span class="sxs-lookup"><span data-stu-id="a88fa-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="a88fa-174">Popsat typy odpovědí</span><span class="sxs-lookup"><span data-stu-id="a88fa-174">Describe response types</span></span>

<span data-ttu-id="a88fa-175">Vývojáři, kteří konzumují webové&mdash;rozhraní API, se nejvíce zajímají o to, co je vráceno konkrétně typy odpovědí a kódy chyb (pokud nejsou standardní).</span><span class="sxs-lookup"><span data-stu-id="a88fa-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="a88fa-176">Typy odpovědí a kódy chyb jsou označeny v poznámkách XML a datových poznámkách.</span><span class="sxs-lookup"><span data-stu-id="a88fa-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="a88fa-177">Akce `Create` vrátí stavový kód HTTP 201 na úspěch.</span><span class="sxs-lookup"><span data-stu-id="a88fa-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="a88fa-178">Stavový kód HTTP 400 je vrácen, pokud je zaúčtovaný text požadavku null.</span><span class="sxs-lookup"><span data-stu-id="a88fa-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="a88fa-179">Bez řádné dokumentace v ui Swagger, spotřebitel postrádá znalosti o těchto očekávaných výsledků.</span><span class="sxs-lookup"><span data-stu-id="a88fa-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="a88fa-180">Tento problém opravte přidáním zvýrazněných řádků v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a88fa-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="a88fa-181">Swagger UI nyní jasně dokumentuje očekávané kódy odpovědi HTTP:</span><span class="sxs-lookup"><span data-stu-id="a88fa-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Swagger UI zobrazující popis třídy odezvy POST "Vrátí nově vytvořenou položku todo" a "400 - Pokud je položka null" pro stavový kód a důvod v části Zprávy odpovědi](sample_images/data-annotations-response-types.png)

<span data-ttu-id="a88fa-183">V ASP.NET Jádrem 2.2 nebo novějším lze konvence použít jako `[ProducesResponseType]`alternativu k explicitnímu zdobení jednotlivých akcí pomocí .</span><span class="sxs-lookup"><span data-stu-id="a88fa-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="a88fa-184">Další informace naleznete v [tématu Použití konvencí webového rozhraní API](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="a88fa-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="a88fa-185">Informace o přizpůsobení hlavního nastavení najdete v [tématu Přizpůsobení](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="a88fa-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
