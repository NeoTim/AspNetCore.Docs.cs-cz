---
title: Začínáme se službou NSwag a ASP.NET Core
author: zuckerthoben
description: Další informace o použití službou NSwag generovat dokumentaci a stránky pro webovému rozhraní API ASP.NET Core nápovědy.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/30/2018
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 566d402fbbb71f6b2cc46fc401cdf915a29cccda
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087437"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Začínáme se službou NSwag a ASP.NET Core

Podle [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), a [společnosti Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([stažení](xref:index#how-to-download-a-sample))

::: moniker-end

Službou NSwag nabízí následující funkce:

* Možnost využít uživatelské rozhraní Swagger a Swagger generátoru.
* Možnosti generování flexibilní kódu.

Se službou NSwag, není nutné existujícího rozhraní API&mdash;můžete použít rozhraní API třetích stran, která začlenit Swagger a generovat implementace klienta. Službou NSwag umožňuje urychlení cyklu vývoje a snadno reagovat na změny rozhraní API.

## <a name="register-the-nswag-middleware"></a>Zaregistrujte middleware službou NSwag

Zaregistrujte službou NSwag middlewaru, který má být:

* Generovat specifikaci Swaggeru pro rozhraní API implementované webu.
* Poskytování uživatelského rozhraní Swagger pro procházení a testování webové rozhraní API.

Použít [službou NSwag](https://github.com/RSuter/NSwag) middleware ASP.NET Core, nainstalujte [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) balíček NuGet. Tento balíček obsahuje middleware pro generování a obsluhovat specifikace Swaggeru, uživatelské rozhraní Swagger (v2 a v3), a [uživatelského rozhraní ReDoc](https://github.com/Rebilly/ReDoc).

Použijte jednu z následujících dvou přístupů k instalaci balíčku NuGet službou NSwag:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

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

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**
* Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"
* Do vyhledávacího pole zadejte "NSwag.AspNetCore"
* Vyberte v podokně výsledků "NSwag.AspNetCore" balíček a klikněte na tlačítko **přidat balíček**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštěním následujícího příkazu z **integrovaný terminál**:

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Přidejte a nakonfigurujte Swagger middleware

 Přidat a nakonfigurovat Swagger ve vaší aplikaci ASP.NET Core pomocí provádí následující kroky v `Startup` třídy:

* Importujte následující obory názvů:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

* V `ConfigureServices` metoda, registraci k požadovaným službám Swaggeru:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* V `Configure` metoda, povolí middleware pro poskytování generované specifikace Swagger a uživatelské rozhraní Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Spusťte aplikaci. Přejděte do:
  * `http://localhost:<port>/swagger` Chcete-li zobrazit uživatelské rozhraní Swagger.
  * `http://localhost:<port>/swagger/v1/swagger.json` Chcete-li zobrazit specifikace Swagger.

## <a name="code-generation"></a>Generování kódu

Můžete využít možnosti generování kódu vaší službou NSwag výběrem jedné z následujících možností:

* [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio) &ndash; desktopové aplikace Windows pro generování kódu klienta pro rozhraní API v C# nebo TypeScript.
* [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) nebo [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) balíčky NuGet pro generování kódu v projektu.
* Službou NSwag z [příkazového řádku](https://github.com/NSwag/NSwag/wiki/CommandLine).
* [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) balíček NuGet.

### <a name="generate-code-with-nswagstudio"></a>Generování kódu s NSwagStudio

* Nainstalujte NSwagStudio podle pokynů uvedených v [úložiště NSwagStudio GitHub](https://github.com/RSuter/NSwag/wiki/NSwagStudio).
* Spusťte NSwagStudio a zadejte *swagger.json* adresy URL v souboru **adresa URL specifikace Swaggeru** textového pole. Například *http://localhost:44354/swagger/v1/swagger.json*.
* Klikněte na tlačítko **vytvořit místní kopii** pro vygenerování JSON s reprezentací specifikace Swagger.

  ![Vytvořit místní kopii specifikace Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* V **výstupy** oblast, klikněte na tlačítko **CSharp klienta** zaškrtávací políčko. V závislosti na váš projekt, můžete také zvolit **TypeScript klienta** nebo **Kontroleru webového rozhraní API CSharp**. Pokud vyberete **Kontroleru webového rozhraní API CSharp**, specifikace služby znovu sestaví služby slouží jako reverzní generace.
* Klikněte na tlačítko **generovat výstupy** vytvoří kompletní C# implementace klienta *TodoApi.NSwag* projektu. Chcete-li zobrazit kód klienta vygenerovaný, klikněte na tlačítko **CSharp klienta** kartu:

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
> C# Generování kódu klienta na základě výběru v **nastavení** kartu. Změňte nastavení a provádět úlohy, například přejmenování výchozí obor názvů a generování synchronní metody.

* Zkopírujte vygenerovaný C# kód do souboru v projektu klienta, který bude využívat rozhraní API.
* Spusťte využívající webové rozhraní API:

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

Swagger poskytuje možnosti pro dokumentace objektového modelu pro usnadnění spotřeby webového rozhraní API.

### <a name="api-info-and-description"></a>Informace o rozhraní API a popis

V `Startup.ConfigureServices` metody akce konfigurace předán `AddSwaggerDocument` přidá informace, jako je vytváření, licence a popis metody:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Uživatelské rozhraní Swagger zobrazí informace na verzi:

![Uživatelské rozhraní swagger s informací o verzi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>XML – komentáře

Povolit komentáře XML, proveďte následující kroky:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **upravit < project_name > .csproj**.
* Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**
* Zkontrolujte **soubor dokumentace XML** pole v rámci **výstup** část **sestavení** kartu

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* Z *oblasti řešení*, stiskněte klávesu **ovládací prvek** a klikněte na název projektu. Přejděte do **nástroje** > **upravit soubor**.
* Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otevřít **možnosti projektu** dialogového okna > **sestavení** > **kompilátoru**
* Zkontrolujte **generovat dokumentaci xml** pole v rámci **Obecné možnosti** oddílu

::: moniker-end

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

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

Vzhledem k tomu používá službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nelze odvodit, co dělá akce a návratovou hodnotu.

Vezměte v úvahu v následujícím příkladu:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Vrátí předchozí akce `IActionResult`, ale uvnitř akce vrací buď [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) nebo [chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*). Použití anotací dat zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP. Uspořádání akce s následujícími atributy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

 Vzhledem k tomu používá službou NSwag [reflexe](/dotnet/csharp/programming-guide/concepts/reflection), a doporučené návratový typ pro akce webové rozhraní API je [ActionResult\<T >](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), ji pouze odvodit návratový typ určené `T`. Nelze automaticky odvodit další možné návratové typy. 

Vezměte v úvahu v následujícím příkladu:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Vrátí předchozí akce `ActionResult<T>`. V akci, vrací [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Protože kontroler je doplněn [[objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut, [chybného požadavku](xref:System.Web.Http.ApiController.BadRequest*) odpovědi je také možné,. Další informace najdete v tématu [odpovědi HTTP 400 automatické](xref:web-api/index#automatic-http-400-responses). Použití anotací dat zjistit klientům, kterých se ví, že tato akce vrátit stavové kódy HTTP. Uspořádání akce s následujícími atributy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

V ASP.NET Core 2.2 nebo vyšší, můžete místo explicitně upravení jednotlivé akce s použít konvence `[ProducesResponseType]`. Další informace naleznete v tématu <xref:web-api/advanced/conventions>.

::: moniker-end

Generátoru Swagger můžete nyní přesně popište tuto akci a vygenerovaný klienti věděli, co se zobrazí při volání koncového bodu. Jako doporučení uspořádání všechny akce s těmito atributy.

Pokyny pro jaké odpovědi protokolu HTTP, by měla vrátit akce rozhraní API najdete v článku [specifikaci RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
