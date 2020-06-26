---
title: Podpora částečné značky v ASP.NET Core
author: scottaddie
description: Seznamte se s pomocníkem pro ASP.NET Core částečné značky a role, kde každý z jeho atributů hraje při vykreslování částečného zobrazení.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: d92707ff06125a4b65f1ec18e402f5fe86547b5e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407912"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>Podpora částečné značky v ASP.NET Core

[Scott Addie](https://github.com/scottaddie)

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Přehled

Pomocník s částečnou značkou se používá pro vykreslování [částečného zobrazení](xref:mvc/views/partial) na Razor stránkách a aplikacích MVC. Vezměte v úvahu:

* Vyžaduje ASP.NET Core 2,1 nebo novější.
* Je alternativou k [syntaxi pomocníka HTML](xref:mvc/views/partial#reference-a-partial-view).
* Vykreslí částečné zobrazení asynchronně.

Možnosti pomocníka HTML pro vykreslování částečného zobrazení zahrnují:

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

*Produktový* model se používá v ukázkách v tomto dokumentu:

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

Následuje inventarizace atributů pomocníka částečné značky.

## <a name="name"></a>name

`name` Atribut je vyžadován. Označuje název nebo cestu k částečnému zobrazení, které se má vykreslit. Je-li zadán název částečného zobrazení, je zahájen proces [zjišťování zobrazení](xref:mvc/views/overview#view-discovery) . Tento proces se obejít, když je zadána explicitní cesta. Všechny přijatelné `name` hodnoty najdete v tématu [zjišťování částečného zobrazení](xref:mvc/views/partial#partial-view-discovery).

Následující kód používá explicitní cestu, která značí, že *_ProductPartial. cshtml* má být načten ze *sdílené* složky. Pomocí atributu [for](#for) je model předán do částečného zobrazení pro vazbu.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>pro

`for`Atribut přiřadí [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) k vyhodnocení proti aktuálnímu modelu. A `ModelExpression` odvodí `@Model.` syntaxi. Například `for="Product"` lze použít místo `for="@Model.Product"` . Toto výchozí chování odvození je potlačeno pomocí `@` symbolu pro definování vloženého výrazu.

Následující kód načte *_ProductPartial. cshtml*:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

Částečné zobrazení je vázáno na vlastnost přidruženého modelu stránky `Product` :

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>model

`model`Atribut přiřadí instanci modelu, aby byla předána částečnému zobrazení. `model`Atribut nelze použít s atributem [for](#for) .

V následujícím kódu `Product` je vytvořena instance nového objektu a předána do `model` atributu pro vazbu:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>zobrazení dat

`view-data`Atribut přiřadí objekt [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) , který se má předat částečnému zobrazení. Následující kód zpřístupňuje celou kolekci ViewData pro částečné zobrazení:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

V předchozím kódu `IsNumberReadOnly` je klíčová hodnota nastavena na `true` a přidána do kolekce ViewData. V důsledku toho `ViewData["IsNumberReadOnly"]` je k dispozici v následujícím částečném zobrazení:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

V tomto příkladu hodnota `ViewData["IsNumberReadOnly"]` Určuje, zda se pole *číslo* zobrazuje pouze pro čtení.

## <a name="migrate-from-an-html-helper"></a>Migrace z pomocníka jazyka HTML

Zvažte následující příklad asynchronní pomocné rutiny HTML. Kolekce produktů je iterovaná a zobrazená. Na `PartialAsync` první parametr metody je načteno částečné zobrazení *_ProductPartial. cshtml* . Instance `Product` modelu je předána do částečného zobrazení pro vazbu.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

Následující pomocník částečné značky dosáhne stejné chování při asynchronním vykreslování jako `PartialAsync` Pomocník HTML. `model`Atributu je přiřazena `Product` instance modelu pro vazbu k částečnému zobrazení.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
