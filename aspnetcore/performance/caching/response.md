---
title: Ukládání odpovědí do mezipaměti v ASP.NET Core
author: rick-anderson
description: Další informace o použití odpověď do mezipaměti pro nižší požadavky na šířku pásma a zvýšit výkon aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/28/2019
uid: performance/caching/response
ms.openlocfilehash: efcf443b1487827fe6cf4d43b6dda69adf4d61fb
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345743"
---
# <a name="response-caching-in-aspnet-core"></a>Ukládání odpovědí do mezipaměti v ASP.NET Core

Podle [Jan Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), a [Luke Latham](https://github.com/guardrex)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukládání odpovědí do mezipaměti snižuje počet požadavků, které odešle klient nebo server proxy webový server. Ukládání odpovědí do mezipaměti také snižuje množství práce provádí webového serveru pro generování odpovědi. Ukládání odpovědí do mezipaměti se řídí hlavičky, které určují, jak chcete klienta, serveru proxy a middlewarem do mezipaměti odpovědi.

[ResponseCache atribut](#responsecache-attribute) účastní nastavení odpověď záhlaví, kteří klienti mohou případném dalším sdílení dodržovat při ukládání odpovědí do mezipaměti. [Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) je možné do mezipaměti odpovědi na serveru. Middleware lze použít <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> vlastnosti a ovlivnit chování ukládání do mezipaměti na straně serveru.

## <a name="http-based-response-caching"></a>Ukládání do mezipaměti založené na protokolu HTTP odpovědi

[Specifikace HTTP 1.1 ukládání do mezipaměti](https://tools.ietf.org/html/rfc7234) popisuje chování mezipaměti Internet. Primární záhlaví HTTP používá pro ukládání do mezipaměti je [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), který se používá k určení mezipaměti *direktivy*. Direktivy řízení chování ukládání do mezipaměti požadavky dostanou od klientů na servery a odpovědi dostanou ze serverů zpět klientům. Požadavky a odpovědi procházet proxy servery a proxy servery musí také odpovídají specifikaci HTTP 1.1 ukládání do mezipaměti.

Běžné `Cache-Control` direktivy jsou uvedeny v následující tabulce.

| – Direktiva                                                       | Akce |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Mezipaměť může ukládat odpovědi. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Odpověď nesmí být uloženy ve sdílené mezipaměti. Soukromé mezipaměti může ukládat a opakovaně používat odpovědi. |
| [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Klient nepřijme odpovědi, jejichž stáří je větší než zadaný počet sekund. Příklady: `max-age=60` (60 sekund), `max-age=2592000` (1 měsíc) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **U požadavků**: Mezipaměť nesmí používat uložené odpovědi, abyste vyhověli žádosti. Zdrojový server obnoví odpovědi pro klienta a middleware aktualizuje odpověď na uložené v mezipaměti.<br><br>**V odpovědi**: Odpověď nesmí se používat pro další požadavek bez ověřování na původním serveru. |
| [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **U požadavků**: Mezipaměť nesmí uložit žádost.<br><br>**V odpovědi**: Mezipaměť nesmí uložit libovolnou část odpovědi. |

V následující tabulce jsou uvedeny další hlavičky mezipaměti, které hrají roli při ukládání do mezipaměti.

| Záhlaví                                                     | Funkce |
| ---------------------------------------------------------- | -------- |
| [Stáří](https://tools.ietf.org/html/rfc7234#section-5.1)     | Odhad množství času v sekundách, protože odpověď byla vygenerována nebo úspěšně ověřen na původním serveru. |
| [Vypršení platnosti](https://tools.ietf.org/html/rfc7234#section-5.3) | Doba, po jejímž uplynutí odpovědi se považuje za zastaralý. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Pro zpětnou kompatibilitu s HTTP verze 1.0 ukládá do mezipaměti pro nastavení existuje `no-cache` chování. Pokud `Cache-Control` záhlaví je k dispozici, `Pragma` záhlaví se ignoruje. |
| [se liší](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Určuje, že odpověď uložená v mezipaměti nesmí být odeslána, pokud všechny nástroje `Vary` záhlaví pole shodují v původní požadavek odpověď uložená v mezipaměti a nový požadavek. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Ukládání do mezipaměti respektuje založené na protokolu HTTP požadavku direktivy Cache-Control

[Specifikace HTTP 1.1 ukládání do mezipaměti pro hlavičku Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) vyžaduje mezipaměti případném dalším sdílení dodržovat platné `Cache-Control` záhlaví odesílaném klientem. Klienta můžete vytvářet požadavky s `no-cache` hodnota hlavičky a vynutit server vygenerovat novou odpověď pro každý požadavek.

Vždy dodržením klienta `Cache-Control` hlavičky žádosti dává smysl, pokud považujete za cíl protokolu HTTP, ukládání do mezipaměti. V části specifikaci oficiální ukládání do mezipaměti smyslem je snížit latenci a sítě režii splňující požadavky napříč sítí klientů, proxy servery a servery. Není nutně způsob, jak řídit zatížení na původním serveru.

Neexistuje žádné vývojáři řídit tohoto chování ukládání do mezipaměti při použití [Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) vzhledem k tomu, že dodržuje middleware official je přínosné pro ukládání do mezipaměti specifikace. [Plánované vylepšení middleware](https://github.com/aspnet/AspNetCore/issues/2612) jsou příležitosti pro konfiguraci middlewaru ignorovat požadavku `Cache-Control` hlavičku při rozhodování o tom, která bude sloužit odpověď uložená v mezipaměti. Plánované vylepšení nabízejí možnost lepší řízení zatížení serveru.

## <a name="other-caching-technology-in-aspnet-core"></a>Další technologie ukládání do mezipaměti v ASP.NET Core

### <a name="in-memory-caching"></a>Ukládání do mezipaměti v paměti

Ukládání do mezipaměti v paměti používá k ukládání dat uložených v mezipaměti paměti serveru. Tento typ ukládání do mezipaměti je vhodný pro jeden nebo více servery pomocí *rychlé relace*. Rychlé relace znamená, že požadavky od klienta jsou vždy směrovány na stejný server ke zpracování.

Další informace naleznete v tématu <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Distribuovaná mezipaměť

K ukládání dat v paměti, když je aplikace hostovaná v cloudu nebo serveru farmy pomocí distribuované mezipaměti. Mezipaměť je sdílen mezi servery, které zpracovávají požadavky. Klient může odeslat žádost, kterou provádí služba jakýkoli server ve skupině, pokud je k dispozici data uložená v mezipaměti klienta. ASP.NET Core nabízí systému SQL Server a mezipamětí Redis distribuovat.

Další informace naleznete v tématu <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Pomocné rutiny značky do mezipaměti

Ukládat do mezipaměti obsah ze zobrazení MVC nebo stránky Razor s pomocné rutiny značky mezipaměti. Pomocná rutina značek mezipaměti používá k ukládání dat do mezipaměti v paměti.

Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Pomocná rutina značek v distribuované mezipaměti

Do mezipaměti obsah ze zobrazení MVC nebo stránky Razor distribuovaných cloud nebo web scénářích farmy s pomocná rutina značek distribuované mezipaměti. Distribuované mezipaměti pomocná rutina značek v používá k ukládání dat serveru SQL Server nebo Redis.

Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>Atribut ResponseCache

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Určuje parametry, které jsou nezbytné pro nastavení příslušné záhlaví v ukládání odpovědí do mezipaměti.

> [!WARNING]
> Zakáže ukládání do mezipaměti pro obsah, který obsahuje informace o ověření klienti. Ukládání do mezipaměti musí být povolené pouze pro obsah, který se nezmění na základě identity uživatele nebo určuje, zda je uživatel přihlášený.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> uložené odpovědi se liší podle hodnoty daný seznam klíče dotazu. Když na jedinou hodnotu `*` je k dispozici, se liší middleware odpovědi na všechny žádosti parametrů řetězce dotazu.

[Middleware pro ukládání do mezipaměti odpovědí](xref:performance/caching/middleware) musí být povoleno nastavení <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> vlastnost. V opačném případě je vyvolána výjimka za běhu. Není k dispozici odpovídající hlavičku protokolu HTTP <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> vlastnost. Vlastnost je zpracována Middleware pro ukládání do mezipaměti odpovědí funkce protokolu HTTP. Pro daný middleware pro obsluhu odpověď uložená v mezipaměti řetězec dotazu a hodnotu řetězce dotazu musí odpovídat předchozí žádosti. Představte si třeba pořadí požadavků a výsledky zobrazené v následující tabulce.

| Žádost                          | Výsledek                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Vrácená ze serveru. |
| `http://example.com?key1=value1` | Vrácená z middlewaru. |
| `http://example.com?key1=value2` | Vrácená ze serveru. |

První požadavek je vrácená serverem a uložili do mezipaměti v middlewaru. Druhý požadavek je vrátit middlewarem, protože řetězec dotazu odpovídá předchozí žádosti. Třetí žádost není v mezipaměti middleware, protože hodnotu řetězce dotazu neodpovídá předchozí žádosti.

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Slouží ke konfiguraci a vytvořte (prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>. <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter> Provádí aktualizace správné hlavičky HTTP a funkce odpovědi. Filtr:

* Odebere všechny existující záhlaví pro `Vary`, `Cache-Control`, a `Pragma`.
* Zapíše příslušné záhlaví podle vlastnosti nastavené <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.
* Aktualizace ukládání do mezipaměti funkce protokolu HTTP, pokud odpověď <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> nastavena.

### <a name="vary"></a>se liší

Této hlavičky je zapsán, pouze když <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> je nastavena. Vlastnost nastavena `Vary` hodnoty vlastnosti. Následující ukázkový používá <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> vlastnost:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Použití ukázkové aplikace, zobrazte hlavičky odpovědi se nástroje prohlížeče sítě. Následující hlavičky odpovědi se odesílají s Cache1 odezvy stránky:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore a Location.None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> přepíše většinu dalších vlastností. Pokud je tato vlastnost nastavena na `true`, `Cache-Control` záhlaví je nastavena na `no-store`. Pokud <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> je nastavena na `None`:

* `Cache-Control` je nastavena na `no-store,no-cache`.
* `Pragma` je nastavena na `no-cache`.

Pokud <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> je `false` a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> je `None`, `Cache-Control`, a `Pragma` jsou nastaveny na `no-cache`.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> obvykle nastavena na `true` pro chybové stránky. Na stránce Cache2 v ukázkové aplikaci vytvoří hlavičky odpovědi, které dáte pokyn, aby klient Neukládat odpovědi.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

Ukázková aplikace vrátí stránku Cache2 s těmito záhlavími:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Umístění a doba trvání

Chcete-li povolit ukládání do mezipaměti, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> musí být nastaven na kladnou hodnotu a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> musí být buď `Any` (výchozí) nebo `Client`. V takovém případě `Cache-Control` záhlaví je nastavena na hodnotu umístění, za nímž následuje `max-age` odpovědi.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>v možnosti `Any` a `Client` překlad do `Cache-Control` hodnoty hlavičky `public` a `private`v uvedeném pořadí. Jak bylo uvedeno dříve, nastavení <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> k `None` nastaví obě `Cache-Control` a `Pragma` záhlaví `no-cache`.

Následující příklad ukazuje Cache3 model stránky z ukázkovou aplikaci a vytvořené tak, že nastavíte záhlaví <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> a výchozí <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> hodnotu:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

Ukázková aplikace vrátí stránku Cache3 s následující hlavičky:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Profily mezipaměti

Namísto duplikování nastavení mezipaměti pro odpovědi u mnoha atributů akce kontroleru, mezipaměti profily se dají konfigurovat jako možnosti při nastavování MVC/Razor Pages v `Startup.ConfigureServices`. Hodnoty nalezené v profilu odkazované mezipaměti se používají jako výchozí hodnoty podle <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> a jsou přepsány podle libovolných vlastností zadané v atributu.

Nastavení profilu mezipaměti. Následující příklad ukazuje 30 druhý profil mezipaměti v ukázkové aplikaci `Startup.ConfigureServices`:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

Odkazy na ukázkové aplikace Cache4 stránky model `Default30` profilu do mezipaměti:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Můžete použít pro:

* Stránka Razor (třídy) – obslužné rutiny &ndash; atributy nejde použít u metody obslužné rutiny.
* Kontrolery MVC (třídy).
* Akce MVC (metody) &ndash; atributy na úrovni metody přepsání nastavení uvedená v atributy na úrovni třídy.

Výsledný záhlaví aplikované na odpověď stránky Cache4 podle `Default30` profilu do mezipaměti:

```
Cache-Control: public,max-age=30
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
