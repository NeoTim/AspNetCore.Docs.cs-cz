---
title: Ukládání odpovědí do mezipaměti v ASP.NET Core
author: rick-anderson
description: Naučte se, jak používat ukládání odpovědí do mezipaměti pro menší požadavky na šířku pásma a zvýšit výkon aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
uid: performance/caching/response
ms.openlocfilehash: 9246305e6979a6a7e006f567ee6bf9569029aef1
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828305"
---
# <a name="response-caching-in-aspnet-core"></a>Ukládání odpovědí do mezipaměti v ASP.NET Core

[Jan Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/)a [Luke Latham](https://github.com/guardrex)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukládání odpovědí do mezipaměti snižuje počet požadavků, které klient nebo proxy vytvoří na webový server. Ukládání odpovědí do mezipaměti také snižuje množství práce, které webový Server provede k vygenerování odpovědi. Ukládání odpovědí do mezipaměti je řízeno hlavičkami, které určují, jak má klient, proxy a middleware ukládat odpovědi do mezipaměti.

[Atribut ResponseCache](#responsecache-attribute) se účastní nastavení hlaviček ukládání odpovědí do mezipaměti. Klienti a zprostředkující proxy servery by měly dodržovat hlavičky pro ukládání odpovědí do mezipaměti v rámci [specifikace ukládání do mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234).

Pro ukládání do mezipaměti na straně serveru, které následuje za specifikací mezipaměti HTTP 1,1, použijte [middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware). Middleware může pomocí vlastností <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> ovlivnit chování ukládání do mezipaměti na straně serveru.

## <a name="http-based-response-caching"></a>Ukládání odpovědí na základě protokolu HTTP

[Specifikace ukládání do mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234) popisuje, jak se mají chovat internetové mezipaměti. Primární hlavičkou HTTP použitou pro ukládání do mezipaměti je [řízení mezipaměti](https://tools.ietf.org/html/rfc7234#section-5.2), které se používá k určení *direktiv*mezipaměti. Direktivy řídí chování ukládání do mezipaměti jako požadavky od klientů po servery a jako reakce způsobují od serverů zpátky klientům. Žádosti a odpovědi se pohybují přes proxy servery a proxy servery musí splňovat i specifikace mezipaměti HTTP 1,1.

Společné direktivy `Cache-Control` jsou uvedeny v následující tabulce.

| – Direktiva                                                       | Akce |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Mezipaměť může ukládat odpověď. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Odpověď nesmí být uložena sdílenou mezipamětí. Soukromá mezipaměť může uložit a znovu použít odpověď. |
| [Maximální stáří](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Klient nepřijme odpověď, jejíž stáří je větší než zadaný počet sekund. Příklady: `max-age=60` (60 sekund), `max-age=2592000` (1 měsíc) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **V případě požadavků**: mezipaměť nesmí k uspokojení požadavku použít uloženou odpověď. Zdrojový server znovu vygeneruje odpověď pro klienta a middleware aktualizuje uloženou odpověď v mezipaměti.<br><br>**Na odpovědích**: odpověď nesmí být použita pro následné žádosti bez ověření na zdrojovém serveru. |
| [bez uložení](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **V žádostech**: mezipaměť nesmí ukládat požadavek.<br><br>**V odpovědích**: mezipaměť nesmí ukládat žádnou část odpovědi. |

Další hlavičky mezipaměti, které hrají roli v mezipaměti, jsou uvedeny v následující tabulce.

| Záhlaví                                                     | Funkce |
| ---------------------------------------------------------- | -------- |
| [Stáří](https://tools.ietf.org/html/rfc7234#section-5.1)     | Odhad doby v sekundách, po jejímž uplynutí byla odpověď generována nebo úspěšně ověřena na zdrojovém serveru. |
| [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | Čas, po jehož uplynutí je odpověď považována za zastaralou. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Existuje v případě zpětné kompatibility s mezipamětí HTTP/1.0 pro nastavení chování `no-cache`. Pokud je k dispozici hlavička `Cache-Control`, hlavička `Pragma` se ignoruje. |
| [Toho](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Určuje, že odpověď uložená v mezipaměti nesmí být odeslána, pokud všechna `Vary` pole hlaviček neodpovídají v původní žádosti odpovědi v mezipaměti i v novém požadavku. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Požadavky na ukládání do mezipaměti založené na protokolu HTTP respektují direktivy řízení mezipaměti

[Specifikace ukládání do mezipaměti protokolu HTTP 1,1 pro hlavičku Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) vyžaduje mezipaměť, aby bylo možné akceptovat platnou hlavičku `Cache-Control` odeslanou klientem. Klient může vytvořit požadavky s hodnotou hlavičky `no-cache` a vynutit, aby server vygeneroval novou odpověď pro každý požadavek.

Používání hlaviček požadavků klienta `Cache-Control` vždy dává smysl, pokud považujete cíl ukládání HTTP do mezipaměti. V rámci oficiální specifikace je ukládání do mezipaměti určeno ke snížení latence a zatížení sítě při požadavcích na požadavky v síti klientů, proxy serverů a serverů. To není nutně způsob, jak řídit zatížení na zdrojovém serveru.

Při použití [middleware pro ukládání odpovědí](xref:performance/caching/middleware) do mezipaměti není k dispozici žádné vývojářské řízení, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti. [Plánovaná vylepšení middlewaru](https://github.com/dotnet/AspNetCore/issues/2612) jsou příležitostí ke konfiguraci middlewaru pro ignorování hlavičky `Cache-Control` požadavku při rozhodování o obsluze odpovědi v mezipaměti. Plánovaná vylepšení poskytují možnost lepšího řízení zatížení serveru.

## <a name="other-caching-technology-in-aspnet-core"></a>Další technologie pro ukládání do mezipaměti v ASP.NET Core

### <a name="in-memory-caching"></a>Ukládání do mezipaměti v paměti

Ukládání do mezipaměti v paměti používá paměť serveru k ukládání dat uložených v mezipaměti. Tento typ ukládání do mezipaměti je vhodný pro jeden server nebo víc serverů pomocí *relací s rychlým cvičením*. Rychlé relace znamenají, že požadavky klienta jsou vždycky směrovány na stejný server ke zpracování.

Další informace najdete v tématu <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Distribuovaná mezipaměť

Pomocí distribuované mezipaměti můžete ukládat data v paměti, když je aplikace hostovaná v cloudové nebo serverové farmě. Mezipaměť se sdílí mezi servery, které zpracovávají požadavky. Klient může odeslat žádost, která je zpracována jakýmkoli serverem ve skupině, pokud jsou k dispozici data uložená v mezipaměti pro klienta. ASP.NET Core nabízí SQL Server a Redis distribuované mezipaměti.

Další informace najdete v tématu <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Pomocná rutina značky mezipaměti

Ukládání obsahu do mezipaměti v zobrazení MVC nebo na stránce Razor pomocí pomocníka značky mezipaměti. Pomocník značek Cache používá ukládání dat do mezipaměti v paměti.

Další informace najdete v tématu <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Pomocná rutina značky distribuované mezipaměti

Ukládání obsahu do mezipaměti v zobrazení MVC nebo na stránce Razor v rámci distribuovaného cloudu nebo webové farmy pomocí pomocníka značek distribuované mezipaměti. Pomocník značek distribuované mezipaměti používá k ukládání dat SQL Server nebo Redis.

Další informace najdete v tématu <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>ResponseCache – atribut

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Určuje parametry potřebné pro nastavení příslušných hlaviček v ukládání odpovědí do mezipaměti.

> [!WARNING]
> Zakáže ukládání do mezipaměti pro obsah, který obsahuje informace pro ověřené klienty. Ukládání do mezipaměti by mělo být povolené jenom pro obsah, který se nemění v závislosti na identitě uživatele nebo na tom, jestli je uživatel přihlášený.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> mění uloženou odpověď podle hodnot daného seznamu klíčů dotazů. Když je k dispozici jediná hodnota `*`, middleware bude měnit odpovědi všemi parametry řetězce dotazu Request.

Aby bylo možné nastavit vlastnost <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>, musí být povoleno [middleware mezipaměti odpovědí](xref:performance/caching/middleware) . V opačném případě je vyvolána výjimka modulu runtime. Pro vlastnost <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> neexistuje odpovídající hlavička protokolu HTTP. Vlastnost je funkce HTTP, kterou zpracovává middleware pro ukládání odpovědí do mezipaměti. Aby middleware poskytovala odpověď uloženou v mezipaměti, řetězec dotazu a hodnota řetězce dotazu musí odpovídat předchozí žádosti. Zvažte například posloupnost požadavků a výsledků, které jsou uvedeny v následující tabulce.

| Request                          | Výsledek                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Vráceno ze serveru. |
| `http://example.com?key1=value1` | Vráceno z middlewaru. |
| `http://example.com?key1=value2` | Vráceno ze serveru. |

První požadavek vrací Server a ukládá do mezipaměti v middlewaru. Druhý požadavek vrací middleware, protože řetězec dotazu odpovídá předchozímu požadavku. Třetí žádost není v mezipaměti middlewaru, protože hodnota řetězce dotazu neodpovídá předchozí žádosti.

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> se používá ke konfiguraci a vytvoření (prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`. `ResponseCacheFilter` provádí aktualizaci příslušných hlaviček protokolu HTTP a funkcí odpovědi. Filtr:

* Odebere všechna existující záhlaví pro `Vary`, `Cache-Control`a `Pragma`.
* Vypíše příslušná záhlaví na základě vlastností nastavených v <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.
* Aktualizuje funkci ukládání do mezipaměti odpovědí HTTP, pokud je nastavena <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>.

### <a name="vary"></a>Toho

Tato hlavička je zapsána pouze v případě, že je nastavena vlastnost <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader>. Vlastnost nastavená na hodnotu vlastnosti `Vary`. Následující příklad používá vlastnost <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader>:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Pomocí ukázkové aplikace si prohlédněte hlavičky odpovědí pomocí síťových nástrojů v prohlížeči. Následující hlavičky odpovědí se odesílají s odpovědí na stránku Cache1:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>Úložiště a umístění. žádné

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> Přepisuje většinu dalších vlastností. Když je tato vlastnost nastavená na `true`, `Cache-Control` záhlaví je nastavené na `no-store`. Pokud je <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> nastaveno na `None`:

* `Cache-Control` je nastavená na `no-store,no-cache`.
* `Pragma` je nastavená na `no-cache`.

Pokud je <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> `false` a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None`, `Cache-Control`a `Pragma` je nastavená na `no-cache`.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> je obvykle nastaveno na `true` pro chybové stránky. Stránka Cache2 v ukázkové aplikaci vytváří hlavičky odpovědí, které klientovi neukládají odpověď.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

Ukázková aplikace vrátí stránku Cache2 s následujícími záhlavími:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Umístění a doba trvání

Chcete-li povolit ukládání do mezipaměti, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> musí být nastaven na kladnou hodnotu a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> musí být buď `Any` (výchozí), nebo `Client`. Rozhraní nastaví hlavičku `Cache-Control` na hodnotu umístění následovanou `max-age` odpovědi.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>možností `Any` a `Client` se překládat do `Cache-Control` hodnot hlaviček `public` a `private`v uvedeném pořadí. Jak je uvedeno v části [úložiště a umístění. None](#nostore-and-locationnone) , nastavení <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> pro `None` nastaví `Cache-Control` a `Pragma` hlavičky na `no-cache`.

`Location.Any` (`Cache-Control` nastavené na `public`) znamená, že *klient nebo jakýkoli zprostředkující proxy server* můžou hodnotu ukládat do mezipaměti, včetně [middlewaru pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware).

`Location.Client` (`Cache-Control` nastavená na `private`) znamená, že hodnotu může ukládat *jenom klient* . Žádná mezimezipaměť by neměla hodnotu ukládat do mezipaměti, včetně [middlewaru pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware).

Řídicí hlavičky mezipaměti pouze poskytují pokyny klientům a zprostředkujícím proxy serverům, kdy a jak ukládat odpovědi do mezipaměti. Není nijak zaručeno, že klienti a proxy budou respektovat [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234). [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) bude vždycky postupovat podle pravidel pro ukládání do mezipaměti, která jsou stanovená specifikací

Následující příklad ukazuje model stránky Cache3 z ukázkové aplikace a záhlaví vytvořená nastavením <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> a opouští výchozí hodnotu <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

Ukázková aplikace vrátí stránku Cache3 s následující hlavičkou:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Profily mezipaměti

Místo duplikace nastavení mezipaměti odpovědí u mnoha atributů akce kontroleru lze profily mezipaměti nakonfigurovat jako možnosti při nastavování MVC/Razor Pages v `Startup.ConfigureServices`. Hodnoty nalezené v profilu odkazované mezipaměti se používají jako výchozí hodnoty <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> a jsou přepsány všemi vlastnostmi zadanými v atributu.

Nastavte profil mezipaměti. Následující příklad ukazuje 30 sekundový profil mezipaměti v `Startup.ConfigureServices`ukázkové aplikace:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

Model stránky Cache4 ukázkové aplikace odkazuje na profil mezipaměti `Default30`:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> lze použít pro:

* Obslužné rutiny stránky Razor (třídy) &ndash; atributy nelze použít na metody obslužné rutiny.
* Řadiče MVC (třídy).
* Akce (metody) MVC &ndash; atributy na úrovni metody přepíšou nastavení zadaná v atributech na úrovni třídy.

Výsledná hlavička použitá pro odpověď stránky Cache4 profilem mezipaměti `Default30`:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Ukládání odpovědí do mezipamětí](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
