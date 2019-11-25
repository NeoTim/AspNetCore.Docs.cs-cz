---
title: Pomocník značek Script v ASP.NET Core
author: rick-anderson
ms.author: riande
description: Seznamte se s atributy pomocníka značky skriptu ASP.NET Core a role, které každý atribut hraje při rozšiřování chování značky skriptu HTML.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: c3d9148bd62dcc045873cc3a72884ae458349d70
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317124"
---
# <a name="script-tag-helper-in-aspnet-core"></a>Pomocník značek Script v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) vygeneruje odkaz na primární soubor nebo soubor skriptu. Obvykle je soubor primárního skriptu na [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Pomocná značka skriptu umožňuje zadat síť CDN pro soubor skriptu a zálohu, pokud CDN není k dispozici. Pomocník značek Script poskytuje výkonnou výhodu sítě CDN s odolností místního hostování.

Následující kód Razor ukazuje `script` element s Fallback:

```HTML
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

## <a name="commonly-used-script-tag-helper-attributes"></a>Běžně používané pomocné atributy značek skriptů

Viz [Pomocník značek Script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) pro všechny pomocné atributy, vlastnosti a metody značky skriptu.

### <a name="asp-fallback-test"></a>ASP – Fallback-test

Metoda skriptu definovaná v primárním skriptu pro použití pro záložní test. Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.

### <a name="asp-fallback-src"></a>ASP – Fallback – src

Adresa URL značky skriptu, na kterou se má přejít v případě, že primární z nich selhala Další informace najdete v tématu <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.

## <a name="additional-resources"></a>Další zdroje informací:

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
