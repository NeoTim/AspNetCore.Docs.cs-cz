---
title: 'Kurz: Volání ASP.NET Core webového rozhraní API pomocí JavaScriptu'
author: rick-anderson
description: Naučte se volat ASP.NET Core webového rozhraní API pomocí JavaScriptu.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 0070816149d64fc1d71d453eb0f135050c78597a
ms.sourcegitcommit: de17150e5ec7507d7114dde0e5dbc2e45a66ef53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70116655"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Kurz: Volání ASP.NET Core webového rozhraní API pomocí JavaScriptu

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak volat ASP.NET Core webového rozhraní API s JavaScriptem pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Pro ASP.NET Core 2,2 se podívejte na verzi 2,2 [volání webového rozhraní API pomocí JavaScriptu](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

* Úplný [kurz: Vytvoření webového rozhraní API](xref:tutorials/first-web-api)
* Znalost šablon stylů CSS, HTML a JavaScript

## <a name="call-the-web-api-with-javascript"></a>Volání webového rozhraní API pomocí JavaScriptu

V této části přidáte stránku HTML obsahující formuláře pro vytváření a správu položek úkolů. Obslužné rutiny událostí jsou připojeny k prvkům na stránce. Obslužné rutiny událostí mají za následek požadavky HTTP na metody akcí webového rozhraní API. `fetch` Funkce načíst rozhraní API inicializuje jednotlivé požadavky HTTP.

Funkce vrátí objekt, který obsahuje odpověď `Response` http reprezentovanou jako objekt. `Promise` `fetch` Běžným vzorem je extrakce textu odpovědi JSON vyvoláním `json` funkce `Response` na objektu. JavaScript aktualizuje stránku s podrobnostmi z odpovědi webového rozhraní API.

Nejjednodušší `fetch` volání přijímá jeden parametr reprezentující trasu. Druhý parametr, známý jako `init` objekt, je volitelný. `init`slouží ke konfiguraci požadavku HTTP.

1. Nakonfigurujte aplikaci tak, aby [sloužila statickým souborům](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [povolovala výchozí mapování souborů](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). V `Configure` metodě *Startup.cs*je potřeba následující zvýrazněný kód:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. V kořenovém adresáři projektu vytvořte adresář *wwwroot* .

1. Přidat soubor HTML s názvem *index.html* k *wwwroot* adresáře. Nahraďte jeho obsah následujícím kódem:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Přidejte soubor JavaScriptu s názvem *site.js* k *wwwroot* adresáře. Nahraďte jeho obsah následujícím kódem:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Ke změně nastavení spouštěcí projekt ASP.NET Core může být nutné testovací stránka HTML místně:

1. Otevřít *Properties\launchSettings.json*.
1. Odeberte `launchUrl` vlastnost, aby se aplikace na *index.html*&mdash;výchozí soubor projektu.

Tato ukázka volá všechny metody CRUD webového rozhraní API. Následují vysvětlení požadavků webového rozhraní API.

### <a name="get-a-list-of-to-do-items"></a>Získání seznamu úkolů

V následujícím kódu se pošle požadavek HTTP GET do trasy *API/TodoItems* :

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Když webové rozhraní API vrátí úspěšný kód stavu, `_displayItems` funkce se vyvolá. Každá položka úkolů v parametru pole Accepted `_displayItems` je přidána do tabulky s tlačítky **Upravit** a **Odstranit** . Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.

### <a name="add-a-to-do-item"></a>Přidat položku seznamu úkolů

V následujícím kódu:

* `item` Proměnná je deklarována pro sestavení řetězcové literálové reprezentace položky.
* Požadavek na načtení je nakonfigurovaný s následujícími možnostmi:
    * `method`&mdash;Určuje operaci POST HTTP Action.
    * `body`&mdash;Určuje reprezentaci textu žádosti ve formátu JSON. Kód JSON je vytvořen předáním literálu objektu uloženého `item` v do funkce [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) .
    * `headers`&mdash;Určuje hlavičky požadavků `Content-Type`ahttp `Accept` . Obě hlavičky jsou nastaveny na `application/json` zadání typu média přijímaného a odeslaného v uvedeném pořadí.
* Požadavek HTTP POST se odešle do trasy *API/TodoItems* .

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Když webové rozhraní API vrátí úspěšný kód stavu, `getItems` funkce se vyvolá, aby se aktualizovala tabulka HTML. Pokud se požadavek webového rozhraní API nepovede, do konzoly prohlížeče se zaprotokoluje chyba.

### <a name="update-a-to-do-item"></a>Aktualizace položky seznamu úkolů

Aktualizace položky úkolů je podobná přidání. Existují však dva významné rozdíly:

* Trasa má příponu s jedinečným identifikátorem položky, která se má aktualizovat. Například *API/TodoItems/1*.
* Operace HTTP Action je PUT, jak je uvedeno v `method` možnosti.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Odstranit úkol

Chcete-li odstranit položku, nastavte `method` možnost žádosti na `DELETE` a v adrese URL zadejte jedinečný identifikátor položky.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Přejděte k dalšímu kurzu, kde se dozvíte, jak vygenerovat stránky s usnadněním webového rozhraní API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
