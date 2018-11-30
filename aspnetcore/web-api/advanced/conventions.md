---
title: Řiďte se vytváření webového rozhraní API
author: pranavkm
description: Další informace o vytváření webových rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 11/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: ede9a46c160cf6a49aa93da710af0bf0b8f59acc
ms.sourcegitcommit: c4572be5ebb301013a5698caf9b5572b76cb2e34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710072"
---
# <a name="use-web-api-conventions"></a>Řiďte se vytváření webového rozhraní API

ASP.NET Core 2.2 představuje způsob, jak extrahovat běžné [dokumentace k rozhraní API](xref:tutorials/web-api-help-pages-using-swagger) a použít ji pro více akcí, řadiče nebo všech řadičů v rámci sestavení. Vytváření webových rozhraní API jsou jako náhrada upravení jednotlivé akce s [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute). Umožňuje definovat nejběžnější "konvenční" návratové typy a stavové kódy, které vracejí z akci se způsob, jak vybrat metodu vytváření názvů, který se vztahuje na akce.

Ve výchozím nastavení, ASP.NET Core MVC 2.2 dodává se sadou výchozích konvencí `Microsoft.AspNetCore.Mvc.DefaultApiConventions`. Konvence jsou založeny na kontroler, který vygeneruje uživatelské rozhraní ASP.NET Core. Pokud vaše akce mají tvar vytváří tento generování uživatelského rozhraní, byste měli úspěšného používání výchozích konvencí.

V době běhu <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumí konvence. `ApiExplorer` je abstraktní MVC ke komunikaci s generátorů dokumentu Open API. Atributy použité konvence k akci a jsou zahrnuty v dokumentaci k akce Swaggeru. Rozhraní API analyzátorů srozuměni konvence. Pokud je vaše akce neobvyklé (například vrátí stavový kód, který nebyl zdokumentován použité konvence), vyvolá upozornění, podporu vám ho zdokumentovat.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Použít konvence webového rozhraní API

Existují tři způsoby, jak použít konvence. Konvence není compose. Každá akce může být spojen s konvence přesně jeden. Konkrétnější konvence (podrobnosti jsou dole) mají přednost před těmi méně konkrétní. Výběr je Nedeterministický, když dva nebo více konvence stejnou prioritou použijete akci. Existují tyto možnosti použít konvenci akci, od nejkonkrétnější po nejméně konkrétní:

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Platí pro jednotlivé akce a určuje konvence typ a metodu konvence, která se použije. V následujícím příkladu metoda konvence `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` aplikován `Update` akce:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventionmethod&highlight=2-3)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použijí pro kontroler &mdash; konvence typu se vztahuje na všechny akce v kontroleru. Metody vytváření názvů jsou vybaveny pomocné parametry, které určují akce, které by použít (podrobnosti jako součást pro vytváření konvencí). Příklad:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventiontypeattribute)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použité u sestavení &mdash; konvence typ se vztahuje na všechny řadiče v aktuálním sestavení. Příklad:

    [!code-csharp[](conventions/sample/Startup.cs?name=apiconventiontypeattribute)]

## <a name="create-web-api-conventions"></a>Vytvoření webového rozhraní API konvence

Konvence je statický typ metody. Tyto metody jsou opatřeny poznámkami s `[ProducesResponseType]` nebo `[ProducesDefaultResponseType]` atributy.

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

Použití této konvence pro sestavení výsledkem metodu konvence použití na každou akci s názvem `Find` a mít přesně jeden parametr s názvem `id`, za předpokladu, že nemají další konkrétnější atributy metadat.

Kromě `[ProducesResponseType]` a `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` a `[ApiConventionTypeMatch]` můžete použít pro metodu vytváření názvů, který určuje metody, které se vztahují na. Příklad:

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` Možnost použít pro metodu, označuje, že tato konvence tak dlouho, dokud je předponu "Najít" odpovídá žádnou akci. To zahrnuje metody jako `Find`, `FindPet`, nebo `FindById`.
* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` Použitý pro parametr určuje, že tato konvence odpovídá metody s koncovkou identifikátorem přípony přesně jeden parametr. To zahrnuje parametry jako `id` nebo `petId`. `ApiConventionTypeMatch` Podobně lze na typy, chcete-li omezit typu parametru. A `params[]` argument můžete použít k označení zbývající parametry, které není potřeba explicitně odpovídat.
