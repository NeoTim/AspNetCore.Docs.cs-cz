---
title: 'Kurz: volání ASP.NET Core webového rozhraní API pomocí JavaScriptu'
author: rick-anderson
description: Naučte se volat ASP.NET Core webového rozhraní API pomocí JavaScriptu.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 0070816149d64fc1d71d453eb0f135050c78597a
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "72378707"
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

V této části přidáte stránku HTML obsahující formuláře pro vytváření a správu položek úkolů. Obslužné rutiny událostí jsou připojeny k prvkům na stránce. Obslužné rutiny událostí mají za následek požadavky HTTP na metody akcí webového rozhraní API. Funkce `fetch` rozhraní API pro načtení inicializuje jednotlivé požadavky HTTP.

Funkce `fetch` vrátí objekt `Promise`, který obsahuje odpověď HTTP reprezentovanou jako objekt `Response`. Běžným vzorem je extrakce textu odpovědi JSON vyvoláním funkce `json` u objektu `Response`. JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.

Nejjednodušší volání `fetch` přijímá jeden parametr reprezentující trasu. Druhý parametr, označovaný jako objekt `init`, je volitelný. pro konfiguraci požadavku HTTP se používá `init`.

1. Nakonfigurujte aplikaci tak, aby [sloužila statickým souborům](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolovala výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). Následující zvýrazněný kód je vyžadován v metodě `Configure` *Startup.cs*:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. V kořenovém adresáři projektu vytvořte adresář *wwwroot* .

1. Do adresáře *wwwroot* přidejte soubor HTML s názvem *index. html* . Nahraďte jeho obsah následujícím kódem:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Do adresáře *wwwroot* přidejte soubor JavaScriptu s názvem *Web. js* . Nahraďte jeho obsah následujícím kódem:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Pro místní testování stránky HTML může být nutné změnit nastavení spouštění ASP.NET Core projektu:

1. Otevřete *Properties\launchSettings.JSON*.
1. Odebráním vlastnosti `launchUrl` vynutíte otevření aplikace v *indexu. html* &mdash;the výchozí soubor projektu.

Tato ukázka volá všechny metody CRUD webového rozhraní API. Následují vysvětlení požadavků webového rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získat seznam úkolů

V následujícím kódu se pošle požadavek HTTP GET do trasy *API/TodoItems* :

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Když webové rozhraní API vrátí úspěšný kód stavu, vyvolá se funkce `_displayItems`. Každá položka k tomuto účelu v parametru Array Accept `_displayItems` je přidána do tabulky s tlačítky **Upravit** a **Odstranit** . Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.

### <a name="add-a-to-do-item"></a>Přidat položku úkolů

V následujícím kódu:

* Proměnná `item` je deklarována k sestavení řetězcové literálové reprezentace položky.
* Požadavek na načtení je nakonfigurovaný s následujícími možnostmi:
    * `method`&mdash;určuje operaci POST HTTP Action.
    * `body`&mdash;určuje reprezentaci textu žádosti ve formátu JSON. KÓD JSON je vytvořen předáním literálu objektu uloženého v `item` do funkce [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) .
    * `headers`&mdash;Určuje hlavičku požadavku HTTP `Accept` a `Content-Type`. Obě hlavičky jsou nastavené na `application/json` a určují typ média, který se přijímá a pošle v uvedeném pořadí.
* Požadavek HTTP POST se odešle do trasy *API/TodoItems* .

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Když webové rozhraní API vrátí úspěšný stavový kód, vyvolá se funkce `getItems`, aby se aktualizovala tabulka HTML. Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.

### <a name="update-a-to-do-item"></a>Aktualizace položky úkolů

Aktualizace položky úkolů je podobná přidání. Existují však dva významné rozdíly:

* Trasa má příponu s jedinečným identifikátorem položky, která se má aktualizovat. Například *API/TodoItems/1*.
* Operace HTTP Action je PUT, jak je uvedeno v možnosti `method`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Odstranění položky úkolů

Chcete-li odstranit položku, nastavte možnost `method` žádosti na hodnotu `DELETE` a v adrese URL zadejte jedinečný identifikátor položky.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Přejděte k dalšímu kurzu, kde se dozvíte, jak vygenerovat stránky s usnadněním webového rozhraní API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
