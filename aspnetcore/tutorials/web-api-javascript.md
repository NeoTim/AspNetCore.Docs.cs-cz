---
title: 'Kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu'
author: rick-anderson
description: Naučte se volat ASP.NET Core webového rozhraní API pomocí JavaScriptu.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 5a31aa2974eb41938db89f97c070c352a26290fd
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681172"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu

Od [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak volat ASP.NET Core webového rozhraní API s JavaScriptem pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Pro ASP.NET Core 2,2 se podívejte na verzi 2,2 [volání webového rozhraní API pomocí JavaScriptu](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

* Úplný [kurz: Vytvoření webového rozhraní API](xref:tutorials/first-web-api)
* Znalost šablon stylů CSS, HTML a JavaScript

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

V této části přidáte stránku HTML obsahující formuláře pro vytváření a správu položek úkolů. Obslužné rutiny událostí jsou připojeny k prvkům na stránce. Obslužné rutiny událostí mají za následek požadavky HTTP na metody akcí webového rozhraní API. Funkce `fetch` pro načtení rozhraní API inicializuje jednotlivé požadavky HTTP.

Funkce `fetch` vrátí objekt `Promise`, který obsahuje odpověď HTTP reprezentovanou jako objekt `Response`. Běžným vzorem je extrakce textu odpovědi JSON vyvoláním funkce `json` na objektu `Response`. JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.

Nejjednodušší `fetch` volání přijímá jeden parametr reprezentující trasu. Druhý parametr, známý jako objekt `init`, je volitelný. pro konfiguraci požadavku HTTP se používá `init`.

1. Nakonfigurujte aplikaci tak, aby [sloužila statickým souborům](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolovala výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). V metodě `Configure` *Startup.cs*je potřeba následující zvýrazněný kód:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Vytvořte složku *wwwroot* v kořenu projektu.

1. Vytvořte složku *js* ve složce *wwwroot* .

1. Do složky *wwwroot* přidejte soubor HTML s názvem *index. html* . Obsah souboru *index. html* nahraďte následujícím kódem:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Do složky *wwwroot/js* přidejte soubor JavaScriptu s názvem *Web. js* . Obsah *stránky site. js* nahraďte následujícím kódem:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:

1. Otevřete *Properties\launchSettings.JSON*.
1. Odebráním vlastnosti `launchUrl` vynutíte otevření aplikace v *indexu. html*&mdash;výchozím souboru projektu.

Tato ukázka volá všechny metody CRUD webového rozhraní API. Následují vysvětlení požadavků webového rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získat seznam úkolů

V následujícím kódu se pošle požadavek HTTP GET do trasy *API/TodoItems* :

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Když webové rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `_displayItems`. Každá položka k tomuto účelu v parametru Array Accept `_displayItems` je přidána do tabulky s tlačítky **Upravit** a **Odstranit** . Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.

### <a name="add-a-to-do-item"></a>Přidat položku úkolů

V následujícím kódu:

* `item` proměnná je deklarována pro sestavení řetězcové literálové reprezentace položky.
* Požadavek na načtení je nakonfigurovaný s následujícími možnostmi:
  * `method`&mdash;určuje operaci POST HTTP Action.
  * `body`&mdash;určuje reprezentaci textu žádosti ve formátu JSON. KÓD JSON je vytvořen předáním literálu objektu uloženého v `item` do funkce [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) .
  * `headers`&mdash;Určuje hlavičku požadavku HTTP `Accept` a `Content-Type`. Obě hlavičky jsou nastavené na `application/json` k určení typu média přijímaného a odeslaného v uvedeném pořadí.
* Požadavek HTTP POST se odešle do trasy *API/TodoItems* .

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Když webové rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `getItems`, která aktualizuje tabulku HTML. Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.

### <a name="update-a-to-do-item"></a>Aktualizace položky úkolů

Aktualizace položky úkolů je podobná přidání. Existují však dva významné rozdíly:

* Trasa má příponu s jedinečným identifikátorem položky, která se má aktualizovat. Například *API/TodoItems/1*.
* Operace HTTP Action je PUT, jak je uvedeno v možnosti `method`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Odstranění položky úkolů

Chcete-li odstranit položku, nastavte možnost `method` požadavku na `DELETE` a v adrese URL zadejte jedinečný identifikátor položky.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Přejděte k dalšímu kurzu, kde se dozvíte, jak vygenerovat stránky s usnadněním webového rozhraní API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
