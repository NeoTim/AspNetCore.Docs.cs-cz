---
title: Ukládání odpovědí do mezipaměti v ASP.NET Core
author: rick-anderson
description: Další informace o použití odpověď do mezipaměti pro nižší požadavky na šířku pásma a zvýšit výkon aplikace ASP.NET Core.
ms.author: riande
ms.date: 01/07/2018
uid: performance/caching/response
ms.openlocfilehash: 5fbcaddff6e53d01a19ba8a7455c719feb614326
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098945"
---
# <a name="response-caching-in-aspnet-core"></a>Ukládání odpovědí do mezipaměti v ASP.NET Core

Podle [Jan Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), a [Luke Latham](https://github.com/guardrex)

> [!NOTE]
> Ukládání odpovědí do mezipaměti v Razor Pages je k dispozici v ASP.NET Core 2.1 nebo novější.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukládání odpovědí do mezipaměti snižuje počet požadavků, které odešle klient nebo server proxy webový server. Ukládání odpovědí do mezipaměti také snižuje množství práce provádí webového serveru pro generování odpovědi. Ukládání odpovědí do mezipaměti se řídí hlavičky, které určují, jak chcete klienta, serveru proxy a middlewarem do mezipaměti odpovědi.

[ResponseCache atribut](#responsecache-attribute) účastní nastavení odpověď záhlaví, kteří klienti mohou případném dalším sdílení dodržovat při ukládání odpovědí do mezipaměti. [Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) je možné do mezipaměti odpovědi na serveru. Middleware lze použít `ResponseCache` atribut vlastnosti a ovlivnit chování ukládání do mezipaměti na straně serveru.

## <a name="http-based-response-caching"></a>Ukládání do mezipaměti založené na protokolu HTTP odpovědi

[Specifikace HTTP 1.1 ukládání do mezipaměti](https://tools.ietf.org/html/rfc7234) popisuje chování mezipaměti Internet. Primární záhlaví HTTP používá pro ukládání do mezipaměti je [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), který se používá k určení mezipaměti *direktivy*. Direktivy řízení chování ukládání do mezipaměti podle požadavků dostanou od klientů na servery a jako odpověď dostanou ze serverů zpět klientům. Požadavky a odpovědi procházet proxy servery a proxy servery musí také odpovídají specifikaci HTTP 1.1 ukládání do mezipaměti.

Běžné `Cache-Control` direktivy jsou uvedeny v následující tabulce.

| – Direktiva                                                       | Akce |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Mezipaměť může ukládat odpovědi. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Odpověď nesmí být uloženy ve sdílené mezipaměti. Soukromé mezipaměti může ukládat a opakovaně používat odpovědi. |
| [Maximální stáří](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Klient nebude přijímat odpovědi, jejichž stáří je větší než zadaný počet sekund. Příklady: `max-age=60` (60 sekund), `max-age=2592000` (1 měsíc) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **U požadavků**: Mezipaměť nesmí používat uložené odpovědi, abyste vyhověli žádosti. Poznámka: Zdrojový server znovu generuje odpovědi pro klienta a middleware aktualizuje odpověď na uložené v mezipaměti.<br><br>**V odpovědi**: Odpověď nesmí se používat pro další požadavek bez ověřování na původním serveru. |
| [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **U požadavků**: Mezipaměť nesmí uložit žádost.<br><br>**V odpovědi**: Mezipaměť nesmí uložit libovolnou část odpovědi. |

V následující tabulce jsou uvedeny další hlavičky mezipaměti, které hrají roli při ukládání do mezipaměti.

| Záhlaví                                                     | Funkce |
| ---------------------------------------------------------- | -------- |
| [Stáří](https://tools.ietf.org/html/rfc7234#section-5.1)     | Odhad množství času v sekundách, protože odpověď byla vygenerována nebo úspěšně ověřen na původním serveru. |
| [Vypršení platnosti](https://tools.ietf.org/html/rfc7234#section-5.3) | Datum a čas, po jejímž uplynutí se považuje za odpověď zastaralá. |
| [Direktiva pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Pro zpětnou kompatibilitu s HTTP verze 1.0 ukládá do mezipaměti pro nastavení existuje `no-cache` chování. Pokud `Cache-Control` záhlaví je k dispozici, `Pragma` záhlaví se ignoruje. |
| [se liší](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Určuje, že odpověď uložená v mezipaměti nesmí být odeslána, pokud všechny nástroje `Vary` záhlaví pole shodují v původní požadavek odpověď uložená v mezipaměti a nový požadavek. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Ukládání do mezipaměti respektuje založené na protokolu HTTP požadavku direktivy Cache-Control

[Specifikace HTTP 1.1 ukládání do mezipaměti pro hlavičku Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) vyžaduje mezipaměti případném dalším sdílení dodržovat platné `Cache-Control` záhlaví odesílaném klientem. Klienta můžete vytvářet požadavky s `no-cache` hodnota hlavičky a vynutit server vygenerovat novou odpověď pro každý požadavek.

Vždy dodržením klienta `Cache-Control` hlavičky žádosti dává smysl, pokud považujete za cíl protokolu HTTP, ukládání do mezipaměti. V části specifikaci oficiální ukládání do mezipaměti smyslem je snížit latenci a sítě režii splňující požadavky napříč sítí klientů, proxy servery a servery. Není nutně způsob, jak řídit zatížení na původním serveru.

Neexistuje žádné vývojáři řídit tohoto chování ukládání do mezipaměti při použití [Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) vzhledem k tomu, že dodržuje middleware official je přínosné pro ukládání do mezipaměti specifikace. [Plánované vylepšení middleware](https://github.com/aspnet/AspNetCore/issues/2612) jsou příležitosti pro konfiguraci middlewaru ignorovat požadavku `Cache-Control` hlavičku při rozhodování o tom, která bude sloužit odpověď uložená v mezipaměti. Plánované vylepšení nabízejí možnost lepší řízení zatížení serveru.

## <a name="other-caching-technology-in-aspnet-core"></a>Další technologie ukládání do mezipaměti v ASP.NET Core

### <a name="in-memory-caching"></a>Ukládání do mezipaměti v paměti

Ukládání do mezipaměti v paměti používá k ukládání dat uložených v mezipaměti paměti serveru. Tento typ ukládání do mezipaměti je vhodný pro jeden nebo více servery pomocí *rychlé relace*. Rychlé relace znamená, že požadavky od klienta jsou vždy směrovány na stejný server ke zpracování.

Další informace najdete v tématu [ukládat do mezipaměti v paměti](xref:performance/caching/memory).

### <a name="distributed-cache"></a>Distribuované mezipaměti

K ukládání dat v paměti, když je aplikace hostovaná v cloudu nebo serveru farmy pomocí distribuované mezipaměti. Mezipaměť je sdílen mezi servery, které zpracovávají požadavky. Klient může odeslat žádost, kterou provádí služba jakýkoli server ve skupině, pokud je k dispozici data uložená v mezipaměti klienta. ASP.NET Core nabízí systému SQL Server a mezipamětí Redis distribuovat.

Další informace naleznete v tématu <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Pomocné rutiny značky do mezipaměti

Vám může ukládat do mezipaměti obsah ze zobrazení MVC nebo stránky Razor s pomocné rutiny značky mezipaměti. Pomocná rutina značek mezipaměti používá k ukládání dat do mezipaměti v paměti.

Další informace najdete v tématu [pomocná rutina značek v mezipaměti v ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="distributed-cache-tag-helper"></a>Pomocná rutina značek v distribuované mezipaměti

Obsah ze zobrazení MVC nebo stránky Razor v distribuovaných cloudových nebo webových farem můžete mezipaměti pomocí distribuované pomocná rutina značek mezipaměti. Distribuované mezipaměti pomocná rutina značek v používá k ukládání dat serveru SQL Server nebo Redis.

Další informace najdete v tématu [pomocné rutiny značky distribuované mezipaměti](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).

## <a name="responsecache-attribute"></a>Atribut ResponseCache

[ResponseCacheAttribute](/dotnet/api/Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) Určuje parametry, které jsou nezbytné pro nastavení příslušné záhlaví v ukládání odpovědí do mezipaměti.

> [!WARNING]
> Zakáže ukládání do mezipaměti pro obsah, který obsahuje informace o ověření klienti. Ukládání do mezipaměti musí být povolené pouze pro obsah, který se nezmění na základě identity uživatele nebo určuje, zda je uživatel přihlášený.

[VaryByQueryKeys](/dotnet/api/microsoft.aspnetcore.mvc.responsecacheattribute.varybyquerykeys) uložené odpovědi se liší podle hodnoty daný seznam klíče dotazu. Když na jedinou hodnotu `*` je k dispozici, se liší middleware odpovědi na všechny žádosti parametrů řetězce dotazu. `VaryByQueryKeys` vyžaduje ASP.NET Core 1.1 nebo vyšší.

[Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) musí být povoleno nastavení `VaryByQueryKeys` vlastnosti; v opačném případě je vyvolána výjimka za běhu. Není k dispozici odpovídající hlavičku protokolu HTTP `VaryByQueryKeys` vlastnost. Vlastnost je funkce protokolu HTTP zpracovávaných middlewarem odpovědi ukládání do mezipaměti. Pro daný middleware pro obsluhu odpověď uložená v mezipaměti řetězec dotazu a hodnotu řetězce dotazu musí odpovídat předchozí žádosti. Představte si třeba pořadí požadavků a výsledky zobrazené v následující tabulce.

| Žádost                          | Výsledek                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | Server vrátil     |
| `http://example.com?key1=value1` | Vrácená z middlewaru |
| `http://example.com?key1=value2` | Server vrátil     |

První požadavek je vrácená serverem a uložili do mezipaměti v middlewaru. Druhý požadavek je vrátit middlewarem, protože řetězec dotazu odpovídá předchozí žádosti. Třetí žádost není v mezipaměti middleware, protože hodnotu řetězce dotazu neodpovídá předchozí žádosti. 

`ResponseCacheAttribute` Slouží ke konfiguraci a vytvořte (prostřednictvím `IFilterFactory`) [ResponseCacheFilter](/dotnet/api/microsoft.aspnetcore.mvc.internal.responsecachefilter). `ResponseCacheFilter` Provádí aktualizace správné hlavičky HTTP a funkce odpovědi. Filtr:

* Odebere všechny existující záhlaví pro `Vary`, `Cache-Control`, a `Pragma`. 
* Zapíše příslušné záhlaví podle vlastnosti nastavené `ResponseCacheAttribute`. 
* Aktualizace ukládání do mezipaměti funkce protokolu HTTP, pokud odpověď `VaryByQueryKeys` nastavena.

### <a name="vary"></a>se liší

Této hlavičky je zapsán, pouze když `VaryByHeader` je nastavena. Je nastaven na hodnotu `Vary` hodnoty vlastnosti. Následující ukázkový používá `VaryByHeader` vlastnost:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

Můžete zobrazit pomocí nástrojů v prohlížeči sítě hlavičky odpovědi. Následující obrázek ukazuje F12 Edge výstupních **sítě** kartu, kdy `About2` aktualizaci metody akce:

![Při volání metody akce About2 hraniční F12 výstup na síťové kartě](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore a Location.None

`NoStore` přepíše většinu dalších vlastností. Pokud je tato vlastnost nastavena na `true`, `Cache-Control` záhlaví je nastavena na `no-store`. Pokud `Location` je nastavena na `None`:

* `Cache-Control` je nastavena na `no-store,no-cache`.
* `Pragma` je nastavena na `no-cache`.

Pokud `NoStore` je `false` a `Location` je `None`, `Cache-Control` a `Pragma` jsou nastaveny na `no-cache`.

Obvykle nastavena `NoStore` k `true` na chybové stránky. Příklad:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

Výsledkem je následující hlavičky:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Umístění a doba trvání

Chcete-li povolit ukládání do mezipaměti, `Duration` musí být nastaven na kladnou hodnotu a `Location` musí být buď `Any` (výchozí) nebo `Client`. V takovém případě `Cache-Control` záhlaví je nastavena na hodnotu umístění, za nímž následuje `max-age` odpovědi.

> [!NOTE]
> `Location`v možnosti `Any` a `Client` překlad do `Cache-Control` hodnoty hlavičky `public` a `private`v uvedeném pořadí. Jak bylo uvedeno dříve, nastavení `Location` k `None` nastaví obě `Cache-Control` a `Pragma` záhlaví `no-cache`.

Níže je příklad ukazující hlavičky vytvořen tak, že nastavíte `Duration` a výchozí `Location` hodnotu:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

Tímto se vytvoří následující hlavičky:

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a>Profily mezipaměti

Namísto duplikování `ResponseCache` nastavení na mnoho atributů akce kontroleru, mezipaměti profily se dají konfigurovat jako možnosti při nastavování MVC v `ConfigureServices` metoda ve `Startup`. Hodnoty nalezené v profilu odkazované mezipaměti se používají jako výchozí hodnoty podle `ResponseCache` atribut a jsou přepsány podle libovolných vlastností zadané v atributu.

Nastavení profilu mezipaměti:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

Odkazování na profil mezipaměti:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

`ResponseCache` Atribut se dá použít k akce (metody) a kontrolerů (třídy). Atributy na úrovni metody přepsání nastavení uvedená v atributy na úrovni třídy.

V příkladu výše Určuje atribut úroveň třídy v délce 30 sekund, přestože úroveň metody atribut odkazuje profil mezipaměti s určitou dobou trvání nastaven na 60 sekund.

Výsledný záhlaví:

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a>Další zdroje

* [Ukládání odpovědí do mezipaměti](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
