---
title: JsonPatch v ASP.NET základní webové rozhraní API
author: rick-anderson
description: Zjistěte, jak zpracovat požadavky na opravu JSON v ASP.NET základníwebové rozhraní API.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
uid: web-api/jsonpatch
ms.openlocfilehash: be4115e870dac818aeb6b1e65ddfb21e89d9cf25
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625871"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch v ASP.NET základní webové rozhraní API

Tom [Dykstra](https://github.com/tdykstra) a [Kirk Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Tento článek vysvětluje, jak zpracovat požadavky na opravu JSON v ASP.NET základní webové rozhraní API.

## <a name="package-installation"></a>Instalace balíčku

Chcete-li ve své aplikaci povolit podporu aplikace JSON Patch, proveďte následující kroky:

1. Nainstalujte balíček [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet.
1. Aktualizujte `Startup.ConfigureServices` metodu projektu <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>tak, aby volala . Příklad:

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson`je kompatibilní s metodami registrace služby MVC:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>JSON Patch, AddNewtonsoftJson a System.Text.Json

`AddNewtonsoftJson`nahradí `System.Text.Json`vstupní a výstupní promorávací věci používané pro formátování **veškerého** obsahu JSON. Chcete-li přidat podporu pro `Newtonsoft.Json`JSON Patch pomocí , zatímco ostatní `Startup.ConfigureServices` formatters beze změny, aktualizujte metodu projektu takto:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Předchozí kód vyžaduje `Microsoft.AspNetCore.Mvc.NewtonsoftJson` balíček a `using` následující příkazy:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>Metoda požadavku PATCH HTTP

Metody PUT a [PATCH](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku. Rozdíl mezi nimi je, že PUT nahradí celý prostředek, zatímco PATCH určuje pouze změny.

## <a name="json-patch"></a>JSON Patch

[JSON Patch](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které mají být použity pro prostředek. Dokument Záplaty JSON má řadu *operací*. Každá operace identifikuje určitý typ změny. Příklady takových změn zahrnují přidání prvku pole nebo nahrazení hodnoty vlastnosti.

Například následující dokumenty JSON představují prostředek, dokument Záplata JSON pro prostředek a výsledek použití operací Opravy.

### <a name="resource-example"></a>Příklad zdroje

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Příklad opravy JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

V předchozím JSON:

* Vlastnost `op` označuje typ operace.
* Vlastnost `path` označuje prvek aktualizovat.
* Vlastnost `value` poskytuje novou hodnotu.

### <a name="resource-after-patch"></a>Zdroj po opravě

Zde je zdroj po použití předchozího dokumentu Opravy JSON:

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

Změny provedené použitím dokumentu Záplaty JSON na prostředek jsou atomické. Pokud se některá operace v seznamu nezdaří, nebude použita žádná operace v seznamu.

## <a name="path-syntax"></a>Syntaxe cesty

Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi. Například, `"/address/zipCode"`.

Indexy založené na nule se používají k určení prvků pole. První prvek `addresses` pole by se `/addresses/0`na . Na `add` konec pole použijte místo čísla`-` `/addresses/-`indexu pomlčku ( ).

### <a name="operations"></a>Operace

V následující tabulce jsou uvedeny podporované operace definované ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):

|Operace  | Poznámky |
|-----------|--------------------------------|
| `add`     | Přidejte vlastnost nebo prvek pole. Pro existující vlastnost: set value.|
| `remove`  | Odeberte vlastnost nebo prvek pole. |
| `replace` | Stejné `remove` jako `add` na stejném místě. |
| `move`    | Stejné `remove` jako ze `add` zdroje následuje cíl pomocí hodnoty ze zdroje. |
| `copy`    | Stejné `add` jako k cíli pomocí hodnoty ze zdroje. |
| `test`    | Vrátit kód stavu úspěchu, pokud hodnota na `path` = za předpokladu `value`.|

## <a name="json-patch-in-aspnet-core"></a>JSON Patch v ASP.NET jádru

ASP.NET Core implementace JSON Patch je k dispozici v balíčku [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet.

## <a name="action-method-code"></a>Kód metody akce

V řadiči rozhraní API metoda akce pro opravu JSON:

* Je anotován `HttpPatch` s atributem.
* Přijímá `JsonPatchDocument<T>`, obvykle s `[FromBody]`.
* Zavolá `ApplyTo` dokument opravy použít změny.

Tady je příklad:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Tento kód z ukázkové aplikace `Customer` funguje s následujícím modelem:

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Ukázková metoda akce:

* Vytvoří `Customer`.
* Aplikuje náplast.
* Vrátí výsledek v těle odpovědi.

V reálné aplikaci by kód načetl data z úložiště, jako je například databáze, a po použití opravy databázi aktualizoval.

### <a name="model-state"></a>Stav modelu

Příklad předchozí metody akce volá `ApplyTo` přetížení, které trvá stav modelu jako jeden z jeho parametrů. Pomocí této možnosti můžete získat chybové zprávy v odpovědích. Následující příklad ukazuje tělo 400 chybný požadavek `test` odpověď pro operaci:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dynamické objekty

Následující příklad metody akce ukazuje, jak aplikovat opravu na dynamický objekt:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operace přidání

* Pokud `path` odkazuje na prvek pole: vloží nový prvek `path`před ten, který je určen .
* Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.
* Pokud `path` odkazuje na neexistující umístění:
  * Pokud prostředek k opravě je dynamický objekt: přidá vlastnost.
  * Pokud prostředek k opravě je statický objekt: požadavek se nezdaří.

Následující ukázkový dokument opravy `CustomerName` nastaví `Order` hodnotu a přidá `Orders` objekt na konec pole.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operace odebrání

* Pokud `path` odkazuje na prvek pole: odebere prvek.
* Pokud `path` odkazuje na vlastnost:
  * Pokud prostředek k opravě je dynamický objekt: odebere vlastnost.
  * Pokud je prostředek k opravě statický objekt:
    * Pokud je vlastnost nullable: nastaví ji na hodnotu null.
    * Pokud je vlastnost nenulovatelná, `default<T>`nastaví ji na .

Následující ukázkový dokument `CustomerName` opravy se `Orders[0]`nastaví na hodnotu null a odstraní :

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operace výměny

Tato operace je funkčně `remove` stejná `add`jako následuje .

Následující ukázkový dokument opravy `CustomerName` nastaví `Orders[0]`hodnotu `Order` a nahradí novým objektem:

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operace přesunu

* Pokud `path` odkazuje na prvek `from` pole: zkopíruje prvek do umístění `path` prvku, pak spustí `remove` operaci na `from` prvek.
* Pokud `path` odkazuje na vlastnost: `from` zkopíruje `path` hodnotu vlastnosti vlastnosti, spustí `remove` operaci vlastnosti. `from`
* Pokud `path` odkazuje na neexistující vlastnost:
  * Pokud prostředek k opravě je statický objekt: požadavek se nezdaří.
  * Pokud prostředek k opravě je `from` dynamický objekt: zkopíruje vlastnost do umístění `path`označeného , pak spustí `remove` operaci na `from` vlastnost.

Následující ukázkový dokument o opravě:

* Zkopíruje hodnotu `CustomerName`do . `Orders[0].OrderName`
* Nastaví `Orders[0].OrderName` hodnotu null.
* Přesune `Orders[1]` se `Orders[0]`do before .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operace kopírování

Tato operace je funkčně `move` stejná `remove` jako operace bez posledního kroku.

Následující ukázkový dokument o opravě:

* Zkopíruje hodnotu `CustomerName`do . `Orders[0].OrderName`
* Vloží kopii `Orders[1]` před `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Zkušební operace

Pokud se hodnota v umístění `path` označeném v písmenu `value`a) liší od hodnoty uvedené v písmenu , požadavek se nezdaří. V takovém případě se nezdaří celý požadavek PATCH i v případě, že všechny ostatní operace v dokumentu opravy by jinak úspěšné.

Operace `test` se běžně používá k zabránění aktualizaci, když dojde ke konfliktu souběžnosti.

Následující ukázkový dokument opravy nemá žádný `CustomerName` vliv, pokud počáteční hodnota je "Jan", protože test se nezdaří:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Získání kódu

[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples). ([Jak stáhnout](xref:index#how-to-download-a-sample)).

Chcete-li vzorek otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:

* Adresu url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP:`PATCH`
* Záhlaví:`Content-Type: application/json-patch+json`
* Tělo: Zkopírujte a vložte jeden z vzorků dokumentu opravy JSON ze složky projektu *JSON.*

## <a name="additional-resources"></a>Další zdroje

* [Specifikace metody IETF RFC 5789 PATCH](https://tools.ietf.org/html/rfc5789)
* [Specifikace opravy IETF RFC 6902 JSON](https://tools.ietf.org/html/rfc6902)
* [Specifikace formátu cesty záplaty IETF RFC 6901 JSON](https://tools.ietf.org/html/rfc6901)
* [Dokumentace k záplatě JSON](https://jsonpatch.com/). Obsahuje odkazy na prostředky pro vytváření dokumentů opravy JSON.
* [zdrojový kód opravy ASP.NET Jádra JSON](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek vysvětluje, jak zpracovat požadavky na opravu JSON v ASP.NET základní webové rozhraní API.

## <a name="patch-http-request-method"></a>Metoda požadavku PATCH HTTP

Metody PUT a [PATCH](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku. Rozdíl mezi nimi je, že PUT nahradí celý prostředek, zatímco PATCH určuje pouze změny.

## <a name="json-patch"></a>JSON Patch

[JSON Patch](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které mají být použity pro prostředek. Dokument Záplaty JSON má řadu *operací*. Každá operace identifikuje určitý typ změny, například přidat prvek pole nebo nahradit hodnotu vlastnosti.

Například následující dokumenty JSON představují prostředek, dokument opravy JSON pro prostředek a výsledek použití operací opravy.

### <a name="resource-example"></a>Příklad zdroje

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Příklad opravy JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

V předchozím JSON:

* Vlastnost `op` označuje typ operace.
* Vlastnost `path` označuje prvek aktualizovat.
* Vlastnost `value` poskytuje novou hodnotu.

### <a name="resource-after-patch"></a>Zdroj po opravě

Zde je zdroj po použití předchozího dokumentu Opravy JSON:

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

Změny provedené použitím dokumentu Záplaty JSON na prostředek jsou atomické: pokud se nezdaří jakákoli operace v seznamu, nebude použita žádná operace v seznamu.

## <a name="path-syntax"></a>Syntaxe cesty

Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi. Například, `"/address/zipCode"`.

Indexy založené na nule se používají k určení prvků pole. První prvek `addresses` pole by se `/addresses/0`na . Na `add` konec pole použijte pomlčku (-) místo čísla indexu: `/addresses/-`.

### <a name="operations"></a>Operace

V následující tabulce jsou uvedeny podporované operace definované ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):

|Operace  | Poznámky |
|-----------|--------------------------------|
| `add`     | Přidejte vlastnost nebo prvek pole. Pro existující vlastnost: set value.|
| `remove`  | Odeberte vlastnost nebo prvek pole. |
| `replace` | Stejné `remove` jako `add` na stejném místě. |
| `move`    | Stejné `remove` jako ze `add` zdroje následuje cíl pomocí hodnoty ze zdroje. |
| `copy`    | Stejné `add` jako k cíli pomocí hodnoty ze zdroje. |
| `test`    | Vrátit kód stavu úspěchu, pokud hodnota na `path` = za předpokladu `value`.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch v ASP.NET jádru

ASP.NET Core implementace JSON Patch je k dispozici v balíčku [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet. Balíček je součástí metabalíčku [Microsoft.AspnetCore.App.](xref:fundamentals/metapackage-app)

## <a name="action-method-code"></a>Kód metody akce

V řadiči rozhraní API metoda akce pro opravu JSON:

* Je anotován `HttpPatch` s atributem.
* Přijímá `JsonPatchDocument<T>`, obvykle s `[FromBody]`.
* Zavolá `ApplyTo` dokument opravy použít změny.

Tady je příklad:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Tento kód z ukázkové aplikace `Customer` pracuje s následujícím modelem.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Ukázková metoda akce:

* Vytvoří `Customer`.
* Aplikuje náplast.
* Vrátí výsledek v těle odpovědi.

 V reálné aplikaci by kód načetl data z úložiště, jako je například databáze, a po použití opravy databázi aktualizoval.

### <a name="model-state"></a>Stav modelu

Příklad předchozí metody akce volá `ApplyTo` přetížení, které trvá stav modelu jako jeden z jeho parametrů. Pomocí této možnosti můžete získat chybové zprávy v odpovědích. Následující příklad ukazuje tělo 400 chybný požadavek `test` odpověď pro operaci:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dynamické objekty

Následující příklad metody akce ukazuje, jak aplikovat opravu na dynamický objekt.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Operace přidání

* Pokud `path` odkazuje na prvek pole: vloží nový prvek `path`před ten, který je určen .
* Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.
* Pokud `path` odkazuje na neexistující umístění:
  * Pokud prostředek k opravě je dynamický objekt: přidá vlastnost.
  * Pokud prostředek k opravě je statický objekt: požadavek se nezdaří.

Následující ukázkový dokument opravy `CustomerName` nastaví `Order` hodnotu a přidá `Orders` objekt na konec pole.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Operace odebrání

* Pokud `path` odkazuje na prvek pole: odebere prvek.
* Pokud `path` odkazuje na vlastnost:
  * Pokud prostředek k opravě je dynamický objekt: odebere vlastnost.
  * Pokud je prostředek k opravě statický objekt:
    * Pokud je vlastnost nullable: nastaví ji na hodnotu null.
    * Pokud je vlastnost nenulovatelná, `default<T>`nastaví ji na .

Následující ukázkový dokument `CustomerName` opravy se `Orders[0]`nastaví na hodnotu null a odstraní .

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Operace výměny

Tato operace je funkčně `remove` stejná `add`jako následuje .

Následující ukázkový dokument opravy `CustomerName` nastaví `Orders[0]`hodnotu `Order` a nahradí novým objektem.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Operace přesunu

* Pokud `path` odkazuje na prvek `from` pole: zkopíruje prvek do umístění `path` prvku, pak spustí `remove` operaci na `from` prvek.
* Pokud `path` odkazuje na vlastnost: `from` zkopíruje `path` hodnotu vlastnosti vlastnosti, spustí `remove` operaci vlastnosti. `from`
* Pokud `path` odkazuje na neexistující vlastnost:
  * Pokud prostředek k opravě je statický objekt: požadavek se nezdaří.
  * Pokud prostředek k opravě je `from` dynamický objekt: zkopíruje vlastnost do umístění `path`označeného , pak spustí `remove` operaci na `from` vlastnost.

Následující ukázkový dokument o opravě:

* Zkopíruje hodnotu `CustomerName`do . `Orders[0].OrderName`
* Nastaví `Orders[0].OrderName` hodnotu null.
* Přesune `Orders[1]` se `Orders[0]`do before .

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Operace kopírování

Tato operace je funkčně `move` stejná `remove` jako operace bez posledního kroku.

Následující ukázkový dokument o opravě:

* Zkopíruje hodnotu `CustomerName`do . `Orders[0].OrderName`
* Vloží kopii `Orders[1]` před `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Zkušební operace

Pokud se hodnota v umístění `path` označeném v písmenu `value`a) liší od hodnoty uvedené v písmenu , požadavek se nezdaří. V takovém případě se nezdaří celý požadavek PATCH i v případě, že všechny ostatní operace v dokumentu opravy by jinak úspěšné.

Operace `test` se běžně používá k zabránění aktualizaci, když dojde ke konfliktu souběžnosti.

Následující ukázkový dokument opravy nemá žádný `CustomerName` vliv, pokud počáteční hodnota je "Jan", protože test se nezdaří:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Získání kódu

[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Jak stáhnout](xref:index#how-to-download-a-sample)).

Chcete-li vzorek otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:

* Adresu url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Metoda HTTP:`PATCH`
* Záhlaví:`Content-Type: application/json-patch+json`
* Tělo: Zkopírujte a vložte jeden z vzorků dokumentu opravy JSON ze složky projektu *JSON.*

## <a name="additional-resources"></a>Další zdroje

* [Specifikace metody IETF RFC 5789 PATCH](https://tools.ietf.org/html/rfc5789)
* [Specifikace opravy IETF RFC 6902 JSON](https://tools.ietf.org/html/rfc6902)
* [Specifikace formátu cesty záplaty IETF RFC 6901 JSON](https://tools.ietf.org/html/rfc6901)
* [Dokumentace k záplatě JSON](https://jsonpatch.com/). Obsahuje odkazy na prostředky pro vytváření dokumentů opravy JSON.
* [zdrojový kód opravy ASP.NET Jádra JSON](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
