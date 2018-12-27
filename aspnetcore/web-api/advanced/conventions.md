---
title: Řiďte se vytváření webového rozhraní API
author: pranavkm
description: Další informace o vytváření webových rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: 481e3810f1e1aca40e0ee1ce3da6c67dc9d841f4
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425104"
---
# <a name="use-web-api-conventions"></a>Řiďte se vytváření webového rozhraní API

Podle [Pranav Krishnamoorthy](https://github.com/pranavkm) a [Scott Addie](https://github.com/scottaddie)

ASP.NET Core 2.2 a novější zahrnuje způsob, jak extrahovat běžné [dokumentace k rozhraní API](xref:tutorials/web-api-help-pages-using-swagger) a použít ji pro více akcí, řadiče nebo všech řadičů v rámci sestavení. Vytváření webových rozhraní API jsou jako náhrada upravení jednotlivé akce s [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).

Konvence umožňuje:

* Definujte nejběžnější návratové typy a stavové kódy vrácená z konkrétní typ akce.
* Určete akce, které se liší od standardu definované.

ASP.NET Core MVC 2.2 a novější obsahuje sadu výchozích konvencí v `Microsoft.AspNetCore.Mvc.DefaultApiConventions`. Konvence jsou založeny na kontroler (*ValuesController.cs*) k dispozici v ASP.NET Core **API** šablony projektu. Pokud vaše akce provést tyto vzory se dají v šabloně, měli byste být úspěšného používání výchozích konvencí. Pokud výchozí konvence nevyhovují vašim potřebám, přečtěte si téma [vytvoření webového rozhraní API konvence](#create-web-api-conventions).

V době běhu <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumí konvence. `ApiExplorer` je abstraktní MVC ke komunikaci s [OpenAPI](https://www.openapis.org/) (také označované jako Swagger) dokumentu generátorů. Atributy použité konvence jsou spojeny s akcí a jsou zahrnuty v dokumentaci k OpenAPI akce. [Rozhraní API analyzátorů](xref:web-api/advanced/analyzers) srozuměni konvence. Pokud je vaše akce neobvyklé (například vrátí stavový kód, který nebyl zdokumentován použité konvence), upozornění doporučuje dokumentu stavový kód.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Použít konvence webového rozhraní API

Konvence není compose; Každá akce může být spojen s konvence přesně jeden. Konkrétnější konvence mají přednost před specifické pro less konvence. Výběr je Nedeterministický, když dva nebo více konvence stejnou prioritou použijete akci. Existují tyto možnosti použít konvenci akci, od nejkonkrétnější po nejméně konkrétní:

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Platí pro jednotlivé akce a určuje konvence typ a metodu konvence, která se použije.

    V následujícím příkladu, výchozí typ konvence společnosti `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` konvence metoda platí pro `Update` akce:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` Metoda konvence platí následující atributy pro akci:

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(204)]
    [ProducesResponseType(404)]
    [ProducesResponseType(400)]
    ```

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použijí pro kontroler &mdash; typ zadané konvence se vztahuje na všechny akce v kontroleru. Metoda konvence je upravený pomocí tipů, které určují akce, u kterých bude použito metodu konvence. Další informace o pomocných parametrů najdete v tématu [vytvoření webového rozhraní API konvence](#create-web-api-conventions)).

    V následujícím příkladu se použije výchozí sadu pravidel pro všechny akce v *ContactsConventionController*:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použité u sestavení &mdash; typ zadané konvence se vztahuje na všechny řadiče v aktuálním sestavení. Jako doporučení, použijte atributy úrovně sestavení, abyste `Startup` třídy.

    V následujícím příkladu se použije výchozí sadu konvence pro všemi řadiči v sestavení:

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>Vytvoření webového rozhraní API konvence

Pokud výchozích konvencí API nevyhovují vašim potřebám, vytvořte vlastní zásady odvíjející. Konvence je:

* Statického typu metody.
* Dokáže definování [typů odpovědi](#response-types) a [požadavkům na názvy](#naming-requirements) na akce.

### <a name="response-types"></a>Typy odezvy

Tyto metody jsou opatřeny poznámkami s `[ProducesResponseType]` nebo `[ProducesDefaultResponseType]` atributy. Příklad:

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(200)]
    [ProducesResponseType(404)]
    public static void Find(int id)
    {
    }
}
```

Pokud chybí konkrétnější atributy metadat, vynucuje použití Tato konvence pro sestavení, který:

* Metoda konvence platí pro všechny akce s názvem `Find`.
* Parametr s názvem `id` je k dispozici na `Find` akce.

### <a name="naming-requirements"></a>Požadavky na pojmenování

`[ApiConventionNameMatch]` a `[ApiConventionTypeMatch]` atributy lze použít pro metodu vytváření názvů, který určuje akce, na které se vztahují. Příklad:

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

V předchozím příkladu:

* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` Možnost použít pro metodu označuje, že odpovídá konvenci žádnou akci s předponou "Najít". Příklady odpovídající akce `Find`, `FindPet`, a `FindById`.
* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` Použitý pro parametr označuje, že odpovídá konvenci metody s koncovkou identifikátorem přípony přesně jeden parametr. Příklady zahrnují parametry, jako například `id` nebo `petId`. `ApiConventionTypeMatch` Podobně lze na typy k omezení parametru typu. A `params[]` argument určuje zbývající parametry, které není potřeba explicitně odpovídat.

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
