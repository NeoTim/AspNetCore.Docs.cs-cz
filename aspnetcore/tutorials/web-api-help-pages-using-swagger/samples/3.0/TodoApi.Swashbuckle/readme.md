---
page_type: sample
description: Naučte se, jak přidat swashbuckle do projektu webového rozhraní API ASP.NET Core pro integraci uživatelského rozhraní Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: 2b1da1d524eb18f1048314c544c64f82c22761e9
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69988982"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="44722-102">Začínáme s swashbuckle a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44722-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="44722-103">Při využívání webové rozhraní API, může vývojář náročné Principy jeho různé metody.</span><span class="sxs-lookup"><span data-stu-id="44722-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="44722-104">[Swagger](https://swagger.io/), označované také jako [OpenAPI](https://www.openapis.org/), řeší problém generování užitečné stránky dokumentace a nápovědu pro webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44722-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="44722-105">Poskytuje výhody, jako je například interaktivní dokumentace, generování klientských sad SDK a rozhraní API zjistitelnost.</span><span class="sxs-lookup"><span data-stu-id="44722-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="44722-106">V této ukázce se zobrazí [swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) implementace rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="44722-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="44722-107">Přidat a nakonfigurovat middleware Swagger</span><span class="sxs-lookup"><span data-stu-id="44722-107">Add and configure Swagger middleware</span></span>

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

<span data-ttu-id="44722-108">`Startup.Configure` V metodě povolte middleware pro poskytování generovaného dokumentu JSON a uživatelského rozhraní Swagger:</span><span class="sxs-lookup"><span data-stu-id="44722-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");In the 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="44722-109">Předchozí `UseSwaggerUI` volání metody povoluje middleware [statických souborů](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="44722-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="44722-110">Pokud cílíte .NET Framework nebo .NET Core 1. x, přidejte do projektu balíček NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .</span><span class="sxs-lookup"><span data-stu-id="44722-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="44722-111">Spusťte aplikaci a přejděte na `http://localhost:<port>/swagger/v1/swagger.json`adresu.</span><span class="sxs-lookup"><span data-stu-id="44722-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="44722-112">Vygenerovaný dokument popisující koncové body se zobrazí, jak je znázorněno ve [specifikaci Swagger (Swagger. JSON)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="44722-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="44722-113">Uživatelské rozhraní Swagger najdete na adrese `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="44722-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="44722-114">Prozkoumejte rozhraní API prostřednictvím uživatelského rozhraní Swagger a zahrňte je do jiných programů.</span><span class="sxs-lookup"><span data-stu-id="44722-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="44722-115">Pro obsluhu uživatelského rozhraní Swagger v kořenovém adresáři aplikace (`http://localhost:<port>/`) `RoutePrefix` nastavte vlastnost na prázdný řetězec:</span><span class="sxs-lookup"><span data-stu-id="44722-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="44722-116">Pokud používáte adresáře se službou IIS nebo reverzním proxy serverem, nastavte koncový bod Swagger na relativní cestu pomocí `./` předpony.</span><span class="sxs-lookup"><span data-stu-id="44722-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="44722-117">Například, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="44722-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="44722-118">Pomocí `/swagger/v1/swagger.json` nástroje instruuje aplikaci, aby hledala soubor JSON na skutečném kořenu adresy URL (plus předpona trasy, pokud se používá).</span><span class="sxs-lookup"><span data-stu-id="44722-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="44722-119">Použijte `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` například`http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`místo.</span><span class="sxs-lookup"><span data-stu-id="44722-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="44722-120">Přizpůsobení a rozšiřování</span><span class="sxs-lookup"><span data-stu-id="44722-120">Customize and extend</span></span>

<span data-ttu-id="44722-121">Swagger poskytuje možnosti pro dokumentaci k objektovému modelu a přizpůsobení uživatelského rozhraní tak, aby odpovídalo vašemu motivu.</span><span class="sxs-lookup"><span data-stu-id="44722-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="44722-122">`Startup` Do třídy přidejte následující obory názvů:</span><span class="sxs-lookup"><span data-stu-id="44722-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="44722-123">Informace a popis rozhraní API</span><span class="sxs-lookup"><span data-stu-id="44722-123">API info and description</span></span>

<span data-ttu-id="44722-124">Akce konfigurace předaná `AddSwaggerGen` metodě přidá informace, jako je autor, licence a popis:</span><span class="sxs-lookup"><span data-stu-id="44722-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

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

<span data-ttu-id="44722-125">Uživatelské rozhraní Swagger zobrazuje informace o verzi:</span><span class="sxs-lookup"><span data-stu-id="44722-125">The Swagger UI displays the version's information:</span></span>

![Uživatelské rozhraní Swagger s informacemi o verzi: Popis, autor a zobrazit další odkaz](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="44722-127">XML – komentáře</span><span class="sxs-lookup"><span data-stu-id="44722-127">XML comments</span></span>

<span data-ttu-id="44722-128">Komentáře XML lze povolit pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="44722-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44722-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44722-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44722-130">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **upravit < PROJECT_NAME >. csproj**.</span><span class="sxs-lookup"><span data-stu-id="44722-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="44722-131">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="44722-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="44722-132">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="44722-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="44722-133">V *oblast řešení*stiskněte **ovládací prvek** a klikněte na název projektu.</span><span class="sxs-lookup"><span data-stu-id="44722-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="44722-134">Přejděte do **nástroje** > **Upravit soubor**.</span><span class="sxs-lookup"><span data-stu-id="44722-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="44722-135">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="44722-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="44722-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44722-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="44722-137">Ručně přidejte zvýrazněné řádky do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="44722-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="44722-138">Povolení komentářů XML poskytuje ladicí informace pro nedokumentované veřejné typy a členy.</span><span class="sxs-lookup"><span data-stu-id="44722-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="44722-139">Nedokumentované typy a členy jsou označeny varovnou zprávou.</span><span class="sxs-lookup"><span data-stu-id="44722-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="44722-140">Například následující zpráva indikuje porušení kódu upozornění 1591:</span><span class="sxs-lookup"><span data-stu-id="44722-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="44722-141">Chcete-li potlačit upozornění v rámci projektu, definujte seznam kódů upozornění oddělených středníkem, které mají být v souboru projektu ignorovány.</span><span class="sxs-lookup"><span data-stu-id="44722-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="44722-142">Připojení kódů upozornění k `$(NoWarn);` aplikování [ C# výchozích hodnot](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .</span><span class="sxs-lookup"><span data-stu-id="44722-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="44722-143">Chcete-li potlačit upozornění pouze pro konkrétní členy, uveďte kód v direktivách preprocesoru [upozornění #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) .</span><span class="sxs-lookup"><span data-stu-id="44722-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="44722-144">Tento přístup je užitečný pro kód, který by neměl být zveřejněn prostřednictvím dokumentace rozhraní API. V následujícím příkladu je kód upozornění CS1591 ignorován pro celou `Program` třídu.</span><span class="sxs-lookup"><span data-stu-id="44722-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="44722-145">Vynucování kódu upozornění je obnoveno při zavření definice třídy.</span><span class="sxs-lookup"><span data-stu-id="44722-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="44722-146">Zadejte více kódů upozornění se seznamem odděleným čárkami.</span><span class="sxs-lookup"><span data-stu-id="44722-146">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="44722-147">Nakonfigurujte Swagger pro použití souboru XML, který je vygenerovaný předchozími pokyny.</span><span class="sxs-lookup"><span data-stu-id="44722-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="44722-148">Pro operační systémy Linux a jiné než Windows můžou názvy souborů a cesty rozlišovat velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="44722-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="44722-149">Například soubor *TodoApi. XML* je platný ve Windows, ale ne CentOS.</span><span class="sxs-lookup"><span data-stu-id="44722-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

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

<span data-ttu-id="44722-150">V předchozím kódu se reflexe používá k sestavení názvu souboru XML, který odpovídá tomuto projektu webového rozhraní API. [](/dotnet/csharp/programming-guide/concepts/reflection)</span><span class="sxs-lookup"><span data-stu-id="44722-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="44722-151">Vlastnost [AppContext. BaseDirectory](/dotnet/api/system.appcontext.basedirectory) se používá k vytvoření cesty k souboru XML.</span><span class="sxs-lookup"><span data-stu-id="44722-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="44722-152">Některé funkce Swagger (například schémat vstupních parametrů nebo metody HTTP a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML.</span><span class="sxs-lookup"><span data-stu-id="44722-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="44722-153">Pro většinu funkcí, konkrétně souhrny metod a popisy parametrů a kódů odpovědí, je použití souboru XML povinné.</span><span class="sxs-lookup"><span data-stu-id="44722-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="44722-154">Přidání komentářů se třemi lomítky k akci vylepšuje uživatelské rozhraní Swagger přidáním popisu do záhlaví oddílu.</span><span class="sxs-lookup"><span data-stu-id="44722-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="44722-155">Přidejte prvek `Delete` souhrnu > nad akci: [ \<](/dotnet/csharp/programming-guide/xmldoc/summary)</span><span class="sxs-lookup"><span data-stu-id="44722-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

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
<span data-ttu-id="44722-156">Uživatelské rozhraní Swagger zobrazí vnitřní text `<summary>` elementu předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="44722-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Uživatelské rozhraní Swagger, které zobrazuje komentář XML, odstraní konkrétní TodoItem.](sample_images/triple-slash-comments.png)

<span data-ttu-id="44722-159">Uživatelské rozhraní je založené na generovaném schématu JSON:</span><span class="sxs-lookup"><span data-stu-id="44722-159">The UI is driven by the generated JSON schema:</span></span>

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
<span data-ttu-id="44722-160">Do dokumentace k metodě Akcepřidejte>elementpoznámky.`Create` [ \<](/dotnet/csharp/programming-guide/xmldoc/remarks)</span><span class="sxs-lookup"><span data-stu-id="44722-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="44722-161">Doplňuje informace zadané v `<summary>` prvku a poskytuje robustnější uživatelské rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="44722-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="44722-162">Obsah `<remarks>` elementu se může skládat z textu, JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="44722-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

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
[ProducesResponseType(201)]
[ProducesResponseType(400)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="44722-163">Všimněte si vylepšení uživatelského rozhraní pomocí těchto dalších komentářů:</span><span class="sxs-lookup"><span data-stu-id="44722-163">Notice the UI enhancements with these additional comments:</span></span>

![Uživatelské rozhraní Swagger s dalšími zobrazenými komentáři](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="44722-165">Datové poznámky</span><span class="sxs-lookup"><span data-stu-id="44722-165">Data annotations</span></span>

<span data-ttu-id="44722-166">Naupravujte model pomocí atributů, které se nacházejí v oboru názvů [System. ComponentModel.](/dotnet/api/system.componentmodel.dataannotations) DataAnnotations, aby bylo možné lépe řídit součásti uživatelského rozhraní Swagger.</span><span class="sxs-lookup"><span data-stu-id="44722-166">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="44722-167">`[Required]` Přidejte atribut`Name` do vlastnosti`TodoItem` třídy:</span><span class="sxs-lookup"><span data-stu-id="44722-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

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

<span data-ttu-id="44722-168">Přítomnost tohoto atributu mění chování uživatelského rozhraní a mění základní schéma JSON:</span><span class="sxs-lookup"><span data-stu-id="44722-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="44722-169">`[Produces("application/json")]` Přidejte atribut do kontroleru rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="44722-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="44722-170">Jeho účelem je deklarovat, že akce kontroleru podporují typ obsahu odpovědi *Application/JSON*:</span><span class="sxs-lookup"><span data-stu-id="44722-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="44722-171">Rozevírací seznam **typ obsahu odpovědi** pro akce GET kontroleru vybere tento typ obsahu jako výchozí:</span><span class="sxs-lookup"><span data-stu-id="44722-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Uživatelské rozhraní Swagger s výchozím typem obsahu odpovědi](sample_images/json-response-content-type.png)

<span data-ttu-id="44722-173">Díky nárůstu využití datových poznámek ve webovém rozhraní API se stránky s nápovědu pro uživatelské rozhraní a rozhraní API stanou výstižnější a užitečnější.</span><span class="sxs-lookup"><span data-stu-id="44722-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="44722-174">Popis typů odpovědí</span><span class="sxs-lookup"><span data-stu-id="44722-174">Describe response types</span></span>

<span data-ttu-id="44722-175">Vývojáři, kteří využívají webové rozhraní API, mají největší obavy s&mdash;tím, co vrací konkrétně typy odpovědí a kódy chyb (Pokud ne Standard).</span><span class="sxs-lookup"><span data-stu-id="44722-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="44722-176">Typy odpovědí a kódy chyb jsou označeny v komentářích XML a datových anotacích.</span><span class="sxs-lookup"><span data-stu-id="44722-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="44722-177">`Create` Akce vrátí stavový kód HTTP 201 při úspěchu.</span><span class="sxs-lookup"><span data-stu-id="44722-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="44722-178">Stavový kód HTTP 400 se vrátí, když text odeslaného požadavku má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="44722-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="44722-179">Bez správné dokumentace v uživatelském rozhraní Swagger nemá spotřebitel znalosti o těchto očekávaných výsledcích.</span><span class="sxs-lookup"><span data-stu-id="44722-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="44722-180">Opravte tento problém tak, že přidáte zvýrazněné řádky v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="44722-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(201)]
[ProducesResponseType(400)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="44722-181">Uživatelské rozhraní Swagger teď jasně klade očekávané kódy odpovědí HTTP:</span><span class="sxs-lookup"><span data-stu-id="44722-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Uživatelské rozhraní Swagger, které zobrazuje popis třídy odpovědí POST, vrátí nově vytvořenou položku TODO ' a ' 400 – Pokud má položka hodnotu null pro stavový kód a důvod v rámci zpráv odpovědí.](sample_images/data-annotations-response-types.png)

<span data-ttu-id="44722-183">V ASP.NET Core 2,2 nebo novějších lze konvence použít jako alternativu k explicitnímu Upraveníí jednotlivých `[ProducesResponseType]`akcí.</span><span class="sxs-lookup"><span data-stu-id="44722-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="44722-184">Další informace najdete v tématu [použití konvencí webového rozhraní API](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="44722-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="44722-185">Informace o přizpůsobení uživatelského rozhraní najdete v těchto tématech: [Přizpůsobení uživatelského rozhraní](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="44722-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
