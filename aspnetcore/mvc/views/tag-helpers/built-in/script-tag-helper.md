---
title: Pomocník značek Script v ASP.NET Core
author: rick-anderson
ms.author: riande
description: Seznamte se s atributy pomocníka značky skriptu ASP.NET Core a role, které každý atribut hraje při rozšiřování chování značky skriptu HTML.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 5f2fb8a45048804afa8aff2989cd53489e45a33b
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256496"
---
# <a name="script-tag-helper-in-aspnet-core"></a>Pomocník značek Script v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) vygeneruje odkaz na primární soubor nebo soubor skriptu. Obvykle je soubor primárního skriptu na [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Pomocná značka skriptu umožňuje zadat síť CDN pro soubor skriptu a zálohu, pokud CDN není k dispozici. Pomocník značek Script poskytuje výkonnou výhodu sítě CDN s odolností místního hostování.

Následující kód Razor ukazuje `script` prvek souboru rozložení vytvořeného pomocí šablony ASP.NET Core webové aplikace:

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

Následující text je podobný vykreslenému HTML z předchozího kódu (v nevývojovém prostředí):

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

V předchozím kódu pomocník značky skriptu vygeneroval druhý element Script ( `<script>  (window.jQuery || document.write(`), který testuje pro. `window.jQuery` Pokud `window.jQuery` se nenajde, `document.write(` spustí a vytvoří skript. 

## <a name="commonly-used-script-tag-helper-attributes"></a>Běžně používané pomocné atributy značek skriptů

Viz [Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) pro všechny pomocné atributy, vlastnosti a metody značky skriptu.

### <a name="href"></a>href

Upřednostňovaná adresa propojeného prostředku Adresa je předána na vygenerovaný kód HTML ve všech případech.

### <a name="asp-fallback-href"></a>ASP – Fallback – href

Adresa URL šablony stylů CSS, na kterou se má přejít v případě, že primární adresa URL není úspěšná

### <a name="asp-fallback-test-class"></a>ASP – Fallback-test-třída

Název třídy definovaný v šabloně stylů, která se má použít pro záložní test. Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>ASP – Fallback-test-vlastnost

Název vlastnosti CSS, který má být použit pro záložní test. Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>ASP – Fallback-test-hodnota

Hodnota vlastnosti CSS, která má být použita pro záložní test. Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

### <a name="asp-fallback-test-value"></a>ASP – Fallback-test-hodnota

Hodnota vlastnosti CSS, která má být použita pro záložní test. Další informace najdete v tématu.<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
