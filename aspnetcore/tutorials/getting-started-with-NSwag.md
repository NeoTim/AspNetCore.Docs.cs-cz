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
# <a name="get-started-with-nswag-and-aspnet-core"></a>Začínáme se službou NSwag a ASP.NET Core

Podle [Christoph Nienaber](https://twitter.com/zuckerthoben) a [Rico Suter](https://rsuter.com)

::: moniker range=">= aspnetcore-2.1"

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))

::: moniker-end

Zaregistrujte middlewares službou NSwag na:

* Generovat specifikaci Swaggeru pro rozhraní API implementované webu.
* Poskytování uživatelského rozhraní Swagger pro procházení a testování webové rozhraní API.

Použít [službou NSwag](https://github.com/RSuter/NSwag) middlewares ASP.NET Core, nainstalujte [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) balíček NuGet. Tento balíček obsahuje middlewares generovat a slouží specifikace Swaggeru, uživatelské rozhraní Swagger (v2 a v3), a [uživatelského rozhraní ReDoc](https://github.com/Rebilly/ReDoc).

Kromě toho má důrazně doporučujeme používat vaší službou NSwag možnosti generování kódu. Vyberte jednu z následujících možností využít možnosti generování kódu:

* Použití [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio), desktopové aplikace Windows pro generování klientského kódu v C# a TypeScript pro vaše rozhraní API.
* Použití [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) balíčky NuGet, se generování uvnitř projektu kódu.
* Použití službou NSwag z [příkazového řádku](https://github.com/NSwag/NSwag/wiki/CommandLine).
* Použití [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) balíček NuGet.

## <a name="features"></a>Funkce

Hlavním důvodem pro použití službou NSwag je schopnost pouze uživatelské rozhraní Swagger a generátoru Swagger, ale také vytvářecí využívají možnosti generování flexibilní kódu. Není nutné existujícího rozhraní API&mdash;můžete použít rozhraní API třetích stran, která začlenit Swagger a nechat službou NSwag generovat implementace klienta. V obou případech urychlené vývojový cyklus a snadno přizpůsobit změn rozhraní API.

## <a name="package-installation"></a>Instalace balíčku

Balíček NuGet službou NSwag lze přidat pomocí následujících postupů:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Konzola správce balíčků** okno:
  * Přejděte na **zobrazení** > **jiných Windows** > **Konzola správce balíčků**
  * Přejděte do adresáře, ve kterém *TodoApi.csproj* soubor existuje
  * Spusťte následující příkaz:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* Z **spravovat balíčky NuGet** dialogové okno:
  * Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**
  * Nastavte **zdroj balíčku** do "nuget.org"
  * Do vyhledávacího pole zadejte "NSwag.AspNetCore"
  * Vyberte balíček "NSwag.AspNetCore" z **Procházet** kartě a klikněte na tlačítko **instalace**

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**
* Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"
* Do vyhledávacího pole zadejte "NSwag.AspNetCore"
* Vyberte v podokně výsledků "NSwag.AspNetCore" balíček a klikněte na tlačítko **přidat balíček**

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštěním následujícího příkazu z **integrovaný terminál**:

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Přidejte a nakonfigurujte Swagger middleware

Importujte následující obory názvů v `Startup` třídy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

V `Startup.ConfigureServices` metoda, registraci k požadovaným službám Swaggeru: 

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

V `Startup.Configure` metoda, povolí middleware pro poskytování generované specifikace Swagger a uživatelské rozhraní Swagger v3:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-10)]

Spusťte aplikaci. Přejděte na `http://localhost:<port>/swagger` Chcete-li zobrazit uživatelské rozhraní Swagger. Přejděte do `http://localhost:<port>/swagger/v1/swagger.json` zobrazíte specifikace Swagger.

## <a name="code-generation"></a>Generování kódu

### <a name="via-nswagstudio"></a>Via NSwagStudio

