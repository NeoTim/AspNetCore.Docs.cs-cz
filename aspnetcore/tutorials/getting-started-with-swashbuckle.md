---
title: Začínáme s Swashbuckle a ASP.NET Core
author: zuckerthoben
description: Přečtěte si, jak přidat Swashbuckle do projektu ASP.NET core web API pro integraci uživatelského rozhraní Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: da848ef9c5fa85f5186d1b6f0a6111d8c8d069c4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661299"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Začínáme s Swashbuckle a ASP.NET Core

Podle [Shayne Boyer](https://twitter.com/spboyer) a [Scott Addie](https://twitter.com/Scott_Addie)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Swashbuckle má tři hlavní komponenty:

* [Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): objektový model Swagger `SwaggerDocument` a middleware, které zveřejňují objekty jako koncové body JSON.

* [Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generátor Swagger, `SwaggerDocument` který vytváří objekty přímo z vašich tras, řadičů a modelů. Obvykle funguje tak, že v kombinaci s middlewarem koncových bodů Swaggeru automaticky zveřejňuje kód JSON pro Swagger.

* [Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): vložená verze nástroje Swagger UI. Interpretuje JSON pro Swagger v podobě propracovaného, přizpůsobitelného prostředí pro popis funkcí webového rozhraní API. Jeho součástí je i integrovaný správce testů pro veřejné metody.

## <a name="package-installation"></a>Instalace balíčku

Swashbuckle lze přidat s následujícími přístupy:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z okna **Konzoly správce balíčků:**
  * Přejít na **zobrazit** > další**konzolu Správce balíčků systému** **Windows** > 
  * Přejděte do adresáře, ve kterém existuje soubor *TodoApi.csproj.*
  * Spusťte následující příkaz:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* V dialogovém okně **Spravovat balíčky NuGet:**
  * Klikněte pravým tlačítkem myši na projekt v **Průzkumníku** > řešení**Spravovat balíčky NuGet**
  * Nastavit **zdroj balíčku** na "nuget.org"
  * Ujistěte se, že je povolena možnost Zahrnout předběžnou verzi.
  * Do vyhledávacího pole zadejte "Swashbuckle.AspNetCore".
  * Na kartě **Procházet** vyberte nejnovější balíček Swashbuckle.AspNetCore a klikněte na **Instalovat.**

### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši na složku *Balíčky* v **panelu řešení** > **Přidat balíčky...**
* Nastavení rozbalovacího okna **Zdroj** v okně **Přidat balíčky** na hodnotu "nuget.org"
* Ujistěte se, že je povolena možnost Zobrazit předběžné verze balíčků.
* Do vyhledávacího pole zadejte "Swashbuckle.AspNetCore".
* V podokně výsledků vyberte nejnovější balíček "Swashbuckle.AspNetCore" a klepněte na tlačítko **Přidat balíček.**

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte následující příkaz z **integrovaného terminálu**:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Spusťte následující příkaz:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a>Přidání a konfigurace middlewaru Swagger

Ve `Startup` třídě importujte následující obor názvů `OpenApiInfo` a použijte třídu:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Přidejte generátor Swagger do kolekce `Startup.ConfigureServices` služeb v metodě:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

V `Startup.Configure` metodě povolte middleware pro obsluhu generovaného dokumentu JSON a ui Swagger:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

Předchozí `UseSwaggerUI` volání metody umožňuje [middleware statického souboru](xref:fundamentals/static-files). Pokud cílíte na rozhraní .NET Framework nebo .NET Core 1.x, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet.

Spusťte aplikaci `http://localhost:<port>/swagger/v1/swagger.json`a přejděte na . Vygenerovaný dokument popisující koncové body se zobrazí tak, jak je znázorněno ve [specifikaci Swagger (swagger.json).](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)

Swagger UI lze nalézt `http://localhost:<port>/swagger`na adrese . Prozkoumejte rozhraní API prostřednictvím rozhraní Swagger A začleňte ho do jiných programů.

> [!TIP]
> Chcete-li sloužit Swagger UI v`http://localhost:<port>/`kořenovém `RoutePrefix` adresáři aplikace ( ), nastavte vlastnost na prázdný řetězec:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

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

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Swagger UI zobrazuje informace o verzi:

![Swagger UI s informacemi o verzi: popis, autor a další odkaz](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML – komentáře

Komentáře XML lze povolit pomocí následujících přístupů:

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **upravit <project_name>.csproj**.
* Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **příkaz Vlastnosti**.
* Zaškrtněte **políčko Soubor dokumentace XML** v části **Výstup** na kartě **Sestavení.**

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* V *panelu řešení*stiskněte **ovládací prvek** a klikněte na název projektu. Přejděte na **Nástroje** > **upravit soubor**.
* Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otevření dialogového **okna Možnosti projektu** > **kompilátor** **sestavení** >
* Zaškrtněte políčko **Generovat dokumentaci XML** v části **Obecné možnosti.**

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Ručně přidejte zvýrazněné řádky do souboru *.csproj:*

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

Povolení komentářů XML poskytuje ladicí informace pro nezdokumentované veřejné typy a členy. Nedokumentované typy a členy jsou označeny varovnou zprávou. Například následující zpráva označuje porušení kódu upozornění 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Chcete-li potlačit upozornění pro celý projekt, definujte seznam kódů upozornění oddělených středníkem, který chcete v souboru projektu ignorovat. Připojení kódy upozornění `$(NoWarn);` použije [c# výchozí hodnoty](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) příliš.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

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

V předchozím kódu [reflection](/dotnet/csharp/programming-guide/concepts/reflection) se používá k vytvoření názvu souboru XML odpovídající ho projektu webového rozhraní API. Vlastnost [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) se používá k vytvoření cesty k souboru XML. Některé funkce Swagger (například schémata vstupních parametrů nebo http metody a kódy odpovědí z příslušných atributů) fungují bez použití souboru dokumentace XML. U většiny funkcí, konkrétně souhrnů metod a popisů parametrů a kódů odpovědí, je použití souboru XML povinné.

Můžete k akci přidat také komentáře uvozené třemi lomítky, a vylepšit tak Swagger UI tím, že přidáte popis do hlavičky oddílu. Nad `Delete` akci přidejte [ \<souhrnný prvek>:](/dotnet/csharp/programming-guide/xmldoc/summary)

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Swagger ui zobrazí vnitřní text prvku předchozího `<summary>` kódu:

![Swagger UI zobrazující komentář XML 'Odstraní konkrétní TodoItem.' pro metodu DELETE](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

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

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Všimněte si vylepšení ui s těmito dalšími komentáři:

![Swagger UI s dalšíkomentáře zobrazeny](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Datové poznámky

Označte model atributy, které se nacházejí v oboru názvů [System.ComponentModel.DataAnnotations,](/dotnet/api/system.componentmodel.dataannotations) které pomáhají řídit součásti uživatelského uživatelského uživatelského nastavení Swagger.

Přidejte `[Required]` atribut `Name` do vlastnosti třídy: `TodoItem`

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

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

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

Rozevírací nabídky **Typ obsahu odpovědi** vybere tento typ obsahu jako výchozí pro akce GET řadiče:

![Swagger UI s výchozím typem obsahu odpovědi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

S tím, jak se zvyšuje využití datových anotací ve webovém rozhraní API, se stránky nápovědy uživatelského rozhraní a rozhraní API stávají popisnějšími a užitečnějšími.

### <a name="describe-response-types"></a>Popsat typy odpovědí

Vývojáři, kteří konzumují webové&mdash;rozhraní API, se nejvíce zajímají o to, co je vráceno konkrétně typy odpovědí a kódy chyb (pokud nejsou standardní). Typy odpovědí a kódy chyb jsou označeny v poznámkách XML a datových poznámkách.

Akce `Create` vrátí stavový kód HTTP 201 na úspěch. Stavový kód HTTP 400 je vrácen, pokud je zaúčtovaný text požadavku null. Bez řádné dokumentace v ui Swagger, spotřebitel postrádá znalosti o těchto očekávaných výsledků. Tento problém opravte přidáním zvýrazněných řádků v následujícím příkladu:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Swagger UI nyní jasně dokumentuje očekávané kódy odpovědi HTTP:

![Swagger UI zobrazující popis třídy odezvy POST "Vrátí nově vytvořenou položku todo" a "400 - Pokud je položka null" pro stavový kód a důvod v části Zprávy odpovědi](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

V ASP.NET Jádrem 2.2 nebo novějším lze konvence použít jako `[ProducesResponseType]`alternativu k explicitnímu zdobení jednotlivých akcí pomocí . Další informace naleznete v tématu <xref:web-api/advanced/conventions>.

::: moniker-end

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Základní ui je funkční a reprezentativní. Stránky dokumentace rozhraní API by však měly představovat vaši značku nebo motiv. Branding komponent Swashbuckle vyžaduje přidání prostředků pro poskytování statických souborů a vytváření struktury složek pro hostování těchto souborů.

Pokud cílíte na rozhraní .NET Framework nebo .NET Core 1.x, přidejte do projektu balíček [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Předchozí balíček NuGet je již nainstalován, pokud cílení .NET Core 2.x a pomocí [metabalíčku](xref:fundamentals/metapackage).

Povolit middleware statického souboru:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

Získejte obsah složky *dist* z [úložiště Swagger UI GitHub](https://github.com/swagger-api/swagger-ui/tree/master/dist). Tato složka obsahuje potřebné prostředky pro stránku swagger ui.

Vytvořte složku *wwwroot/swagger/ui* a zkopírujte do ní obsah složky *dist.*

Vytvořte soubor *custom.css* v *wwwroot/swagger/ui*s následujícím css pro přizpůsobení záhlaví stránky:

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

Odkaz *na custom.css* v souboru *index.html* uvnitř složky ui, po všech dalších souborech CSS:

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

Přejděte na stránku `http://localhost:<port>/swagger/ui/index.html` *index.html* na adrese . Zadejte `https://localhost:<port>/swagger/v1/swagger.json` do textového pole záhlaví a klikněte na tlačítko **Prozkoumat.** Výsledná stránka vypadá takto:

![Swagger UI s vlastním názvem záhlaví](web-api-help-pages-using-swagger/_static/custom-header.png)

Je tu mnohem víc, co můžete udělat s stránkou. Podívejte se na úplné možnosti pro prostředky ui v [úložišti Swagger UI GitHub](https://github.com/swagger-api/swagger-ui).
