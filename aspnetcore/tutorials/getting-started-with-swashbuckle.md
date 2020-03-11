---
title: Začínáme s swashbuckle a ASP.NET Core
author: zuckerthoben
description: Naučte se, jak přidat swashbuckle do projektu webového rozhraní API ASP.NET Core pro integraci uživatelského rozhraní Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: da848ef9c5fa85f5186d1b6f0a6111d8c8d069c4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661299"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Začínáme s swashbuckle a ASP.NET Core

Od [Shayne Boyer](https://twitter.com/spboyer) a [Scott Addie](https://twitter.com/Scott_Addie)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Existují tři hlavní součásti, které je potřeba swashbuckle:

* [Swashbuckle. AspNetCore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): objektový model a middleware pro objekty Swagger k vystavení objektů `SwaggerDocument` jako koncových bodů JSON.

* [Swashbuckle. AspNetCore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generátor Swagger, který vytváří `SwaggerDocument` objekty přímo z vašich tras, řadičů a modelů. V kombinaci s middlewarem koncového bodu Swagger se obvykle automaticky zpřístupňuje JSON pro Swagger.

* [Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): vložená verze nástroje uživatelského rozhraní Swagger. Interpretuje formát JSON pro Swagger pro vytváření bohatě přizpůsobitelné prostředí pro popis funkce webového rozhraní API. Obsahuje integrované testovací vodičy pro veřejné metody.

## <a name="package-installation"></a>Instalace balíčku

Swashbuckle je možné přidat s následujícími přístupy:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V okně **konzoly Správce balíčků** :
  * Přejít na **zobrazení** > **Další Windows** > **konzole správce balíčků**
  * Přejděte do adresáře, ve kterém existuje soubor *TodoApi. csproj.*
  * Spusťte následující příkaz:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* V dialogovém okně **Spravovat balíčky NuGet** :
  * Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** > **Spravovat balíčky NuGet** .
  * Nastavte **zdroj balíčku** na "NuGet.org".
  * Ujistěte se, že je povolená možnost zahrnout předběžné verze.
  * Do vyhledávacího pole zadejte "swashbuckle. AspNetCore".
  * Na kartě **Procházet** vyberte nejnovější balíček "swashbuckle. AspNetCore" a klikněte na **instalovat** .

### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *balíčky* v **oblast řešení** > **Přidat balíčky...**
* Nastavte rozevírací seznam pro **zdroj** okna **Přidat balíčky** na "NuGet.org".
* Ujistěte se, že je povolená možnost Zobrazit balíčky předběžných verzí.
* Do vyhledávacího pole zadejte "swashbuckle. AspNetCore".
* V podokně výsledků vyberte nejnovější balíček "swashbuckle. AspNetCore" a klikněte na **Přidat balíček** .

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Z **integrovaného terminálu**spusťte následující příkaz:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a>Přidat a nakonfigurovat middleware Swagger

Ve třídě `Startup` importujte následující obor názvů pro použití `OpenApiInfo` třídy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Přidejte generátor Swagger do kolekce Services v metodě `Startup.ConfigureServices`:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

V metodě `Startup.Configure` povolte middleware pro obsluhu generovaného dokumentu JSON a uživatelského rozhraní Swagger:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

Předchozí volání metody `UseSwaggerUI` povoluje [middleware statických souborů](xref:fundamentals/static-files). Pokud cílíte .NET Framework nebo .NET Core 1. x, přidejte do projektu balíček NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) .

Spusťte aplikaci a přejděte na `http://localhost:<port>/swagger/v1/swagger.json`. Vygenerovaný dokument popisující koncové body se zobrazí, jak je znázorněno ve [specifikaci Swagger (Swagger. JSON)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

Uživatelské rozhraní Swagger se dá najít na `http://localhost:<port>/swagger`. Prozkoumejte rozhraní API prostřednictvím uživatelského rozhraní Swagger a zahrňte je do jiných programů.

> [!TIP]
> Pro obsluhu uživatelského rozhraní Swagger v kořenovém adresáři aplikace (`http://localhost:<port>/`) nastavte vlastnost `RoutePrefix` na prázdný řetězec:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

Pokud používáte adresáře se službou IIS nebo reverzním proxy serverem, nastavte koncový bod Swagger na relativní cestu pomocí předpony `./`. například `./swagger/v1/swagger.json`. Použití `/swagger/v1/swagger.json` instruuje aplikaci, aby hledala soubor JSON na skutečném kořenu adresy URL (plus předpona trasy, pokud se používá). Použijte například `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` místo `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Přizpůsobení a rozšiřování

Swagger poskytuje možnosti pro dokumentaci k objektovému modelu a přizpůsobení uživatelského rozhraní tak, aby odpovídalo vašemu motivu.

Do třídy `Startup` přidejte následující obory názvů:

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informace a popis rozhraní API

Akce konfigurace předaná metodě `AddSwaggerGen` přidává informace, jako je autor, licence a popis:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Uživatelské rozhraní Swagger zobrazuje informace o verzi:

![Uživatelské rozhraní Swagger s informacemi o verzi: Popis, autor a zobrazit další odkaz](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML – komentáře

Komentáře XML lze povolit pomocí následujících přístupů:

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **upravit < PROJECT_NAME >. csproj**.
* Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **vlastnosti**.
* V části **výstup** na kartě **sestavení** zaškrtněte políčko **soubor dokumentace XML** .

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* V *oblast řešení*stiskněte **ovládací prvek** a klikněte na název projektu. Přejděte na **nástroje** > **Upravit soubor**.
* Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otevřete dialogové okno **Možnosti projektu** > **kompilátor** **sestavení** >
* V části **Obecné možnosti** zaškrtněte políčko **generovat dokumentaci XML** .

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Ručně přidejte zvýrazněné řádky do souboru *. csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

Povolení komentářů XML poskytuje ladicí informace pro nedokumentované veřejné typy a členy. Nedokumentované typy a členy jsou označeny varovnou zprávou. Například následující zpráva indikuje porušení kódu upozornění 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Chcete-li potlačit upozornění v rámci projektu, definujte seznam kódů upozornění oddělených středníkem, které mají být v souboru projektu ignorovány. Připojení kódů upozornění k `$(NoWarn);` aplikuje i [ C# výchozí hodnoty](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Chcete-li potlačit upozornění pouze pro konkrétní členy, uveďte kód v direktivách preprocesoru [upozornění #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) . Tento přístup je užitečný pro kód, který by neměl být zveřejněn prostřednictvím dokumentace rozhraní API. V následujícím příkladu je kód upozornění CS1591 ignorován pro celou třídu `Program`. Vynucování kódu upozornění je obnoveno při zavření definice třídy. Zadejte více kódů upozornění se seznamem odděleným čárkami.

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

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

V předchozím kódu se [reflexe](/dotnet/csharp/programming-guide/concepts/reflection) používá k sestavení názvu souboru XML, který odpovídá tomuto projektu webového rozhraní API. Vlastnost [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) se používá k vytvoření cesty k souboru XML. Některé funkce Swagger (například schémat vstupních parametrů nebo metody HTTP a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML. Pro většinu funkcí, konkrétně souhrny metod a popisy parametrů a kódů odpovědí, je použití souboru XML povinné.

Přidání komentářů se třemi lomítky k akci vylepšuje uživatelské rozhraní Swagger přidáním popisu do záhlaví oddílu. Přidejte prvek [\<summary >](/dotnet/csharp/programming-guide/xmldoc/summary) nad `Delete` akci:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Uživatelské rozhraní Swagger zobrazí vnitřní text `<summary>`ho prvku předchozího kódu:

![Uživatelské rozhraní Swagger, které zobrazuje komentář XML, odstraní konkrétní TodoItem. pro metodu DELETE](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

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

Do dokumentace k metodě `Create` akcí přidejte [> elementu\<poznámky](/dotnet/csharp/programming-guide/xmldoc/remarks) . Doplňuje informace zadané v prvku `<summary>` a poskytuje robustnější uživatelské rozhraní Swagger. Obsah prvku `<remarks>` se může skládat z textu, JSON nebo XML.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Všimněte si vylepšení uživatelského rozhraní pomocí těchto dalších komentářů:

![Uživatelské rozhraní Swagger s dalšími zobrazenými komentáři](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Datové poznámky

Označte model s atributy, které se nacházejí v oboru názvů [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , aby bylo možné lépe řídit součásti uživatelského rozhraní Swagger.

Přidejte atribut `[Required]` do vlastnosti `Name` třídy `TodoItem`:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

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

Přidejte atribut `[Produces("application/json")]` k řadiči rozhraní API. Jeho účelem je deklarovat, že akce kontroleru podporují typ obsahu odpovědi *Application/JSON*:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

Rozevírací seznam **typ obsahu odpovědi** pro akce GET kontroleru vybere tento typ obsahu jako výchozí:

![Uživatelské rozhraní Swagger s výchozím typem obsahu odpovědi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

Díky nárůstu využití datových poznámek ve webovém rozhraní API se stránky s nápovědu pro uživatelské rozhraní a rozhraní API stanou výstižnější a užitečnější.

### <a name="describe-response-types"></a>Popis typů odpovědí

Vývojáři, kteří využívají webové rozhraní API, mají největší obavy s tím, co se vrátilo&mdash;specificky typy odpovědí a chybové kódy (Pokud není standard). Typy odpovědí a kódy chyb jsou označeny v komentářích XML a datových anotacích.

Akce `Create` vrátí stavový kód HTTP 201 při úspěchu. Stavový kód HTTP 400 se vrátí, když text odeslaného požadavku má hodnotu null. Bez správné dokumentace v uživatelském rozhraní Swagger nemá spotřebitel znalosti o těchto očekávaných výsledcích. Opravte tento problém tak, že přidáte zvýrazněné řádky v následujícím příkladu:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Uživatelské rozhraní Swagger teď jasně klade očekávané kódy odpovědí HTTP:

![Uživatelské rozhraní Swagger, které zobrazuje popis třídy odpovědí POST, vrátí nově vytvořenou položku TODO ' a ' 400 – Pokud má položka hodnotu null pro stavový kód a důvod v rámci zpráv odpovědí.](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

V ASP.NET Core 2,2 nebo novějších lze konvence použít jako alternativu k explicitnímu Upraveníí jednotlivých akcí s `[ProducesResponseType]`. Další informace naleznete v tématu <xref:web-api/advanced/conventions>.

::: moniker-end

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Uložené uživatelské rozhraní je funkční i předposílánelné. Stránky dokumentace k rozhraní API by však měly představovat vaši značku nebo motiv. Branding komponent swashbuckle vyžaduje přidání prostředků k obsluze statických souborů a vytvoření struktury složek pro hostování těchto souborů.

Pokud cílíte .NET Framework nebo .NET Core 1. x, přidejte do projektu balíček NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) :

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Předchozí balíček NuGet je již nainstalován, pokud cílíte na rozhraní .NET Core 2. x a použijete [Metapackage](xref:fundamentals/metapackage).

Povolit middleware statických souborů:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

Získejte obsah složky *DIST* z [úložiště GitHub uživatelského rozhraní Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist). Tato složka obsahuje potřebné prostředky pro stránku uživatelského rozhraní Swagger.

Vytvořte složku *wwwroot/Swagger/UI* a zkopírujte do ní obsah složky *DIST* .

Vytvořte *vlastní soubor. CSS* v *wwwroot/Swagger/uživatelském rozhraní*s následující šablonou stylů CSS pro přizpůsobení záhlaví stránky:

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

Odkaz na *vlastní šablony stylů. CSS* v souboru *index. html* ve složce uživatelského rozhraní za všechny ostatní soubory CSS:

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

Přejděte na stránku *index. html* na `http://localhost:<port>/swagger/ui/index.html`. Do textového pole záhlaví zadejte `https://localhost:<port>/swagger/v1/swagger.json` a klikněte na tlačítko **prozkoumat** . Výsledná stránka vypadá takto:

![Uživatelské rozhraní Swagger s vlastním nadpisem záhlaví](web-api-help-pages-using-swagger/_static/custom-header.png)

Stránka může dělat mnohem víc. Podívejte se na úplné možnosti prostředků uživatelského rozhraní v [úložišti GitHub uživatelského rozhraní Swagger](https://github.com/swagger-api/swagger-ui).
