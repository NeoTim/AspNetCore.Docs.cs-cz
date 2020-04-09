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
# <a name="get-started-with-nswag-and-aspnet-core"></a>Začínáme s NSwag a ASP.NET Core

[Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), a [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) [(jak stáhnout)](xref:index#how-to-download-a-sample)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) [(jak stáhnout)](xref:index#how-to-download-a-sample)

::: moniker-end

NSwag nabízí následující funkce:

* Schopnost využít Swagger UI a Swagger generátor.
* Flexibilní možnosti generování kódu.

S NSwag, nepotřebujete existující rozhraní&mdash;API můžete použít rozhraní API třetích stran, které obsahují Swagger a generovat implementaci klienta. NSwag umožňuje urychlit vývojový cyklus a snadno se přizpůsobit změnám rozhraní API.

## <a name="register-the-nswag-middleware"></a>Zaregistrujte middleware NSwag

Zaregistrujte middleware NSwag na:

* Vygenerujte specifikaci Swagger pro implementované webové rozhraní API.
* Slouží Swagger uživatelskérozhraní procházet a testovat webové rozhraní API.

Chcete-li použít [middleware NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core, nainstalujte balíček [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet. Tento balíček obsahuje middleware pro generování a poskytování specifikace Swagger, Swagger UI (v2 a v3) a [ReDoc UI](https://github.com/Rebilly/ReDoc).

K instalaci balíčku NSwag NuGet použijte jeden z následujících přístupů:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z okna **Konzoly správce balíčků:**
  * Přejít na **zobrazit** > další**konzolu Správce balíčků systému** **Windows** > 
  * Přejděte do adresáře, ve kterém existuje soubor *TodoApi.csproj.*
  * Spusťte následující příkaz:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* V dialogovém okně **Spravovat balíčky NuGet:**
  * Klikněte pravým tlačítkem myši na projekt v **Průzkumníku** > řešení**Spravovat balíčky NuGet**
  * Nastavit **zdroj balíčku** na "nuget.org"
  * Do vyhledávacího pole zadejte "NSwag.AspNetCore".
  * Na kartě **Procházet** vyberte balíček NSwag.AspNetCore a klikněte na **Instalovat.**

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši na složku *Balíčky* v **panelu řešení** > **Přidat balíčky...**
* Nastavení rozbalovacího okna **Zdroj** v okně **Přidat balíčky** na hodnotu "nuget.org"
* Do vyhledávacího pole zadejte "NSwag.AspNetCore".
* V podokně výsledků vyberte balíček NSwag.AspNetCore a klikněte na **Přidat balíček.**

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Přidání a konfigurace middlewaru Swagger

Přidejte a nakonfigurujte Swagger v aplikaci ASP.NET Core provedením následujících kroků:

* V `Startup.ConfigureServices` metodě zaregistrujte požadované služby Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* V `Startup.Configure` metodě povolte middleware pro obsluhu generované specifikace Swagger a ui Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Spustit aplikaci Přejděte na:
  * `http://localhost:<port>/swagger`zobrazíte ui swagger.
  * `http://localhost:<port>/swagger/v1/swagger.json`zobrazíte specifikaci Swagger.

## <a name="code-generation"></a>Generování kódu

Můžete využít funkce generování kódu NSwag výběrem jedné z následujících možností:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; desktopová aplikace pro Windows pro generování kódu klienta ROZHRANÍ API v C# nebo TypeScript.
* [Balíčky NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet pro generování kódu uvnitř projektu.
* NSwag z [příkazového řádku](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* Balíček [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet.
* [Unchase OpenAPI (Swagger) Připojená služba](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; Visual Studio připojená služba pro generování kódu klienta rozhraní API v jazyce C# nebo TypeScript. Také generuje řadiče C# pro služby OpenAPI s NSwag.

### <a name="generate-code-with-nswagstudio"></a>Generovat kód s NSwagStudio

* Nainstalujte NSwagStudio podle pokynů v [úložišti NSwagStudio GitHub](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). Na stránce vydání NSwag si můžete stáhnout xcopy verzi, kterou lze spustit bez oprávnění k instalaci a administrátoru.
* Spusťte NSwagStudio a zadejte adresu URL souboru *swagger.json* do textového pole **URL specifikace Swagger.** Například *http://localhost:44354/swagger/v1/swagger.json*.
* Kliknutím na tlačítko **Vytvořit místní kopii** vygenerujete reprezentaci JSON specifikace Swagger.

  ![Vytvořit místní kopii specifikace Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* V oblasti **Výstupy** klikněte na zaškrtávací políčko **CSharp Client** . V závislosti na projektu můžete také zvolit **TypeScript Client** nebo **CSharp Web API Controller**. Pokud vyberete **CSharp Web API Controller**, specifikace služby znovu obnoví službu, která slouží jako reverzní generace.
* Kliknutím na **generovat výstupy** vytvoříte úplnou implementaci klienta Jazyka C# projektu *TodoApi.NSwag.* Pokud chcete zobrazit generovaný kód klienta, klikněte na kartu **CSharp Client:**

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
> Klientský kód jazyka C# je generován na základě výběrů na kartě **Nastavení.**

* Zkopírujte vygenerovaný kód Jazyka C# do souboru v klientském projektu, který bude využívat rozhraní API.
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

Swagger poskytuje možnosti pro dokumentaci objektového modelu pro usnadnění spotřeby webového rozhraní API.

### <a name="api-info-and-description"></a>Informace a popis rozhraní API

V `Startup.ConfigureServices` metodě akce konfigurace předané metodě `AddSwaggerDocument` přidá informace, jako je autor, licence a popis:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Swagger UI zobrazuje informace o verzi:

![Swagger UI s informacemi o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>XML – komentáře

Chcete-li povolit komentáře XML, proveďte následující kroky:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **upravit <project_name>.csproj**.
* Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **Vlastnosti.**
* Zaškrtněte **políčko Soubor dokumentace XML** v části **Výstup** na kartě **Sestavení.**

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* V *panelu řešení*stiskněte **ovládací prvek** a klikněte na název projektu. Přejděte na **Nástroje** > **upravit soubor**.
* Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otevření dialogového **okna Možnosti projektu** > **kompilátor** **sestavení** >
* Zaškrtněte políčko **Generovat dokumentaci XML** v části **Obecné možnosti.**

::: moniker-end

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Datové poznámky

::: moniker range="<= aspnetcore-2.0"

Vzhledem k tomu, že NSwag používá [reflexe](/dotnet/csharp/programming-guide/concepts/reflection)a doporučený návratový typ pro akce webového rozhraní API je [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nelze odvodit, co vaše akce dělá a co vrátí.

Uvažujte následující příklad:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Předchozí akce vrátí `IActionResult`, ale uvnitř akce je vrácení buď [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) nebo [BadRequest](xref:System.Web.Http.ApiController.BadRequest*). Pomocí datových anotací sdělte klientům, které kódy stavu HTTP je známo, že tato akce vrací. Označte akci následujícími atributy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Vzhledem k tomu, že NSwag používá [reflection](/dotnet/csharp/programming-guide/concepts/reflection)a doporučený návratový typ pro akce webového rozhraní API je [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), může pouze odvodit návratový typ definovaný . `T` Nelze automaticky odvodit jiné možné typy vrácení.

Uvažujte následující příklad:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Předchozí akce vrátí `ActionResult<T>`. Uvnitř akce se vrací [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Vzhledem k [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) tomu, že řadič má atribut, [badrequest](xref:System.Web.Http.ApiController.BadRequest*) odpověď je možné také. Další informace naleznete [v tématu Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses). Pomocí datových anotací sdělte klientům, které kódy stavu HTTP je známo, že tato akce vrací. Označte akci následujícími atributy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

V ASP.NET jádrem 2.2 nebo novějším můžete použít konvence `[ProducesResponseType]`namísto explicitního zdobení jednotlivých akcí pomocí . Další informace naleznete v tématu <xref:web-api/advanced/conventions>.

::: moniker-end

Generátor Swagger nyní můžete přesně popsat tuto akci a generované klienti vědí, co obdrží při volání koncového bodu. Jako doporučení označte všechny akce těmito atributy.

Pokyny k tomu, jaké odpovědi HTTP by měly být vráceny vašim akcím rozhraní API, naleznete ve [specifikaci RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
