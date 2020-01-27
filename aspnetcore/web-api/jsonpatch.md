---
title: JsonPatch v ASP.NET Core Web API
author: rick-anderson
description: Naučte se zpracovávat požadavky na opravy JSON ve ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: e57556e4b3fba55c6c187092593ffab4e31ee2d9
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727020"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch v ASP.NET Core Web API

[Dykstra](https://github.com/tdykstra) a [Kirka Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.

## <a name="package-installation"></a>Instalace balíčku

Podpora pro JsonPatch je povolená pomocí balíčku `Microsoft.AspNetCore.Mvc.NewtonsoftJson`. Chcete-li povolit tuto funkci, aplikace musí:

* Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) .
* Aktualizujte metodu `Startup.ConfigureServices` projektu tak, aby zahrnovala volání `AddNewtonsoftJson`:

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

`AddNewtonsoftJson` je kompatibilní s metodami registrace služby MVC:

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a>JsonPatch, AddNewtonsoftJson a System. text. JSON
  
`AddNewtonsoftJson` nahrazuje formátovací moduly vstupu a výstupu založené na `System.Text.Json` používané pro formátování **veškerého** obsahu JSON. Chcete-li přidat podporu pro `JsonPatch` pomocí `Newtonsoft.Json`, zatímco ostatní formátovací moduly zůstaly beze změny, aktualizujte `Startup.ConfigureServices` projektu následujícím způsobem:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Předchozí kód vyžaduje odkaz na [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) a následující příkazy using:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>Oprava metody požadavku HTTP

Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku. Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.

## <a name="json-patch"></a>Oprava JSON

[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku. Dokument opravy JSON má pole *operací*. Každá operace identifikuje konkrétní typ změny, jako je například přidání prvku pole nebo nahrazení hodnoty vlastnosti.

Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.

### <a name="resource-example"></a>Příklad prostředku

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Příklad opravy JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

V předchozím kódu JSON:

* Vlastnost `op` označuje typ operace.
* Vlastnost `path` určuje prvek, který se má aktualizovat.
* Vlastnost `value` poskytuje novou hodnotu.

### <a name="resource-after-patch"></a>Prostředek po opravě

Toto je prostředek po použití předchozího dokumentu opravy JSON:

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

Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické: Pokud se některá operace v seznamu nezdařila, nepoužije se žádná operace v seznamu.

## <a name="path-syntax"></a>Syntaxe cesty

Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi. Například `"/address/zipCode"`.

Indexy založené na nule slouží k určení prvků pole. První prvek `addresses` pole by byl `/addresses/0`. Chcete-li `add` na konec pole, použijte spojovník (-) místo čísla indexu: `/addresses/-`.

### <a name="operations"></a>Operace

V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):

|Operace  | Poznámky |
|-----------|--------------------------------|
| `add`     | Přidejte vlastnost nebo prvek pole. Pro existující vlastnost: nastavte hodnotu.|
| `remove`  | Odebere vlastnost nebo prvek pole. |
| `replace` | Stejné jako `remove` následované `add` na stejném místě. |
| `move`    | Stejné jako `remove` ze zdroje, za nímž následuje `add` k cíli pomocí hodnoty ze zdroje. |
| `copy`    | Stejné jako `add` k cíli pomocí hodnoty ze zdroje. |
| `test`    | Vrátí stavový kód úspěšného zpracování, pokud je hodnota na `path` = poskytnutá `value`.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch v ASP.NET Core

Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .

## <a name="action-method-code"></a>Kód metody akce

V řadiči rozhraní API metoda Action pro opravu JSON:

* Je opatřen s použitím atributu `HttpPatch`.
* Přijímá `JsonPatchDocument<T>`, obvykle s `[FromBody]`.
* Zavolá `ApplyTo` v dokumentu opravy, aby se změny projevily.

Tady je příklad:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Tento kód z ukázkové aplikace funguje s následujícím modelem `Customer`.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Ukázková akce metody:

* Vytvoří `Customer`.
* Aplikuje opravu.
* Vrátí výsledek v těle odpovědi.

 V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.

### <a name="model-state"></a>Stav modelu

