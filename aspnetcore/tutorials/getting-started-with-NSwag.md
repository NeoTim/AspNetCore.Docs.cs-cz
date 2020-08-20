---
title: Začínáme s NSwag a ASP.NET Core
author: zuckerthoben
description: Naučte se, jak pomocí NSwag vygenerovat dokumentaci a stránky s nápovědu pro ASP.NET Core webové rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 6dc1fe85298cae307f554cfc06c4129fabbe8ab4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633588"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Začínáme s NSwag a ASP.NET Core

Od [Christoph Nienaber](https://twitter.com/zuckerthoben), [Portoriko Suter](https://rsuter.com)a [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([Jak stáhnout](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([Jak stáhnout](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag nabízí následující možnosti:

* Schopnost využít uživatelské rozhraní Swagger a generátor Swagger.
* Flexibilní možnosti generování kódu.

Pomocí NSwag nepotřebujete existující rozhraní API, &mdash; můžete použít rozhraní API třetích stran, která zahrnují Swagger a generují implementaci klienta. NSwag vám umožňuje urychlit vývojový cyklus a snadno se přizpůsobit změnám rozhraní API.

## <a name="register-the-nswag-middleware"></a>Registrace middlewaru NSwag

Zaregistrujte middleware NSwag do:

* Vygenerujte specifikaci Swagger pro implementované webové rozhraní API.
* Slouží jako uživatelské rozhraní Swagger k procházení a testování webového rozhraní API.

Pokud chcete použít middleware ASP.NET Core [NSwag](https://github.com/RicoSuter/NSwag) , nainstalujte balíček NuGet [NSwag. AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) . Tento balíček obsahuje middleware k vygenerování a obsluze specifikace Swagger, uživatelského rozhraní Swagger (v2 a V3) a [uživatelského rozhraní ReDoc](https://github.com/Rebilly/ReDoc).

K instalaci balíčku NuGet NSwag použijte jeden z následujících přístupů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V okně **konzoly Správce balíčků** :
  * Přejít k **zobrazení**  >  **jiné**  >  **konzoly Správce balíčků** Windows
  * Přejděte do adresáře, ve kterém existuje soubor *TodoApi. csproj.*
  * Spusťte následující příkaz:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* V dialogovém okně **Spravovat balíčky NuGet** :
  * Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**  >  **Spravovat balíčky NuGet** .
  * Nastavte **zdroj balíčku** na "NuGet.org".
  * Do vyhledávacího pole zadejte "NSwag. AspNetCore".
  * Na kartě **Procházet** vyberte balíček NSwag. AspNetCore a klikněte na **nainstalovat** .

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *balíčky* v **oblast řešení**  >  **Přidat balíčky...**
* Nastavte rozevírací seznam pro **zdroj** okna **Přidat balíčky** na "NuGet.org".
* Do vyhledávacího pole zadejte "NSwag. AspNetCore".
* V podokně výsledků vyberte balíček NSwag. AspNetCore a klikněte na **Přidat balíček** .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Spusťte následující příkaz:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Přidat a nakonfigurovat middleware Swagger

Pomocí následujících kroků přidejte a nakonfigurujte Swagger v aplikaci ASP.NET Core:

* V `Startup.ConfigureServices` metodě zaregistrujte požadované služby Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* V `Startup.Configure` metodě povolte middleware pro poskytování vygenerované specifikace Swagger a uživatelského rozhraní Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Spustit aplikaci Přejít na:
  * `http://localhost:<port>/swagger` pro zobrazení uživatelského rozhraní Swagger.
  * `http://localhost:<port>/swagger/v1/swagger.json` pro zobrazení specifikace Swagger.

## <a name="code-generation"></a>Generování kódu

Můžete využít možnosti generování kódu NSwag výběrem jedné z následujících možností:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): desktopová aplikace pro Windows pro generování kódu klienta rozhraní API v jazyce C# nebo TypeScript.
* Balíčky NuGet [NSwag. strategii. CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag. strategii. TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) pro generování kódu v rámci projektu.
* NSwag z [příkazového řádku](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* Balíček NuGet [NSwag. MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild)
* [Připojená služba Unchase openapi (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): připojená služba sady Visual Studio pro generování kódu klienta rozhraní API v jazyce C# nebo TypeScript. Generuje také řadiče jazyka C# pro služby OpenAPI Services pomocí NSwag.

### <a name="generate-code-with-nswagstudio"></a>Generování kódu pomocí NSwagStudio

* Nainstalujte NSwagStudio podle pokynů v [úložišti GitHub NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). Na stránce verze NSwag si můžete stáhnout verzi příkazu xcopy, kterou lze spustit bez oprávnění k instalaci a správcům.
* Spusťte NSwagStudio a v textovém poli **Adresa URL specifikace Swagger** zadejte adresu url souboru *swagger.js* . Příklad: *http://localhost:44354/swagger/v1/swagger.json*.
* Kliknutím na tlačítko **vytvořit místní kopii** vygenerujete reprezentaci JSON vaší specifikace Swagger.

  ![Vytvořit místní kopii specifikace Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* V oblasti **výstupy** klikněte na zaškrtávací políčko **klient CSharp** . V závislosti na vašem projektu můžete také zvolit **klienta TypeScript** nebo **KONTROLER webového rozhraní API CSharp**. Vyberete-li možnost **kontroler webového rozhraní API CSharp**, specifikace služby znovu sestaví službu a slouží jako reverzní generace.
* Kliknutím na **generovat výstupy** vytvoříte kompletní implementaci klienta v jazyce C# projektu *TodoApi. NSwag* . Pokud chcete zobrazit generovaný kód klienta, klikněte na kartu **klient CSharp** :

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
> Kód klienta jazyka C# je vygenerován na základě výběrů na kartě **Nastavení** . Upravte nastavení tak, aby se prováděly úlohy, jako je například výchozí přejmenování oboru názvů a synchronní metoda generování.

* Zkopírujte generovaný kód C# do souboru v klientském projektu, který bude spotřebovávat rozhraní API.
* Začněte spotřebovávat webové rozhraní API:

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>Přizpůsobení dokumentace k rozhraní API

Swagger poskytuje možnosti pro dokumentaci modelu objektu, aby se usnadnila spotřeba webového rozhraní API.

### <a name="api-info-and-description"></a>Informace a popis rozhraní API

V `Startup.ConfigureServices` metodě může konfigurační akce předaná `AddSwaggerDocument` metodě přidat informace, jako je autor, licence a popis:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Uživatelské rozhraní Swagger zobrazuje informace o verzi:

![Uživatelské rozhraní Swagger s informacemi o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>Komentáře XML

Chcete-li povolit komentáře XML, proveďte následující kroky:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **upravit <PROJECT_NAME>. csproj**.
* Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **vlastnosti** .
* V části **výstup** na kartě **sestavení** zaškrtněte políčko **soubor dokumentace XML** .

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* V *oblast řešení*stiskněte **ovládací prvek** a klikněte na název projektu. Přejděte do **nástroje**  >  **Upravit soubor**.
* Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otevření dialogového okna **Možnosti projektu** > **Build** > **kompilátor** sestavení
* V části **Obecné možnosti** zaškrtněte políčko **generovat dokumentaci XML** .

::: moniker-end

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Datové poznámky

::: moniker range="<= aspnetcore-2.0"

Vzhledem k tomu, že NSwag používá [reflexi](/dotnet/csharp/programming-guide/concepts/reflection)a doporučený návratový typ pro akce webového rozhraní API je [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nemůže odvodit, jakou akci dělá a co vrátí.

Uvažujte následující příklad:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Předchozí akce vrátí `IActionResult` , ale v akci, která vrací hodnotu [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) nebo [důvodu chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*). Pomocí datových poznámek můžete klientům sdělit, které stavové kódy HTTP má tato akce vrátit. Označte akci následujícími atributy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Vzhledem k tomu, že NSwag používá [reflexi](/dotnet/csharp/programming-guide/concepts/reflection)a doporučený návratový typ pro akce webového rozhraní API je [ \<T> ActionResult](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), může odvodit jenom návratový typ definovaný pomocí `T` . Nemůžete automaticky odvodit jiné možné návratové typy.

Uvažujte následující příklad:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Předchozí akce vrátí `ActionResult<T>` . V rámci akce vrátí [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Vzhledem k tomu, že kontroler má [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut, může být také [důvodu chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*) odpověď. Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses). Pomocí datových poznámek můžete klientům sdělit, které stavové kódy HTTP má tato akce vrátit. Označte akci následujícími atributy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

V ASP.NET Core 2,2 nebo novějších můžete místo explicitně upravení jednotlivé akce pomocí použít konvence `[ProducesResponseType]` . Další informace naleznete v tématu <xref:web-api/advanced/conventions>.

::: moniker-end

Generátor Swagger teď může tuto akci přesně popsat a vygenerovaných klientů věděli, co získají při volání koncového bodu. Jako doporučení označte všechny akce pomocí těchto atributů.

Pokyny k vrácení odpovědí HTTP, které vaše akce rozhraní API musí vrátit, najdete v článku [specifikace RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