* Nainstalujte NSwagStudio z oficiální [úložiště GitHub](https://github.com/RSuter/NSwag/wiki/NSwagStudio).
* Spusťte NSwagStudio. Zadejte *swagger.json* adresy URL v souboru **adresa URL specifikace Swaggeru** textového pole a klikněte na tlačítko **vytvořit místní kopii** tlačítko.
* Vyberte **CSharp klienta** typ výstupu klienta. Další možnosti zahrnují **TypeScript klienta** a **Kontroleru webového rozhraní API CSharp**. Použití Kontroleru webového rozhraní API je v podstatě obrácenou generace. Specifikace služby používá k opětovnému sestavení služby.
* Klikněte na tlačítko **generovat výstupy** tlačítko. Na dokončení C# klienta implementace *TodoApi.NSwag* projekt je vytvořen. Klikněte na tlačítko **CSharp klienta** karty **výstupy** část kódu generovaného klienta:

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
> Generování kódu klienta jazyka C# na základě definované v nastavení **nastavení** kartě **CSharp klienta** kartu. Změňte nastavení a provádět úlohy, například přejmenování výchozí obor názvů a generování synchronní metody.

* Zkopírujte vygenerovaný kód jazyka C# do souboru v projektu klienta (například [Xamarin.Forms](/xamarin/xamarin-forms/) aplikace).
* Spusťte využívající webové rozhraní API:

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
> Základní adresu URL a/nebo klienta HTTP můžete vložit do rozhraní API klienta. Osvědčeným postupem je vždy [opakovaně používat HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/).

### <a name="other-ways-to-generate-client-code"></a>Další možnosti, jak generovat kód klienta

Můžete vygenerovat kód klienta jinými způsoby, další vhodné do svého pracovního postupu:

* [MSBuild](https://www.nuget.org/packages/NSwag.MSBuild/)

* [V kódu](https://github.com/NSwag/NSwag/wiki/SwaggerToCSharpClientGenerator)

* [Šablony T4](https://github.com/NSwag/NSwag/wiki/T4)

## <a name="customize"></a>Přizpůsobit

Swagger poskytuje možnosti pro dokumentace objektového modelu pro usnadnění spotřeby webového rozhraní API.

### <a name="api-info-and-description"></a>Informace o rozhraní API a popis

V `Startup.Configure` metody akce konfigurace předán `UseSwagger` přidá informace, jako je vytváření, licence a popis metody:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_UseSwagger)]

Uživatelské rozhraní Swagger zobrazí informace na verzi:

![Uživatelské rozhraní swagger s informací o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>XML – komentáře

Komentáře XML jsou povolené pomocí následujících postupů:

# <a name="visual-studiotabvisual-studio-xml"></a>[Visual Studio](#tab/visual-studio-xml/)

::: moniker range=">= aspnetcore-2.0"

* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **upravit < project_name > .csproj**.
* Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**
* Zkontrolujte **soubor dokumentace XML** pole v rámci **výstup** část **sestavení** kartu

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac-xml"></a>[Visual Studio for Mac](#tab/visual-studio-mac-xml/)

::: moniker range=">= aspnetcore-2.0"

* Z *oblasti řešení*, stiskněte klávesu **ovládací prvek** a klikněte na název projektu. Přejděte do **nástroje** > **upravit soubor**.
* Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otevřít **možnosti projektu** dialogového okna > **sestavení** > **kompilátoru**
* Zkontrolujte **generovat dokumentaci xml** pole v rámci **Obecné možnosti** oddílu

::: moniker-end

# <a name="visual-studio-codetabvisual-studio-code-xml"></a>[Visual Studio Code](#tab/visual-studio-code-xml/)

Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Datové poznámky

::: moniker range="<= aspnetcore-2.0"

Používá se službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult). V důsledku toho službou NSwag nelze odvodit, co dělá akce a návratovou hodnotu. Vezměte v úvahu v následujícím příkladu:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Vrátí předchozí akce `IActionResult`, ale uvnitř akce vrací buď [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) nebo [chybného požadavku](/dotnet/api/system.web.http.apicontroller.badrequest). Datové poznámky se používají zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP. Uspořádání akce s následujícími atributy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Používá se službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [ActionResult\<T >](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1). V důsledku toho může službou NSwag pouze odvodit návratový typ určené `T`. Nejde odvodit další možné návratové typy v akci. Vezměte v úvahu v následujícím příkladu:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Vrátí předchozí akce `ActionResult<T>`. V akci, vrací [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Protože kontroler je doplněn [[objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut, [chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*) odpovědi je také možné,. Další informace najdete v tématu [odpovědi HTTP 400 automatické](xref:web-api/index#automatic-http-400-responses). Datové poznámky se používají zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP. Uspořádání akce s následujícími atributy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

V ASP.NET Core 2.2 nebo vyšší, vytváření názvů může sloužit jako alternativu k upravení explicitně jednotlivé akce s `[ProducesResponseType]`. Další informace naleznete v tématu <xref:web-api/advanced/conventions>.

::: moniker-end

Generátoru Swagger můžete nyní přesně popište tuto akci a vygenerovaný klienti věděli, co se zobrazí při volání koncového bodu. Upravení všechny akce s těmito atributy důrazně doporučujeme. Pokyny pro jaké odpovědi protokolu HTTP, by měla vrátit akce rozhraní API najdete v článku [specifikaci RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