Předchozí příklad metody volá přetížení `ApplyTo`, které jako jeden z jeho parametrů přijímá stav modelu. Pomocí této možnosti můžete získat chybové zprávy v odpovědích. Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro operaci `test`:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dynamické objekty

Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operace přidání

* Pokud `path` odkazuje na prvek pole: vloží nový prvek před první zadaný pomocí `path`.
* Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.
* Pokud `path` odkazuje na neexistující umístění:
  * Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.
  * Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.

Následující ukázkový dokument opravy nastaví hodnotu `CustomerName` a přidá objekt `Order` na konec pole `Orders`.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operace Remove

* Pokud `path` odkazuje na prvek pole: Odebere prvek.
* Pokud `path` odkazuje na vlastnost:
  * Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.
  * Pokud je prostředek pro opravu statickým objektem:
    * Pokud je vlastnost Nullable: nastaví ji na null.
    * Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>`.

Následující ukázková sada dokumentů má `CustomerName` na hodnotu null a odstraní `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operace Replace

Tato operace je funkčně stejná jako `remove` následované `add`.

Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]`novým objektem `Order`.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operace přesunutí

* Pokud `path` odkazuje na prvek pole: zkopíruje `from` elementu do umístění `path` elementu a pak spustí operaci `remove` na `from` elementu.
* Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu vlastnosti `from` na vlastnost `path` a potom spustí operaci `remove` na vlastnosti `from`.
* Pokud `path` odkazuje na neexistující vlastnost:
  * Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.
  * Pokud je prostředek k opravě dynamický objekt: zkopíruje `from` vlastnost do umístění určeného parametrem `path`a poté spustí operaci `remove` pro vlastnost `from`.

Následující ukázkový dokument opravy:

* Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName`.
* Nastaví `Orders[0].OrderName` na hodnotu null.
* Přesune `Orders[1]` do `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operace kopírování

Tato operace je funkčně stejná jako operace `move`, aniž by to mělo poslední `remove` krok.

Následující ukázkový dokument opravy:

* Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName`.
* Vloží kopii `Orders[1]` před `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operace testu

Pokud hodnota v umístění označeném `path` se liší od hodnoty zadané v `value`, požadavek se nezdařil. V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.

Operace `test` se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.

Následující ukázkový dokument opravy nemá žádný vliv, pokud je počáteční hodnota `CustomerName` "Jan", protože test se nezdařil:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Získat kód

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Stažení](xref:index#how-to-download-a-sample)).

Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:

* Adresa URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP: `PATCH`
* Záhlaví: `Content-Type: application/json-patch+json`
* Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [IETF RFC 5789 – specifikace metody opravy](https://tools.ietf.org/html/rfc5789)
* [Specifikace opravy JSON pro IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě](https://tools.ietf.org/html/rfc6901)
* [Dokumentace k opravě JSON](https://jsonpatch.com/). Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.
* [ASP.NET Core zdrojový kód opravy JSON](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.

## <a name="patch-http-request-method"></a>Oprava metody požadavku HTTP

Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku. Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.

## <a name="json-patch"></a>Oprava JSON

[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku. Dokument opravy JSON má pole *operací*. Každá operace identifikuje konkrétní typ změny, jako je například přidání prvku pole nebo nahrazení hodnoty vlastnosti.

Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.

### <a name="resource-example"></a>Příklad prostředku

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Příklad opravy JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

V předchozím kódu JSON:

* Vlastnost `op` označuje typ operace.
* Vlastnost `path` určuje prvek, který se má aktualizovat.
* Vlastnost `value` poskytuje novou hodnotu.

### <a name="resource-after-patch"></a>Prostředek po opravě

Toto je prostředek po použití předchozího dokumentu opravy JSON:

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

Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické: Pokud se některá operace v seznamu nezdařila, nepoužije se žádná operace v seznamu.

## <a name="path-syntax"></a>Syntaxe cesty

Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi. Například `"/address/zipCode"`.

Indexy založené na nule slouží k určení prvků pole. První prvek `addresses` pole by byl `/addresses/0`. Chcete-li `add` na konec pole, použijte spojovník (-) místo čísla indexu: `/addresses/-`.

### <a name="operations"></a>Operace

V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):

|Operace  | Poznámky |
|-----------|--------------------------------|
| `add`     | Přidejte vlastnost nebo prvek pole. Pro existující vlastnost: nastavte hodnotu.|
| `remove`  | Odebere vlastnost nebo prvek pole. |
| `replace` | Stejné jako `remove` následované `add` na stejném místě. |
| `move`    | Stejné jako `remove` ze zdroje, za nímž následuje `add` k cíli pomocí hodnoty ze zdroje. |
| `copy`    | Stejné jako `add` k cíli pomocí hodnoty ze zdroje. |
| `test`    | Vrátí stavový kód úspěšného zpracování, pokud je hodnota na `path` = poskytnutá `value`.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch v ASP.NET Core

Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) . Balíček obsahuje [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) Metapackage.

## <a name="action-method-code"></a>Kód metody akce

V řadiči rozhraní API metoda Action pro opravu JSON:

* Je opatřen s použitím atributu `HttpPatch`.
* Přijímá `JsonPatchDocument<T>`, obvykle s `[FromBody]`.
* Zavolá `ApplyTo` v dokumentu opravy, aby se změny projevily.

Tady je příklad:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Tento kód z ukázkové aplikace funguje s následujícím modelem `Customer`.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Ukázková akce metody:

* Vytvoří `Customer`.
* Aplikuje opravu.
* Vrátí výsledek v těle odpovědi.

 V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.

### <a name="model-state"></a>Stav modelu

Předchozí příklad metody volá přetížení `ApplyTo`, které jako jeden z jeho parametrů přijímá stav modelu. Pomocí této možnosti můžete získat chybové zprávy v odpovědích. Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro operaci `test`:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dynamické objekty

Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operace přidání

* Pokud `path` odkazuje na prvek pole: vloží nový prvek před první zadaný pomocí `path`.
* Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.
* Pokud `path` odkazuje na neexistující umístění:
  * Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.
  * Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.

Následující ukázkový dokument opravy nastaví hodnotu `CustomerName` a přidá objekt `Order` na konec pole `Orders`.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operace Remove

* Pokud `path` odkazuje na prvek pole: Odebere prvek.
* Pokud `path` odkazuje na vlastnost:
  * Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.
  * Pokud je prostředek pro opravu statickým objektem:
    * Pokud je vlastnost Nullable: nastaví ji na null.
    * Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>`.

