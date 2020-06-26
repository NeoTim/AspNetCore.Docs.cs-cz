---
title: JsonPatch v ASP.NET Core Web API
author: rick-anderson
description: Naučte se zpracovávat požadavky na opravy JSON ve ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/jsonpatch
ms.openlocfilehash: 08ae366859c4466e6957592f78dda813d6670bb4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405026"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch v ASP.NET Core Web API

[Dykstra](https://github.com/tdykstra) a [Kirka Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.

## <a name="package-installation"></a>Instalace balíčku

Pokud chcete ve své aplikaci povolit podporu oprav JSON, proveďte následující kroky:

1. Nainstalujte [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) balíček NuGet.
1. Aktualizujte metodu projektu `Startup.ConfigureServices` pro volání <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> . Například:

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson`je kompatibilní s metodami registrace služby MVC:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>Oprava JSON, AddNewtonsoftJson a System.Text.Js

`AddNewtonsoftJson`nahradí `System.Text.Json` vstupní a výstupní formátovací moduly používané pro formátování **veškerého** obsahu JSON. Chcete-li přidat podporu pro opravu JSON pomocí příkazu `Newtonsoft.Json` , zatímco ostatní formátovací moduly zůstaly beze změny, aktualizujte `Startup.ConfigureServices` metodu projektu následujícím způsobem:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Předchozí kód vyžaduje `Microsoft.AspNetCore.Mvc.NewtonsoftJson` balíček a následující `using` příkazy:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>Oprava metody požadavku HTTP

Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku. Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.

## <a name="json-patch"></a>Oprava JSON

[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku. Dokument opravy JSON má pole *operací*. Každá operace identifikuje konkrétní typ změny. Příklady takových změn zahrnují přidání prvku pole nebo nahrazení hodnoty vlastnosti.

Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.

### <a name="resource-example"></a>Příklad prostředku

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Příklad opravy JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

V předchozím kódu JSON:

* `op`Vlastnost určuje typ operace.
* `path`Vlastnost určuje prvek, který se má aktualizovat.
* `value`Vlastnost poskytuje novou hodnotu.

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

Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické. Pokud se některá operace v seznamu nezdařila, nebude použita žádná operace v seznamu.

## <a name="path-syntax"></a>Syntaxe cesty

Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi. Například, `"/address/zipCode"`.

Indexy založené na nule slouží k určení prvků pole. První prvek `addresses` pole by byl na `/addresses/0` . Na `add` konec pole použijte spojovník ( `-` ) místo čísla indexu: `/addresses/-` .

### <a name="operations"></a>Operace

V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):

|Operace  | Poznámky |
|-----------|--------------------------------|
| `add`     | Přidejte vlastnost nebo prvek pole. Pro existující vlastnost: nastavte hodnotu.|
| `remove`  | Odebere vlastnost nebo prvek pole. |
| `replace` | Stejné jako `remove` následováno ve `add` stejném umístění. |
| `move`    | Stejné jako `remove` u zdroje, po kterém následuje `add` k cíli, pomocí hodnoty ze zdroje. |
| `copy`    | Stejné jako `add` cíl pomocí hodnoty ze zdroje. |
| `test`    | Vrátí stavový kód úspěšného zpracování, pokud je zadána hodnota at `path` `value` .|

## <a name="json-patch-in-aspnet-core"></a>Oprava JSON v ASP.NET Core

Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft.AspNetCore.Jspropatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .

## <a name="action-method-code"></a>Kód metody akce

V řadiči rozhraní API metoda Action pro opravu JSON:

* Je označen `HttpPatch` atributem.
* Akceptuje `JsonPatchDocument<T>` , obvykle s `[FromBody]` .
* `ApplyTo`Změny se projeví v dokumentu opravy.

Tady je příklad:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Tento kód z ukázkové aplikace funguje s následujícím `Customer` modelem:

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Ukázková akce metody:

* Vytvoří `Customer` .
* Aplikuje opravu.
* Vrátí výsledek v těle odpovědi.

V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.

### <a name="model-state"></a>Stav modelu

Předchozí příklad metody volá přetížení `ApplyTo` , které přijímá stav modelu jako jeden z jeho parametrů. Pomocí této možnosti můžete získat chybové zprávy v odpovědích. Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro `test` operaci:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dynamické objekty

Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operace přidání

* `path`Odkazuje-li na prvek pole: vloží nový prvek před první, který je určen parametrem `path` .
* Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.
* Pokud `path` odkazuje na neexistující umístění:
  * Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.
  * Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.

Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a přidá `Order` objekt na konec `Orders` pole.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operace Remove

* Pokud `path` odkazuje na element pole: Odebere prvek.
* Pokud `path` odkazuje na vlastnost:
  * Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.
  * Pokud je prostředek pro opravu statickým objektem:
    * Pokud je vlastnost Nullable: nastaví ji na null.
    * Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>` .

Následující ukázkový dokument opravy se nastaví `CustomerName` na hodnotu null a odstraní `Orders[0]` :

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operace Replace

Tato operace je funkčně stejná jako `remove` následováno `add` .

Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]` novým `Order` objektem:

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operace přesunutí

* Pokud `path` odkazuje na element pole: zkopíruje `from` element do umístění `path` elementu a potom spustí `remove` operaci na `from` elementu.
* Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu `from` vlastnosti na `path` vlastnost a potom spustí `remove` operaci pro `from` vlastnost.
* Pokud `path` odkazuje na neexistující vlastnost:
  * Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.
  * Pokud je prostředek pro opravu dynamický objekt: zkopíruje `from` vlastnost do umístění označeného `path` a potom spustí `remove` operaci pro `from` vlastnost.

Následující ukázkový dokument opravy:

* Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .
* Nastaví `Orders[0].OrderName` na hodnotu null.
* Přesune `Orders[1]` se na dřív `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operace kopírování

