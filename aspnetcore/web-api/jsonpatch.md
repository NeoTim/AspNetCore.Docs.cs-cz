---
title: JsonPatch in ASP.NET Core web API
author: tdykstra
description: Zjistěte, jak zpracovávat požadavky oprava JSON v webovému rozhraní API ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 97264903d85dbb397e85fdbf7b070e2aaae74bc8
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815543"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch in ASP.NET Core web API

podle [Petr Dykstra](https://github.com/tdykstra)

Tento článek vysvětluje, jak zpracovávat požadavky oprava JSON v webovému rozhraní API ASP.NET Core.

## <a name="patch-http-request-method"></a>Metoda požadavku PATCH HTTP

PUT a [oprava](https://tools.ietf.org/html/rfc5789) metody se používají k aktualizaci existující prostředek. Rozdíl mezi nimi je, že PUT nahradí celý zdroj, zatímco oprava určuje pouze změny.

## <a name="json-patch"></a>Oprava JSON

[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro zadávání aktualizace použije k prostředku. Dokument opravy JSON obsahuje celou řadu *operace*. Každou operaci identifikuje konkrétní typ změny, jako například přidat k elementu pole nebo nahraďte hodnotu vlastnosti.

Například následující dokumenty JSON představují prostředek, dokument opravy JSON pro daný zdroj a výsledek použití operace opravy.

### <a name="resource-example"></a>Příklad prostředků

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Příklad opravy JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Ve výše uvedeného kódu JSON:

* `op` Vlastnost určuje typ operace.
* `path` Vlastnost označuje elementu, který chcete aktualizovat.
* `value` Vlastnost obsahuje novou hodnotu.

### <a name="resource-after-patch"></a>Po opravě prostředku

Zde je prostředek po použití předchozí dokument opravy JSON:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Změny provedené při použití dokument opravy JSON pro prostředek jsou atomické: Pokud všechny operace v seznamu selže, použije se žádná operace v seznamu.

## <a name="path-syntax"></a>Syntaxe cesty

[Cesta](https://tools.ietf.org/html/rfc6901) vlastnost objektu operace má lomítka mezi úrovněmi. Například, `"/address/zipCode"`.

Založený na nule indexy se používají k určení prvků pole. První prvek `addresses` pole by na `/addresses/0`. K `add` na konec pole používejte pomlčkou (-) místo číslo indexu: `/addresses/-`.

### <a name="operations"></a>Operace

Následující tabulka ukazuje podporované operace definované v [oprava JSON specifikace](https://tools.ietf.org/html/rfc6902):

|Operace  | Poznámky |
|-----------|--------------------------------|
| `add`     | Přidáte vlastnost nebo pole elementu. Pro existující vlastnost: nastavení hodnoty.|
| `remove`  | Odeberte element vlastnost nebo pole. |
| `replace` | Stejné jako `remove` následovaný `add` ve stejném umístění. |
| `move`    | Stejné jako `remove` ze zdroje, za nímž následuje `add` do cíle pomocí hodnoty ze zdroje. |
| `copy`    | Stejné jako `add` do cíle pomocí hodnoty ze zdroje. |
| `test`    | Vrátí stavový kód úspěchu, pokud hodnotu `path` = zadaný `value`.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch in ASP.NET Core

ASP.NET Core provádění opravu JSON je součástí [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) balíček NuGet. Je součástí balíčku [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) Microsoft.aspnetcore.all.

## <a name="action-method-code"></a>Kód metody akce

V rozhraní API adaptéru metodu akce pro opravu JSON:

* Je opatřen poznámkou `HttpPatch` atribut.
* Přijímá `JsonPatchDocument<T>`, obvykle s [FromBody].
* Volání `ApplyTo` na dokument opravy a změny aplikujte.

Tady je příklad:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Tento kód z ukázkové aplikace funguje u následujících `Customer` modelu.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Ukázka metody akce:

* Vytvoří `Customer`.
* Oprava se vztahuje.
* Vrátí výsledek v textu odpovědi.

 V reálné aplikaci byste kód načtou data z úložiště, jako je databáze a aktualizovat databázi po použití opravy.

### <a name="model-state"></a>Stav modelu

V předchozím příkladu metoda akce volá přetížení `ApplyTo` stavu modelu, která má jako jeden ze svých parametrů. Pomocí této možnosti můžete získat chybové zprávy v odpovědi. Následující příklad ukazuje text 400 Chybný požadavek odpovědi pro `test` operace:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dynamické objekty

V následujícím příkladu metoda akce ukazuje, jak použít opravu na dynamický objekt.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operace přidání

* Pokud `path` odkazuje na element pole: Vloží nový prvek před vlákna zadaného parametrem `path`.
* Pokud `path` odkazuje na vlastnost: Nastaví hodnotu vlastnosti.
* Pokud `path` odkazuje na neexistující umístění:
  * Pokud dynamických objektů je prostředkem o opravu: Přidá vlastnost.
  * Pokud statických objektů je prostředkem o opravu: požadavek selže.

Následující ukázkový dokument opravy nastaví hodnotu `CustomerName` a přidá `Order` objekt na konec objektu `Orders` pole.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operace odebrání

* Pokud `path` odkazuje na element pole: Odebere element.
* Pokud `path` odkazuje na vlastnost:
  * Pokud dynamických objektů je prostředkem o opravu: Odebere vlastnost.
  * Pokud je prostředkem o opravu statický objekt: 
    * Pokud je vlastnost s možnou hodnotou NULL: Nastaví na hodnotu null.
    * Když se tato vlastnost je null, nastaví `default<T>`.

Následující ukázkové sady dokumentů oprava `CustomerName` null a odstraní `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operaci nahrazení

Tato operace je funkčně stejný jako `remove` následovaný `add`.

Následující ukázkový dokument opravy nastaví hodnotu `CustomerName` a nahradí `Orders[0]`s novou `Order` objektu.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operace přesunu

* Pokud `path` odkazuje na element pole: kopie `from` element umístění `path` elementu, pak spustí `remove` operace `from` element.
* Pokud `path` odkazuje na vlastnost: kopíruje hodnotu `from` vlastnost `path` vlastnost, pak spustí `remove` operace `from` vlastnost.
* Pokud `path` odkazuje na neexistující vlastnost:
  * Pokud statických objektů je prostředkem o opravu: požadavek selže.
  * Pokud dynamických objektů je prostředkem o opravu: kopie `from` vlastnost umístění indikován `path`, pak spustí `remove` operace `from` vlastnost.

Dokument opravy následující ukázka:

* Zkopíruje hodnoty `Orders[0].OrderName` k `CustomerName`.
* Nastaví `Orders[0].OrderName` na hodnotu null.
* Přesune `Orders[1]` před `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operace kopírování

Tato operace je funkčně stejný jako `move` operaci bez finální `remove` kroku.

Dokument opravy následující ukázka:

* Zkopíruje hodnoty `Orders[0].OrderName` k `CustomerName`.
* Vloží kopii `Orders[1]` před `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Testovací operace

Pokud je hodnota v umístění indikován `path` se liší od hodnoty podle `value`, požadavek selže. V takovém případě celý požadavek PATCH selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak úspěšné.

`test` Operace se běžně používá k zabránění aktualizace, když dojde ke konfliktu souběžnosti.

Následující ukázkový dokument opravy nemá žádný vliv, pokud počáteční hodnota `CustomerName` je "John", protože se test nezdaří:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Získat kód

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Stažení](xref:index#how-to-download-a-sample)).

Testování ukázky, spusťte aplikaci a odesílat požadavky HTTP s následujícími nastaveními:

* URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP: `PATCH`
* Záhlaví: `Content-Type: application/json-patch+json`
* Text: Zkopírujte a vložte jednou z ukázek dokumentu opravy JSON z *JSON* složky projektu.

## <a name="additional-resources"></a>Další zdroje

* [Oprava IETF RFC 5789 specifikace – metoda](https://tools.ietf.org/html/rfc5789)
* [Specifikaci IETF RFC 6902 JSON opravy](https://tools.ietf.org/html/rfc6902)
* [Oprava JSON 6901 IETF RFC specifikace formátu cesty](https://tools.ietf.org/html/rfc6901)
* [Dokumentace ke službě oprava JSON](https://jsonpatch.com/). Obsahuje odkazy na prostředky pro vytváření dokumentů oprava JSON.
* [ASP.NET Core oprava JSON zdrojového kódu](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)
