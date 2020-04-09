---
title: 'Kurz: Volání webového rozhraní API ASP.NET Core pomocí JavaScriptu'
author: rick-anderson
description: Přečtěte si, jak volat ASP.NET základní webové rozhraní API pomocí JavaScriptu.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655251"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Kurz: Volání webového rozhraní API ASP.NET Core pomocí JavaScriptu

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz ukazuje, jak volat ASP.NET základní webové rozhraní API s JavaScriptem pomocí [načíst rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Informace ASP.NET Core 2.2 naleznete v 2.2 verzi [volání webového rozhraní API pomocí JavaScriptu](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

* Kompletní [kurz: Vytvoření webového rozhraní API](xref:tutorials/first-web-api)
* Znalost CSS, HTML a JavaScriptu

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

V této části přidáte stránku HTML obsahující formuláře pro vytváření a správu položek. Obslužné rutiny událostí jsou připojeny k prvkům na stránce. Výsledkem obslužných rutin událostí jsou požadavky HTTP pro metody akce webového rozhraní API. `fetch` Funkce rozhraní Fetch API iniciuje každý požadavek HTTP.

Funkce `fetch` vrátí objekt [Promise,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) který obsahuje odpověď `Response` HTTP reprezentovanou jako objekt. Běžným vzorem je extrahovat tělo odezvy JSON vyvoláním `json` funkce na objektu. `Response` JavaScript aktualizuje stránku podrobnostmi z odpovědi webového rozhraní API.

Nejjednodušší `fetch` volání přijme jeden parametr představující trasu. Druhý parametr, označovaný `init` jako objekt, je volitelný. `init`slouží ke konfiguraci požadavku HTTP.

1. Nakonfigurujte aplikaci tak, aby [zobrazovala statické soubory](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolila výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). Následující zvýrazněný kód je `Configure` potřeba v metodě *Startup.cs*:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Vytvořte složku *wwwroot* v kořenovém adresáři projektu.

1. Vytvořte složku *js* uvnitř složky *wwwroot.*

1. Přidejte soubor HTML s názvem *index.html* do složky *wwwroot.* Nahraďte obsah *souboru index.html* následujícími značkami:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Přidejte soubor JavaScript s názvem *site.js* do složky *wwwroot/js.* Nahraďte obsah souboru *site.js* následujícím kódem:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Ke místnímu testování stránky HTML může být nutná změna nastavení spuštění projektu ASP.NET Core:

1. *Spusťte položku Properties\launchSettings.json*.
1. Odeberte `launchUrl` vlastnost, chcete-li aplikaci vynutit otevření na *adrese index.html*&mdash;výchozího souboru projektu.

Tato ukázka volá všechny metody CRUD webového rozhraní API. Následují vysvětlení požadavků webového rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získání seznamu položek úkolů

V následujícím kódu je na trasu *API/TodoItems* odeslán požadavek HTTP GET:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Když webové rozhraní API vrátí úspěšný `_displayItems` stavový kód, funkce je vyvolána. Každá položka soupou v `_displayItems` parametru pole přijatá tlačítkem Je přidána do tabulky pomocí tlačítek **Upravit** a **Odstranit.** Pokud se požadavek webového rozhraní API nezdaří, je do konzoly prohlížeče zaznamenána chyba.

### <a name="add-a-to-do-item"></a>Přidání položky s cílem

V následujícím kódu:

* Proměnná `item` je deklarována k vytvoření literálové reprezentace objektu položky, kterou chcete provést.
* Požadavek na načtení je nakonfigurován s následujícími možnostmi:
  * `method`&mdash;určuje sloveso akce POST HTTP.
  * `body`&mdash;určuje reprezentaci JSON těla požadavku. JSON je vytvořen předáním literálu `item` objektu uloženého v [json.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) funkce.
  * `headers`&mdash;určuje `Accept` hlavičky `Content-Type` požadavků a http. Obě záhlaví jsou `application/json` nastavena tak, aby určovat typ média přijímány a odesílané, v uvedeném pořadí.
* Požadavek HTTP POST je odeslán na trasu *api/TodoItems.*

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Pokud webové rozhraní API vrátí úspěšný `getItems` stavový kód, funkce je vyvolána k aktualizaci tabulky HTML. Pokud se požadavek webového rozhraní API nezdaří, je do konzoly prohlížeče zaznamenána chyba.

### <a name="update-a-to-do-item"></a>Aktualizace položky s cílem

Aktualizace položky s cílem je podobná přidání položky; existují však dva významné rozdíly:

* Trasa je připnout s jedinečným identifikátorem položky k aktualizaci. Například *api/TodoItems/1*.
* Sloveso akce HTTP je PUT, `method` jak je uvedeno v možnosti.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Odstranění položky pro práci

Chcete-li odstranit položku pro zadání, `method` nastavte `DELETE` možnost požadavku na jedinečný identifikátor položky v adrese URL a zadejte její jedinečný identifikátor.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Přejdete k dalšímu kurzu, kde se dozvíte, jak generovat stránky nápovědy webového rozhraní API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
