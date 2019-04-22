---
title: Začínáme s Swashbuckle a ASP.NET Core
author: zuckerthoben
description: Zjistěte, jak přidat do projektu ASP.NET Core webové rozhraní API integrovat uživatelské rozhraní Swagger Swashbuckle.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/04/2019
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 31d45eaa684118ab78d1b3ecac594e95712f631f
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068346"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Začínáme s Swashbuckle a ASP.NET Core

Podle [Shayne Boyer](https://twitter.com/spboyer) a [Scott Addie](https://twitter.com/Scott_Addie)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([stažení](xref:index#how-to-download-a-sample))

Existují tři hlavní komponenty pro Swashbuckle:

* [Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): Swagger objektový model a middlewarem ke zveřejnění `SwaggerDocument` objekty jako koncové body JSON.

* [Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): Generátor Swagger, který vytváří `SwaggerDocument` objekty přímo z tras, kontrolerů a modely. Obvykle se zkombinuje s middlewarem koncového bodu Swaggeru k automaticky vystavení dat JSON pro Swagger.

* [Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): embedded verze nástroje pro uživatelské rozhraní Swagger. Interpretuje JSON pro Swagger k vytváření bohatých a přizpůsobitelných prostředí pro popis webových rozhraní API funkce. Zahrnuje integrované testovací postroje pro veřejné metody.

## <a name="package-installation"></a>Instalace balíčku

Swashbuckle lze přidat pomocí následujících postupů:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Konzola správce balíčků** okno:
  * Přejděte na **zobrazení** > **jiných Windows** > **Konzola správce balíčků**
  * Přejděte do adresáře, ve kterém *TodoApi.csproj* soubor existuje
  * Spusťte následující příkaz:

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* Z **spravovat balíčky NuGet** dialogové okno:
  * Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**
  * Nastavte **zdroj balíčku** do "nuget.org"
  * Do vyhledávacího pole zadejte "Swashbuckle.AspNetCore"
  * Vyberte balíček "Swashbuckle.AspNetCore" z **Procházet** kartě a klikněte na tlačítko **instalace**

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**
* Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"
* Do vyhledávacího pole zadejte "Swashbuckle.AspNetCore"
* Vyberte v podokně výsledků "Swashbuckle.AspNetCore" balíček a klikněte na tlačítko **přidat balíček**

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštěním následujícího příkazu z **integrovaný terminál**:

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Přidejte a nakonfigurujte Swagger middleware

Přidání generátoru Swagger do kolekce služby `Startup.ConfigureServices` metody:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

Importovat následující obor názvů pro použití `Info` třídy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

V `Startup.Configure` metoda, povolí middleware pro poskytování generované dokumentů JSON a uživatelské rozhraní Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

Předchozí `UseSwaggerUI` umožňuje volání metody [Middleware statické soubory](xref:fundamentals/static-files). Pokud je zaměřen na rozhraní .NET Framework nebo .NET Core 1.x, přidejte [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu balíček NuGet.

Spusťte aplikaci a přejděte do `http://localhost:<port>/swagger/v1/swagger.json`. Vygenerovaný dokument popisující koncových bodů se zobrazí, jak je znázorněno v [Swagger specifikace (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

Uživatelské rozhraní Swagger lze nalézt v `http://localhost:<port>/swagger`. Prozkoumejte rozhraní API přes uživatelské rozhraní Swagger a začlení jej v jiných aplikacích.

> [!TIP]
> K poskytování uživatelského rozhraní Swagger kořenové aplikace (`http://localhost:<port>/`), můžete nastavit `RoutePrefix` vlastnost na prázdný řetězec:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

Pokud do relativní cesty pomocí adresáře pomocí služby IIS nebo reverzního proxy serveru, nastavení koncového bodu Swaggeru `./` předponu. Například, `./swagger/v1/swagger.json`. Pomocí `/swagger/v1/swagger.json` Instruuje aplikaci upravovat pro soubor JSON true kořenové adresy URL (plus předponu trasy, pokud se používá). Například použít `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` místo `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Přizpůsobení a rozšíření

Swagger poskytuje možnosti pro dokumentace objektového modelu a přizpůsobení uživatelského rozhraní tak, aby odpovídala motivu.

### <a name="api-info-and-description"></a>Informace o rozhraní API a popis

Konfigurace akce předán `AddSwaggerGen` přidá informace, jako je vytváření, licence a popis metody:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Uživatelské rozhraní Swagger zobrazí informace na verzi:

![Uživatelské rozhraní swagger s informací o verzi: popis, vytvářet a další odkaz](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML – komentáře

Komentáře XML se dá nastavit pomocí následujících postupů:

#### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **upravit < project_name > .csproj**.
* Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**.
* Zkontrolujte **soubor dokumentace XML** pole v rámci **výstup** část **sestavení** kartu.

::: moniker-end

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* Z *oblasti řešení*, stiskněte klávesu **ovládací prvek** a klikněte na název projektu. Přejděte do **nástroje** > **upravit soubor**.
* Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otevřít **možnosti projektu** dialogového okna > **sestavení** > **kompilátoru**
* Zkontrolujte **generovat dokumentaci xml** pole v rámci **Obecné možnosti** oddílu

::: moniker-end

#### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Ručně přidejte zvýrazněné řádky a *.csproj* souboru:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

Povolení komentáře XML poskytuje informace o ladění pro nedokumentované veřejné typy a členy. Nezdokumentovaný typy a členy jsou označeny upozornění. Například následující zpráva označuje porušení kód upozornění. 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Chcete-li potlačit upozornění na úrovni projektu, definujte středníkem oddělený seznam kódů upozornění v souboru projektu. Přidávání kódů upozornění, aby `$(NoWarn);` se vztahuje [ C# výchozí hodnoty](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) příliš.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Potlačit upozornění jenom pro konkrétní členy, uzavřete kód v [varování #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) direktivy preprocesoru. Tento přístup je užitečný pro kód, který by neměly být vystaveny prostřednictvím dokumentace rozhraní API. V následujícím příkladu se ignoruje kód upozornění CS1591 pro celou `Program` třídy. Vynucení kód upozornění je obnovena na konci definice třídy. Zadejte více kódů upozornění s čárkami oddělený seznam.

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

Nakonfigurujte Swagger do souboru XML, který je generován pomocí podle předchozích pokynů. Pro operační systémy než Windows nebo Linuxem názvů a cest souborů lze malá a velká písmena. Například *TodoApi.XML* souboru je platný pro Windows, ale ne CentOS.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

V předchozím kódu [reflexe](/dotnet/csharp/programming-guide/concepts/reflection) sloužící k sestavení, který projektu webového rozhraní API odpovídající název souboru XML. [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) vlastnost se používá ke konstrukci cestu k souboru XML. Některé funkce Swaggeru (například schémat vstupní parametry nebo metod HTTP a kódy odpovědí z příslušných atributů) fungovat bez použití souborů dokumentace XML. Většina funkcí, a to metoda souhrny a popisy parametrů a kódy odpovědí, využívání souboru XML je povinný.

Přidávání komentářů třemi lomítky akci rozšiřuje uživatelské rozhraní Swagger tak, že přidáte popis hlavičku oddílu. Přidat [ \<summary >](/dotnet/csharp/programming-guide/xmldoc/summary) element výše `Delete` akce:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Vnitřní text předchozí kód zobrazí uživatelské rozhraní Swagger `<summary>` element:

![Zobrazuje komentář XML odstraní konkrétní TodoItem. uživatelské rozhraní swagger pro metodu DELETE](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

Generované schéma JSON vychází uživatelského rozhraní:

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

Přidat [ \<Poznámky >](/dotnet/csharp/programming-guide/xmldoc/remarks) elementu `Create` dokumentaci metody akce. Doplňující informace uvedené v `<summary>` elementu a poskytuje výkonnější uživatelské rozhraní Swagger. `<remarks>` Obsah elementu se může skládat z textu, JSON nebo XML.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Všimněte si, že se tyto další komentáře vylepšení uživatelského rozhraní:

![Uživatelské rozhraní swagger se zobrazí další komentáře](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Datové poznámky

Uspořádání s atributy, které jsou součástí modelu [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) oboru názvů, pomůže zajistit součásti uživatelského rozhraní Swagger.

Přidat `[Required]` atribut `Name` vlastnost `TodoItem` třídy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

Přítomnost tento atribut se změní chování uživatelského rozhraní a změní podkladové schéma JSON:

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

Přidat `[Produces("application/json")]` atribut kontroleru rozhraní API. Jeho účelem je deklarovat, že akce kontroleru, podporují typ obsahu odpovědi z *application/json*:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

**Typ obsahu odpovědi** rozevíracího seznamu vybere tento typ obsahu jako výchozí pro akce GET kontroleru:

![Uživatelské rozhraní swagger s výchozím typem obsahu odpovědi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

Popisný a užitečné stránky nápovědy, jak se zvyšuje využití anotacemi dat ve webovém rozhraní API, uživatelské rozhraní a rozhraní API.

### <a name="describe-response-types"></a>Popis typů odpovědi

Vývojáři využívající webové rozhraní API jsou nejvíce zajímají co bude vráceno&mdash;konkrétně typů odpovědi a chybové kódy (Pokud není standard). V poznámkách komentáře a data XML jsou rozlišeny typů odpovědi a kódy chyb.

`Create` Akce v případě úspěchu vrátí stavový kód HTTP 201. Stavový kód HTTP 400 je vrácena, když textu odeslaného požadavku má hodnotu null. Bez správnou dokumentaci v Uživatelském rozhraní Swagger nemá příjemce znalost těchto očekávaných výsledků. Tento problém vyřešit přidáním zvýrazněné řádky v následujícím příkladu:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Uživatelské rozhraní Swagger teď jasně dokumenty očekávané kódy odpovědí protokolu HTTP:

![Swagger UI zobrazení popisu třídy odpověď POST 'Vrátí nově vytvořenou položku seznamu úkolů' a '400 - Pokud položka má hodnotu null' stavový kód a důvod v odpovědích](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

V ASP.NET Core 2.2 nebo vyšší, vytváření názvů může sloužit jako alternativu k upravení explicitně jednotlivé akce s `[ProducesResponseType]`. Další informace naleznete v tématu <xref:web-api/advanced/conventions>.

::: moniker-end

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Stock uživatelského rozhraní je funkční a přístupné. Stránky dokumentace rozhraní API by měla představovat však vaší značce nebo motivu. Branding komponenty Swashbuckle vyžaduje přidání těchto materiálů k doručování statických souborů a sestavování strukturu složek pro hostování těchto souborů.

Pokud je zaměřen na rozhraní .NET Framework nebo .NET Core 1.x, přidejte [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) do projektu balíček NuGet:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Pokud cílí na .NET Core je již nainstalována předchozí balíček NuGet 2.x a použití [Microsoft.aspnetcore.all](xref:fundamentals/metapackage).

Povolte Middleware se statickými soubory:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

Získat obsah *dist* složky [úložiště GitHub uživatelského rozhraní Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist). Tato složka obsahuje prostředky potřebné pro stránka uživatelského rozhraní Swagger.

Vytvoření *wwwroot/swagger/uživatelského rozhraní* složky a zkopírujte do něj obsah *dist* složky.

Vytvoření *custom.css* souboru *wwwroot/swagger/uživatelského rozhraní*, pomocí následující šablony stylů CSS pro přizpůsobení záhlaví stránky:

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

Referenční dokumentace *custom.css* v *index.html* souboru po další soubory šablon stylů CSS:

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

Přejděte *index.html* na stránce `http://localhost:<port>/swagger/ui/index.html`. Zadejte `http://localhost:<port>/swagger/v1/swagger.json` hlavičky textového pole a klikněte na **prozkoumat** tlačítko. Výsledný stránka vypadá takto:

![Uživatelské rozhraní swagger s názvem vlastní hlavičky](web-api-help-pages-using-swagger/_static/custom-header.png)

Je mnohem více že můžete provést na stránce. Zobrazit všechny funkce pro prostředky uživatelského rozhraní na [úložiště GitHub uživatelského rozhraní Swagger](https://github.com/swagger-api/swagger-ui).
