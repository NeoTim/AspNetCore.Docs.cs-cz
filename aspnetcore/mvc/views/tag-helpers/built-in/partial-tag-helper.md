---
title: Podpora částečné značky v ASP.NET Core
author: scottaddie
description: Seznamte se s pomocníkem pro ASP.NET Core částečné značky a role, kde každý z jeho atributů hraje při vykreslování částečného zobrazení.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 508f91cdcd93c149602223250520eecb73625b24
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880992"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>Podpora částečné značky v ASP.NET Core

[Scott Addie](https://github.com/scottaddie)

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Přehled

Pomocník s částečnou značkou se používá pro vykreslování [částečného zobrazení](xref:mvc/views/partial) v aplikacích Razor Pages a MVC. Vezměte v úvahu:

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

`name` Atribut je vyžadován. Označuje název nebo cestu k částečnému zobrazení, které se má vykreslit. Je-li zadán název částečného zobrazení, je zahájen proces [zjišťování zobrazení](xref:mvc/views/overview#view-discovery) . Tento proces se obejít, když je zadána explicitní cesta. Všechny přijatelné hodnoty `name` najdete v tématu [zjišťování částečného zobrazení](xref:mvc/views/partial#partial-view-discovery).

Následující kód používá explicitní cestu, která značí, že *_ProductPartial. cshtml* má být načten ze *sdílené* složky. Pomocí atributu [for](#for) je model předán do částečného zobrazení pro vazbu.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>pro

Atribut `for` přiřadí [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) k vyhodnocení proti aktuálnímu modelu. `ModelExpression` odvodí `@Model.` syntaxi. Například `for="Product"` lze použít místo `for="@Model.Product"`. Toto výchozí chování odvození je přepsáno pomocí symbolu `@` pro definování vloženého výrazu.

Následující kód načte *_ProductPartial. cshtml*:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

Částečné zobrazení je vázáno na vlastnost `Product` přidruženého modelu stránky:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>model

Atribut `model` přiřadí instanci modelu, aby byla předána částečnému zobrazení. Atribut `model` nelze použít s atributem [for](#for) .

V následujícím kódu je vytvořen nový objekt `Product` a předán do atributu `model` pro vazbu:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>view-data

Atribut `view-data` přiřadí objekt [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) , který se má předat částečnému zobrazení. Následující kód zpřístupňuje celou kolekci ViewData pro částečné zobrazení:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

V předchozím kódu je hodnota `IsNumberReadOnly` klíče nastavena na `true` a přidána do kolekce ViewData. V důsledku toho je `ViewData["IsNumberReadOnly"]` k dispozici v následujícím částečném zobrazení:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

V tomto příkladu hodnota `ViewData["IsNumberReadOnly"]` určuje, zda se pole *číslo* zobrazuje pouze pro čtení.

## <a name="migrate-from-an-html-helper"></a>Migrace z pomocníka jazyka HTML

Zvažte následující příklad asynchronní pomocné rutiny HTML. Kolekce produktů je iterovaná a zobrazená. Na první parametr metody `PartialAsync` je načteno částečné zobrazení *_ProductPartial. cshtml* . Instance `Product`ho modelu je předána do částečného zobrazení pro vazbu.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

Následující pomocník částečné značky dosáhne stejného chování při asynchronním vykreslování jako pomocníka `PartialAsync` HTML. Atributu `model` je přiřazena instance `Product` modelu pro vazbu k částečnému zobrazení.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