Následující ukázková sada dokumentů má `CustomerName` na hodnotu null a odstraní `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operace Replace

Tato operace je funkčně stejná jako `remove` následované `add`.

Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]`novým objektem `Order`.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operace přesunutí

* Pokud `path` odkazuje na prvek pole: zkopíruje `from` elementu do umístění `path` elementu a pak spustí operaci `remove` na `from` elementu.
* Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu vlastnosti `from` na vlastnost `path` a potom spustí operaci `remove` na vlastnosti `from`.
* Pokud `path` odkazuje na neexistující vlastnost:
  * Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.
  * Pokud je prostředek k opravě dynamický objekt: zkopíruje `from` vlastnost do umístění určeného parametrem `path`a poté spustí operaci `remove` pro vlastnost `from`.

Následující ukázkový dokument opravy:

* Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName`.
* Nastaví `Orders[0].OrderName` na hodnotu null.
* Přesune `Orders[1]` do `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operace kopírování

Tato operace je funkčně stejná jako operace `move`, aniž by to mělo poslední `remove` krok.

Následující ukázkový dokument opravy:

* Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName`.
* Vloží kopii `Orders[1]` před `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operace testu

Pokud hodnota v umístění označeném `path` se liší od hodnoty zadané v `value`, požadavek se nezdařil. V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.

Operace `test` se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.

Následující ukázkový dokument opravy nemá žádný vliv, pokud je počáteční hodnota `CustomerName` "Jan", protože test se nezdařil:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Získat kód

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Stažení](xref:index#how-to-download-a-sample)).

Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:

* Adresa URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP: `PATCH`
* Záhlaví: `Content-Type: application/json-patch+json`
* Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [IETF RFC 5789 – specifikace metody opravy](https://tools.ietf.org/html/rfc5789)
* [Specifikace opravy JSON pro IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě](https://tools.ietf.org/html/rfc6901)
* [Dokumentace k opravě JSON](https://jsonpatch.com/). Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.
* [ASP.NET Core zdrojový kód opravy JSON](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
