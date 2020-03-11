---
title: Použití konvencí webového rozhraní API
author: pranavkm
description: Přečtěte si o konvencích webového rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: web-api/advanced/conventions
ms.openlocfilehash: d49b51d11d3f14d0c3edbe1765d74fd63e3ac061
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657750"
---
# <a name="use-web-api-conventions"></a>Použití konvencí webového rozhraní API

Od [Pranav Krishnamoorthy](https://github.com/pranavkm) a [Scott Addie](https://github.com/scottaddie)

ASP.NET Core 2,2 a novější zahrnuje způsob, jak extrahovat společnou [dokumentaci k rozhraní API](xref:tutorials/web-api-help-pages-using-swagger) a použít ji pro více akcí, řadičů nebo všech řadičů v rámci sestavení. Konvence webového rozhraní API jsou náhradou pro upravení jednotlivé akce pomocí [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).

Konvence vám umožní:

* Definujte nejběžnější návratové typy a stavové kódy vrácené z konkrétního typu akce.
* Identifikujte akce, které se odchylují od definovaného standardu.

ASP.NET Core MVC 2,2 a novější zahrnuje sadu výchozích konvencí v <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>. Konvence vycházejí z kontroleru (*ValuesController.cs*), který je k dispozici v šabloně projektu **rozhraní API** ASP.NET Core. Pokud se vaše akce řídí vzory v šabloně, měli byste být s použitím výchozích konvence úspěšné. Pokud výchozí konvence nevyhovují vašim potřebám, přečtěte si téma [Vytvoření konvencí webového rozhraní API](#create-web-api-conventions).

V době běhu <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> pochopení konvencí. `ApiExplorer` je abstrakce MVC ke komunikaci s [openapi](https://www.openapis.org/) (označovanými také jako Swagger) generátory dokumentů. Atributy z použité konvence jsou spojeny s akcí a jsou obsaženy v dokumentaci k OpenAPI akce. [Analyzátory rozhraní API](xref:web-api/advanced/analyzers) také pochopit konvence. Pokud je vaše akce nepostupná (například vrátí stavový kód, který není dokumentován v použité konvenci), zobrazí se upozornění, které vám umožní zdokumentovat stavový kód.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Použít konvence webového rozhraní API

Konvence netvoří; Každá akce může být přidružena k přesně jedné úmluvě. Konkrétnější konvence mají přednost před méně specifickými konvencemi. Výběr není deterministický, pokud se dvě nebo více konvencí se stejnou prioritou vztahují na akci. Následující možnosti existují, pokud chcete použít konvenci pro akci, od nejpřesnější po nejméně specifickou:

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; se vztahuje na jednotlivé akce a určuje typ konvence a metodu konvence, která se vztahuje.

    V následujícím příkladu je metoda `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` konvence typu výchozí konvence použita pro akci `Update`:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    Metoda `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` konvence používá pro akci následující atributy:

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    Další informace o `[ProducesDefaultResponseType]`najdete v tématu [výchozí odpověď](https://swagger.io/docs/specification/describing-responses/#default).

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použité pro kontroler &mdash; aplikuje zadaný typ konvence na všechny akce na kontroleru. Metoda konvence je označena nápovědami, které určují akce, na které se vztahuje metoda konvence. Další informace o pokynech najdete v tématu [Vytvoření konvencí webového rozhraní API](#create-web-api-conventions).

    V následujícím příkladu se používá výchozí sada konvencí pro všechny akce v *ContactsConventionController*:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použitý na sestavení &mdash; použije zadaný typ konvence na všechny řadiče v aktuálním sestavení. Jako doporučení použijte atributy na úrovni sestavení v souboru *Startup.cs* .

    V následujícím příkladu je použita výchozí sada konvencí pro všechny řadiče v sestavení:

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>Vytvoření konvencí webového rozhraní API

Pokud výchozí konvence rozhraní API nevyhovují vašim potřebám, vytvořte vlastní konvence. Konvence:

* Statický typ s metodami.
* Umožňuje definovat [typy odpovědí](#response-types) a [požadavky na pojmenování](#naming-requirements) u akcí.

### <a name="response-types"></a>Typy odpovědí

Tyto metody jsou opatřeny poznámkami pomocí atributů `[ProducesResponseType]` nebo `[ProducesDefaultResponseType]`. Příklad:

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

Pokud nejsou k dispozici konkrétnější atributy metadat, použití této konvence na sestavení vynutilo:

* Metoda konvence se vztahuje na všechny akce s názvem `Find`.
* V akci `Find` je přítomen parametr s názvem `id`.

### <a name="naming-requirements"></a>Požadavky na pojmenovávání

Atributy `[ApiConventionNameMatch]` a `[ApiConventionTypeMatch]` lze použít na metodu konvence, která určuje akce, na které se vztahují. Příklad:

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

V předchozím příkladu:

* Možnost `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` použitá pro metodu označuje, že konvence odpovídá jakékoli akci s předponou "Find". Mezi příklady akcí pro porovnání patří `Find`, `FindPet`a `FindById`.
* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` použité pro parametr označuje, že konvence odpovídá metodám s přesně jedním parametrem končícím identifikátorem přípony. Příklady zahrnují parametry jako `id` nebo `petId`. `ApiConventionTypeMatch` lze obdobně použít u typů k omezení typu parametru. Argument `params[]` označuje zbývající parametry, které nemusejí být explicitně spárovány.

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