Tato operace je funkčně stejná jako `move` operace bez posledního `remove` kroku.

Následující ukázkový dokument opravy:

* Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .
* Vloží kopii `Orders[1]` před `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operace testu

Pokud hodnota v umístění, která je uvedena v `path` , se liší od hodnoty uvedené v `value` , požadavek se nezdařil. V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.

Tato `test` operace se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.

Následující ukázkový dokument opravy nemá žádný vliv, pokud je původní hodnota `CustomerName` "Jan", protože test se nezdařil:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Získání kódu

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples). ([Stažení](xref:index#how-to-download-a-sample)).

Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:

* Adresa URL`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP:`PATCH`
* Hlaviček`Content-Type: application/json-patch+json`
* Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .

## <a name="additional-resources"></a>Další zdroje

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

* `op`Vlastnost určuje typ operace.
* `path`Vlastnost určuje prvek, který se má aktualizovat.
* `value`Vlastnost poskytuje novou hodnotu.

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

Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi. Například, `"/address/zipCode"`.

Indexy založené na nule slouží k určení prvků pole. První prvek `addresses` pole by byl na `/addresses/0` . Na `add` konec pole, použijte spojovník (-) místo čísla indexu: `/addresses/-` .

### <a name="operations"></a>Operace

V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):

|Operace  | Poznámky |
|-----------|--------------------------------|
| `add`     | Přidejte vlastnost nebo prvek pole. Pro existující vlastnost: nastavte hodnotu.|
| `remove`  | Odebere vlastnost nebo prvek pole. |
| `replace` | Stejné jako `remove` následováno ve `add` stejném umístění. |
| `move`    | Stejné jako `remove` u zdroje, po kterém následuje `add` k cíli, pomocí hodnoty ze zdroje. |
| `copy`    | Stejné jako `add` cíl pomocí hodnoty ze zdroje. |
| `test`    | Vrátí stavový kód úspěšného zpracování, pokud je zadána hodnota at `path` `value` .|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch v ASP.NET Core

Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft.AspNetCore.Jspropatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) . Balíček obsahuje [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) Metapackage.

## <a name="action-method-code"></a>Kód metody akce

V řadiči rozhraní API metoda Action pro opravu JSON:

* Je označen `HttpPatch` atributem.
* Akceptuje `JsonPatchDocument<T>` , obvykle s `[FromBody]` .
* `ApplyTo`Změny se projeví v dokumentu opravy.

Tady je příklad:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Tento kód z ukázkové aplikace funguje s následujícím `Customer` modelem.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Ukázková akce metody:

* Vytvoří `Customer` .
* Aplikuje opravu.
* Vrátí výsledek v těle odpovědi.

 V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.

### <a name="model-state"></a>Stav modelu

Předchozí příklad metody volá přetížení `ApplyTo` , které přijímá stav modelu jako jeden z jeho parametrů. Pomocí této možnosti můžete získat chybové zprávy v odpovědích. Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro `test` operaci:

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

* `path`Odkazuje-li na prvek pole: vloží nový prvek před první, který je určen parametrem `path` .
* Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.
* Pokud `path` odkazuje na neexistující umístění:
  * Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.
  * Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.

Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a přidá `Order` objekt na konec `Orders` pole.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operace Remove

* Pokud `path` odkazuje na element pole: Odebere prvek.
* Pokud `path` odkazuje na vlastnost:
  * Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.
  * Pokud je prostředek pro opravu statickým objektem:
    * Pokud je vlastnost Nullable: nastaví ji na null.
    * Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>` .

Následující ukázkový dokument opravy se nastaví `CustomerName` na hodnotu null a odstraní `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operace Replace

Tato operace je funkčně stejná jako `remove` následováno `add` .

Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]` novým `Order` objektem.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operace přesunutí

* Pokud `path` odkazuje na element pole: zkopíruje `from` element do umístění `path` elementu a potom spustí `remove` operaci na `from` elementu.
* Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu `from` vlastnosti na `path` vlastnost a potom spustí `remove` operaci pro `from` vlastnost.
* Pokud `path` odkazuje na neexistující vlastnost:
  * Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.
  * Pokud je prostředek pro opravu dynamický objekt: zkopíruje `from` vlastnost do umístění označeného `path` a potom spustí `remove` operaci pro `from` vlastnost.

Následující ukázkový dokument opravy:

* Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .
* Nastaví `Orders[0].OrderName` na hodnotu null.
* Přesune `Orders[1]` se na dřív `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operace kopírování

Tato operace je funkčně stejná jako `move` operace bez posledního `remove` kroku.

Následující ukázkový dokument opravy:

* Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .
* Vloží kopii `Orders[1]` před `Orders[0]` .

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Operace testu

Pokud hodnota v umístění, která je uvedena v `path` , se liší od hodnoty uvedené v `value` , požadavek se nezdařil. V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.

Tato `test` operace se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.

Následující ukázkový dokument opravy nemá žádný vliv, pokud je původní hodnota `CustomerName` "Jan", protože test se nezdařil:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Získání kódu

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Stažení](xref:index#how-to-download-a-sample)).

Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:

* Adresa URL`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP:`PATCH`
* Hlaviček`Content-Type: application/json-patch+json`
* Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .

## <a name="additional-resources"></a>Další zdroje

* [IETF RFC 5789 – specifikace metody opravy](https://tools.ietf.org/html/rfc5789)
* [Specifikace opravy JSON pro IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě](https://tools.ietf.org/html/rfc6901)
* [Dokumentace k opravě JSON](https://jsonpatch.com/). Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.
* [ASP.NET Core zdrojový kód opravy JSON](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
