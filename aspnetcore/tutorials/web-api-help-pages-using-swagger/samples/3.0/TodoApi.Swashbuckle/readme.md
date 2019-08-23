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
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Začínáme s swashbuckle a ASP.NET Core

Při využívání webové rozhraní API, může vývojář náročné Principy jeho různé metody. [Swagger](https://swagger.io/), označované také jako [OpenAPI](https://www.openapis.org/), řeší problém generování užitečné stránky dokumentace a nápovědu pro webová rozhraní API. Poskytuje výhody, jako je například interaktivní dokumentace, generování klientských sad SDK a rozhraní API zjistitelnost.

V této ukázce se zobrazí [swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) implementace rozhraní .NET.

## <a name="add-and-configure-swagger-middleware"></a>Přidat a nakonfigurovat middleware Swagger

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

`Startup.Configure` V metodě povolte middleware pro poskytování generovaného dokumentu JSON a uživatelského rozhraní Swagger:

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

Předchozí `UseSwaggerUI` volání metody povoluje middleware [statických souborů](https://docs.microsoft.com/aspnet/core/fundamentals/static-files). Pokud cílíte .NET Framework nebo .NET Core 1. x, přidejte do projektu balíček NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .

Spusťte aplikaci a přejděte na `http://localhost:<port>/swagger/v1/swagger.json`adresu. Vygenerovaný dokument popisující koncové body se zobrazí, jak je znázorněno ve [specifikaci Swagger (Swagger. JSON)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

Uživatelské rozhraní Swagger najdete na adrese `http://localhost:<port>/swagger`. Prozkoumejte rozhraní API prostřednictvím uživatelského rozhraní Swagger a zahrňte je do jiných programů.

> [!TIP]
> Pro obsluhu uživatelského rozhraní Swagger v kořenovém adresáři aplikace (`http://localhost:<port>/`) `RoutePrefix` nastavte vlastnost na prázdný řetězec:
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

Pokud používáte adresáře se službou IIS nebo reverzním proxy serverem, nastavte koncový bod Swagger na relativní cestu pomocí `./` předpony. Například, `./swagger/v1/swagger.json`. Pomocí `/swagger/v1/swagger.json` nástroje instruuje aplikaci, aby hledala soubor JSON na skutečném kořenu adresy URL (plus předpona trasy, pokud se používá). Použijte `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` například`http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`místo.

## <a name="customize-and-extend"></a>Přizpůsobení a rozšiřování

Swagger poskytuje možnosti pro dokumentaci k objektovému modelu a přizpůsobení uživatelského rozhraní tak, aby odpovídalo vašemu motivu.

`Startup` Do třídy přidejte následující obory názvů:
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informace a popis rozhraní API

Akce konfigurace předaná `AddSwaggerGen` metodě přidá informace, jako je autor, licence a popis:

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

Uživatelské rozhraní Swagger zobrazuje informace o verzi:

![Uživatelské rozhraní Swagger s informacemi o verzi: Popis, autor a zobrazit další odkaz](sample_images/custom-info.png)

### <a name="xml-comments"></a>XML – komentáře

Komentáře XML lze povolit pomocí následujících přístupů:

#### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **upravit < PROJECT_NAME >. csproj**.
* Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* V *oblast řešení*stiskněte **ovládací prvek** a klikněte na název projektu. Přejděte do **nástroje** > **Upravit soubor**.
* Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

Povolení komentářů XML poskytuje ladicí informace pro nedokumentované veřejné typy a členy. Nedokumentované typy a členy jsou označeny varovnou zprávou. Například následující zpráva indikuje porušení kódu upozornění 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Chcete-li potlačit upozornění v rámci projektu, definujte seznam kódů upozornění oddělených středníkem, které mají být v souboru projektu ignorovány. Připojení kódů upozornění k `$(NoWarn);` aplikování [ C# výchozích hodnot](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

Chcete-li potlačit upozornění pouze pro konkrétní členy, uveďte kód v direktivách preprocesoru [upozornění #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) . Tento přístup je užitečný pro kód, který by neměl být zveřejněn prostřednictvím dokumentace rozhraní API. V následujícím příkladu je kód upozornění CS1591 ignorován pro celou `Program` třídu. Vynucování kódu upozornění je obnoveno při zavření definice třídy. Zadejte více kódů upozornění se seznamem odděleným čárkami.

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

Nakonfigurujte Swagger pro použití souboru XML, který je vygenerovaný předchozími pokyny. Pro operační systémy Linux a jiné než Windows můžou názvy souborů a cesty rozlišovat velká a malá písmena. Například soubor *TodoApi. XML* je platný ve Windows, ale ne CentOS.

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

V předchozím kódu se reflexe používá k sestavení názvu souboru XML, který odpovídá tomuto projektu webového rozhraní API. [](/dotnet/csharp/programming-guide/concepts/reflection) Vlastnost [AppContext. BaseDirectory](/dotnet/api/system.appcontext.basedirectory) se používá k vytvoření cesty k souboru XML. Některé funkce Swagger (například schémat vstupních parametrů nebo metody HTTP a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML. Pro většinu funkcí, konkrétně souhrny metod a popisy parametrů a kódů odpovědí, je použití souboru XML povinné.

Přidání komentářů se třemi lomítky k akci vylepšuje uživatelské rozhraní Swagger přidáním popisu do záhlaví oddílu. Přidejte prvek `Delete` souhrnu > nad akci: [ \<](/dotnet/csharp/programming-guide/xmldoc/summary)

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
Uživatelské rozhraní Swagger zobrazí vnitřní text `<summary>` elementu předchozí kód:

![Uživatelské rozhraní Swagger, které zobrazuje komentář XML, odstraní konkrétní TodoItem. pro metodu DELETE](sample_images/triple-slash-comments.png)

Uživatelské rozhraní je založené na generovaném schématu JSON:

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
Do dokumentace k metodě Akcepřidejte>elementpoznámky.`Create` [ \<](/dotnet/csharp/programming-guide/xmldoc/remarks) Doplňuje informace zadané v `<summary>` prvku a poskytuje robustnější uživatelské rozhraní Swagger. Obsah `<remarks>` elementu se může skládat z textu, JSON nebo XML.

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
Všimněte si vylepšení uživatelského rozhraní pomocí těchto dalších komentářů:

![Uživatelské rozhraní Swagger s dalšími zobrazenými komentáři](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Datové poznámky

Naupravujte model pomocí atributů, které se nacházejí v oboru názvů [System. ComponentModel.](/dotnet/api/system.componentmodel.dataannotations) DataAnnotations, aby bylo možné lépe řídit součásti uživatelského rozhraní Swagger.

`[Required]` Přidejte atribut`Name` do vlastnosti`TodoItem` třídy:

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

Přítomnost tohoto atributu mění chování uživatelského rozhraní a mění základní schéma JSON:

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

`[Produces("application/json")]` Přidejte atribut do kontroleru rozhraní API. Jeho účelem je deklarovat, že akce kontroleru podporují typ obsahu odpovědi *Application/JSON*:

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
Rozevírací seznam **typ obsahu odpovědi** pro akce GET kontroleru vybere tento typ obsahu jako výchozí:

![Uživatelské rozhraní Swagger s výchozím typem obsahu odpovědi](sample_images/json-response-content-type.png)

Díky nárůstu využití datových poznámek ve webovém rozhraní API se stránky s nápovědu pro uživatelské rozhraní a rozhraní API stanou výstižnější a užitečnější.

### <a name="describe-response-types"></a>Popis typů odpovědí

Vývojáři, kteří využívají webové rozhraní API, mají největší obavy s&mdash;tím, co vrací konkrétně typy odpovědí a kódy chyb (Pokud ne Standard). Typy odpovědí a kódy chyb jsou označeny v komentářích XML a datových anotacích.

`Create` Akce vrátí stavový kód HTTP 201 při úspěchu. Stavový kód HTTP 400 se vrátí, když text odeslaného požadavku má hodnotu null. Bez správné dokumentace v uživatelském rozhraní Swagger nemá spotřebitel znalosti o těchto očekávaných výsledcích. Opravte tento problém tak, že přidáte zvýrazněné řádky v následujícím příkladu:

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(201)]
[ProducesResponseType(400)]
public ActionResult<TodoItem> Create(TodoItem item)
```

Uživatelské rozhraní Swagger teď jasně klade očekávané kódy odpovědí HTTP:

![Uživatelské rozhraní Swagger, které zobrazuje popis třídy odpovědí POST, vrátí nově vytvořenou položku TODO ' a ' 400 – Pokud má položka hodnotu null pro stavový kód a důvod v rámci zpráv odpovědí.](sample_images/data-annotations-response-types.png)

V ASP.NET Core 2,2 nebo novějších lze konvence použít jako alternativu k explicitnímu Upraveníí jednotlivých `[ProducesResponseType]`akcí. Další informace najdete v tématu [použití konvencí webového rozhraní API](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).

Informace o přizpůsobení uživatelského rozhraní najdete v těchto tématech: [Přizpůsobení uživatelského rozhraní](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
