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
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Začínáme s Swashbuckle a ASP.NET Core

Při využívání webového rozhraní API může být pochopení jeho různých metod pro vývojáře náročné. [Swagger](https://swagger.io/), také známý jako [OpenAPI](https://www.openapis.org/), řeší problém generování užitečné dokumentace a stránky nápovědy pro webová rozhraní API. Poskytuje výhody, jako je interaktivní dokumentace, generování sady SDK klienta a zjistitelnost rozhraní API.

V této ukázce je zobrazeno [swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) implementace rozhraní .NET.

## <a name="add-and-configure-swagger-middleware"></a>Přidání a konfigurace middlewaru Swagger

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

V `Startup.Configure` metodě povolte middleware pro obsluhu generovaného dokumentu JSON a ui Swagger:

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

Předchozí `UseSwaggerUI` volání metody umožňuje [middleware statického souboru](https://docs.microsoft.com/aspnet/core/fundamentals/static-files). Pokud cílíte na rozhraní .NET Framework nebo .NET Core 1.x, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet.

Spusťte aplikaci `http://localhost:<port>/swagger/v1/swagger.json`a přejděte na . Vygenerovaný dokument popisující koncové body se zobrazí tak, jak je znázorněno ve [specifikaci Swagger (swagger.json).](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)

Swagger UI lze nalézt `http://localhost:<port>/swagger`na adrese . Prozkoumejte rozhraní API prostřednictvím rozhraní Swagger A začleňte ho do jiných programů.

> [!TIP]
> Chcete-li sloužit Swagger UI v`http://localhost:<port>/`kořenovém `RoutePrefix` adresáři aplikace ( ), nastavte vlastnost na prázdný řetězec:
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

Pokud používáte adresáře se službou IIS nebo reverzní proxy server, `./` nastavte koncový bod Swagger na relativní cestu pomocí předpony. Například, `./swagger/v1/swagger.json`. Použití `/swagger/v1/swagger.json` pokyn aplikace hledat soubor JSON v pravé kořenové adresy URL (plus předpona trasy, pokud je použita). Můžete například namísto `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` použít `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Přizpůsobení a rozšíření

Swagger poskytuje možnosti pro dokumentaci objektového modelu a přizpůsobení ui tak, aby odpovídalo motivu.

Do `Startup` třídy přidejte následující obory názvů:
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informace a popis rozhraní API

Akce konfigurace předaná metodě `AddSwaggerGen` přidává informace, jako je autor, licence a popis:

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

Swagger UI zobrazuje informace o verzi:

![Swagger UI s informacemi o verzi: popis, autor a další odkaz](sample_images/custom-info.png)

### <a name="xml-comments"></a>XML – komentáře

Komentáře XML lze povolit pomocí následujících přístupů:

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **upravit <project_name>.csproj**.
* Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V *panelu řešení*stiskněte **ovládací prvek** a klikněte na název projektu. Přejděte na **Nástroje** > **upravit soubor**.
* Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

Povolení komentářů XML poskytuje ladicí informace pro nezdokumentované veřejné typy a členy. Nedokumentované typy a členy jsou označeny varovnou zprávou. Například následující zpráva označuje porušení kódu upozornění 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Chcete-li potlačit upozornění pro celý projekt, definujte seznam kódů upozornění oddělených středníkem, který chcete v souboru projektu ignorovat. Připojení kódy upozornění `$(NoWarn);` použije [c# výchozí hodnoty](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) příliš.

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

Chcete-li potlačit upozornění pouze pro určité členy, uzavřete kód v #pragma direktivy preprocesoru [upozornění.](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) Tento přístup je užitečný pro kód, který by neměl být vystaven prostřednictvím dokumentů rozhraní API. V následujícím příkladu je kód upozornění CS1591 `Program` ignorován pro celou třídu. Vynucení kódu upozornění je obnoveno na konci definice třídy. Zadejte více kódů upozornění se seznamem odděleným čárkami.

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

Nakonfigurujte Swagger tak, aby používal soubor XML, který je generován s předchozími pokyny. U operačních systémů Linux nebo mimo operační systémy Windows mohou být názvy souborů a cesty rozlišována malá a velká písmena. Například soubor *TodoApi.XML* je platný v systému Windows, ale ne CentOS.

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

V předchozím kódu [reflection](/dotnet/csharp/programming-guide/concepts/reflection) se používá k vytvoření názvu souboru XML odpovídající ho projektu webového rozhraní API. Vlastnost [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) se používá k vytvoření cesty k souboru XML. Některé funkce Swagger (například schémata vstupních parametrů nebo http metody a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML. U většiny funkcí, konkrétně souhrnů metod a popisů parametrů a kódů odpovědí, je použití souboru XML povinné.

Můžete k akci přidat také komentáře uvozené třemi lomítky, a vylepšit tak Swagger UI tím, že přidáte popis do hlavičky oddílu. Nad `Delete` akci přidejte [ \<souhrnný prvek>:](/dotnet/csharp/programming-guide/xmldoc/summary)

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
Swagger ui zobrazí vnitřní text prvku předchozího `<summary>` kódu:

![Swagger UI zobrazující komentář XML 'Odstraní konkrétní TodoItem.' pro metodu DELETE](sample_images/triple-slash-comments.png)

UI je řízeno generované schéma JSON:

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
Přidejte [ \<poznámky](/dotnet/csharp/programming-guide/xmldoc/remarks)>`Create` prvek do dokumentace metody akce. Doplňuje informace uvedené `<summary>` v prvku a poskytuje robustnější Swagger uI. Obsah `<remarks>` prvku se může skládat z textu, JSON nebo XML.

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
Všimněte si vylepšení ui s těmito dalšími komentáři:

![Swagger UI s dalšíkomentáře zobrazeny](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Datové poznámky

Označte model atributy, které se nacházejí v oboru názvů [System.ComponentModel.DataAnnotations,](/dotnet/api/system.componentmodel.dataannotations) které pomáhají řídit součásti uživatelského uživatelského uživatelského nastavení Swagger.

Přidejte `[Required]` atribut `Name` do vlastnosti třídy: `TodoItem`

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

Přítomnost tohoto atributu změní chování ui a změní základní schéma JSON:

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

Přidejte `[Produces("application/json")]` atribut do řadiče rozhraní API. Jeho účelem je prohlásit, že akce správce podporují typ obsahu odpovědi *aplikace/json*:

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
Rozevírací nabídky **Typ obsahu odpovědi** vybere tento typ obsahu jako výchozí pro akce GET řadiče:

![Swagger UI s výchozím typem obsahu odpovědi](sample_images/json-response-content-type.png)

S tím, jak se zvyšuje využití datových anotací ve webovém rozhraní API, se stránky nápovědy uživatelského rozhraní a rozhraní API stávají popisnějšími a užitečnějšími.

### <a name="describe-response-types"></a>Popsat typy odpovědí

Vývojáři, kteří konzumují webové&mdash;rozhraní API, se nejvíce zajímají o to, co je vráceno konkrétně typy odpovědí a kódy chyb (pokud nejsou standardní). Typy odpovědí a kódy chyb jsou označeny v poznámkách XML a datových poznámkách.

Akce `Create` vrátí stavový kód HTTP 201 na úspěch. Stavový kód HTTP 400 je vrácen, pokud je zaúčtovaný text požadavku null. Bez řádné dokumentace v ui Swagger, spotřebitel postrádá znalosti o těchto očekávaných výsledků. Tento problém opravte přidáním zvýrazněných řádků v následujícím příkladu:

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

Swagger UI nyní jasně dokumentuje očekávané kódy odpovědi HTTP:

![Swagger UI zobrazující popis třídy odezvy POST "Vrátí nově vytvořenou položku todo" a "400 - Pokud je položka null" pro stavový kód a důvod v části Zprávy odpovědi](sample_images/data-annotations-response-types.png)

V ASP.NET Jádrem 2.2 nebo novějším lze konvence použít jako `[ProducesResponseType]`alternativu k explicitnímu zdobení jednotlivých akcí pomocí . Další informace naleznete v [tématu Použití konvencí webového rozhraní API](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).

Informace o přizpůsobení hlavního nastavení najdete v [tématu Přizpůsobení](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
