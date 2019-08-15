---
title: 'Kurz: Volání webového rozhraní API pomocí jQuery pomocí ASP.NET Core'
author: rick-anderson
description: Naučte se volat ASP.NET Core webového rozhraní API pomocí jQuery.
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: a319e4b4ce09e9b09afeaff065d5740276deb115
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022559"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a>Kurz: Volání rozhraní Web API ASP.NET Core pomocí jQuery

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak volat ASP.NET Core webového rozhraní API pomocí jQuery.

::: moniker range="< aspnetcore-3.0"

Pro ASP.NET Core 2,2 se podívejte na verzi 2,2 [volání webového rozhraní API pomocí jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

* Úplný [kurz: Vytvoření webového rozhraní API](xref:tutorials/first-web-api)
* Znalost šablon stylů CSS, HTML, JavaScript a jQuery

## <a name="call-the-api-with-jquery"></a>Volání rozhraní API pomocí jQuery

V této části se přidá stránku HTML, který používá jQuery volat webové rozhraní api. jQuery zahájí požadavek a aktualizuje stránku s podrobnostmi o z odpovědi rozhraní API.

Nakonfigurujte aplikaci tak, aby sloužila pro [statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) , a [Povolte výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aktualizací *Startup.cs* pomocí následujícího zvýrazněného kódu:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

Vytvoření *wwwroot* složku v adresáři projektu.

Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře. Nahraďte jeho obsah následujícím kódem:

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře. Nahraďte jeho obsah následujícím kódem:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:

* Otevřít *Properties\launchSettings.json*.
* Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.

Existuje několik způsobů, jak získat jQuery. V předchozím fragmentu kódu je načíst knihovnu ze sítě CDN.

Tato ukázka volá všechny metody CRUD rozhraní API. Následuje vysvětlení volání rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získání seznamu úkolů

JQuery [ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `GET` žádosti na rozhraní API, které vrací JSON představující pole položek úkolů. `success` Funkce zpětného volání je vyvolána, pokud neproběhne. Při zpětném volání modelu DOM se aktualizuje informacemi úkolů.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Přidat položku seznamu úkolů

[Ajax](https://api.jquery.com/jquery.ajax/) funkce odešle `POST` požadavek se úkol v textu požadavku. `accepts` a `contentType` možnosti jsou nastaveny na `application/json` zadat typ média, který se přijalo a odeslalo. Položky seznamu úkolů je převést na JSON pomocí [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Rozhraní API po návratu úspěšné stavový kód, `getData` funkce se vyvolala aktualizovat tabulku HTML.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aktualizace položky seznamu úkolů

Aktualizace položky úkolu je podobné jako přidání jednoho. `url` Změny přidat jedinečný identifikátor položky a `type` je `PUT`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Odstranit úkol

Odstranění položky úkolu se provádí tak, že nastavíte `type` při volání AJAX `DELETE` a zadáte jedinečný identifikátor položky v adrese URL.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

Přejděte k dalšímu kurzu se naučíte generování stránek nápovědy rozhraní API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
