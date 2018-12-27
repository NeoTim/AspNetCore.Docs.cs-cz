---
uid: web-api/overview/web-api-routing-and-actions/routing-and-action-selection
title: Směrování a výběr akce v rozhraní ASP.NET Web API | Dokumentace Microsoftu
author: MikeWasson
description: ''
ms.author: riande
ms.date: 12/14/2018
ms.assetid: bcf2d223-cb7f-411e-be05-f43e96a14015
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/routing-and-action-selection
msc.type: authoredcontent
ms.openlocfilehash: ce54181996376cb5dde3b91c10c16f33b3c6a570
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425169"
---
<a name="routing-and-action-selection-in-aspnet-web-api"></a>Směrování a výběr akce v rozhraní ASP.NET Web API
====================
podle [Mike Wasson](https://github.com/MikeWasson)

Tento článek popisuje, jak rozhraní ASP.NET Web API směruje požadavek HTTP na provedení určité akce v kontroleru.

> [!NOTE]
> Přehled směrování, naleznete v tématu [směrování v rozhraní ASP.NET Web API](routing-in-aspnet-web-api.md).


Tento článek ukazuje podrobnosti o procesu směrování. Pokud vytváříte projekt webového rozhraní API a zjistíte, že některé požadavky Nezískávat směrovány očekávaným způsobem, snad Tento článek vám pomůže.

Směrování má tři hlavní fáze:

1. Shodujícím se identifikátoru URI pro šablonu trasy.
2. Výběr kontroleru.
3. Výběr akce.

Některé části procesu můžete nahradit vlastní vlastní chování. V tomto článku popisují I výchozí chování. Na konci můžu mějte na paměti na místech, kde můžete přizpůsobit chování.

## <a name="route-templates"></a>Šablony trasy

Šablona trasy vypadá podobně jako na cestu URI, ale může mít hodnoty zástupných symbolů se složených závorek:

[!code-csharp[Main](routing-and-action-selection/samples/sample1.ps1)]

Při vytváření trasy můžete zadat výchozí hodnoty pro některé nebo všechny zástupné symboly:

[!code-csharp[Main](routing-and-action-selection/samples/sample2.cs)]

Můžete také zadat omezení, které omezují jak segment identifikátoru URI může odpovídat zástupný symbol:

[!code-csharp[Main](routing-and-action-selection/samples/sample3.js)]

Rozhraní se pokusí vyhledat segmentů v cestě identifikátor URI v šabloně. Literály v šabloně musí přesně odpovídat. Zástupný text odpovídá libovolné hodnotě, není-li zadat omezení. Rozhraní se neshoduje s jiné části identifikátoru URI, jako je název hostitele nebo parametry dotazu. Rozhraní framework vybere první trasy ve směrovací tabulce, která odpovídá identifikátoru URI.

Existují dva speciální zástupné znaky: "{controller}" a "{action}".

- "{controller}" obsahuje název kontroleru.
- "{action} obsahuje název akce. V rozhraní Web API je "{action} vynechejte běžné konvence.

### <a name="defaults"></a>Ve výchozím nastavení

Pokud zadáte výchozí hodnoty, bude odpovídat trasy identifikátor URI, který neobsahuje tyto segmenty. Příklad:

[!code-csharp[Main](routing-and-action-selection/samples/sample4.cs)]

Identifikátory URI `http://localhost/api/products/all` a `http://localhost/api/products` odpovídají předchozím postupu. V druhém URI chybějící `{category}` segmentu je přiřazen výchozí hodnota `all`.

### <a name="route-dictionary"></a>Slovníku trasy

Pokud rozhraní najde shodu pro identifikátor URI, vytvoří slovník, který obsahuje hodnotu pro každý zástupný symbol. Klíče jsou zástupné názvy, bez složených závorek. Hodnoty pocházejí z cesty identifikátoru URI nebo z výchozí hodnoty. Slovník je uložen v **IHttpRouteData** objektu.

V této fázi odpovídající trasy speciální "{controller}" a "{action} zástupné symboly zachází stejně jako jiné zástupné symboly. Jednoduše se ukládají ve slovníku s jiné hodnoty.

Výchozí může mít zvláštní hodnota **RouteParameter.Optional**. Pokud zástupný symbol získá přiřadí tuto hodnotu, hodnota není přidán do slovníku trasy. Příklad:

[!code-csharp[Main](routing-and-action-selection/samples/sample5.cs)]

Pro identifikátor URI cesty "/ produktů s rozhraním api" bude obsahovat slovníku trasy:

- kontroler: "produktů"
- kategorie: "vše"

"Api/produkty/toys/123" ale slovníku trasy obsahovat:

- kontroler: "produktů"
- kategorie: "toys"
- ID: "123"

Výchozí hodnoty může také obsahovat hodnotu, která se nezobrazí kdekoli v šabloně trasy. Pokud trasa odpovídá, tato hodnota je uložena ve slovníku. Příklad:

[!code-csharp[Main](routing-and-action-selection/samples/sample6.cs)]

Pokud cesta k identifikátoru URI je "rozhraní api/root/8", slovníku obsahovat dvě hodnoty:

- kontroler: "zákazníci"
- ID: "8"

## <a name="selecting-a-controller"></a>Vyberte kontroler

Výběr řadiče se zpracovává souborem **IHttpControllerSelector.SelectController** metody. Tato metoda přebírá **HttpRequestMessage** instance a vrátí **HttpControllerDescriptor**. Poskytuje výchozí implementaci **DefaultHttpControllerSelector** třídy. Tato třída používá jednoduché algoritmus:

1. Podívejte se ve slovníku trasy pro klíč "controller".
2. Přijmout hodnotu pro tento klíč a připojte řetězec "Controller" získat tak název typu kontroleru.
3. Vyhledejte kontroler Web API s tímto názvem typu.

Například pokud slovníku trasy obsahuje pár klíč hodnota "controller" = "produktů", "ProductsController" je typ kontroleru. Pokud neexistuje žádný odpovídající typ nebo více shod, rozhraní chybovou zprávu do klienta.

Krok 3 **DefaultHttpControllerSelector** používá **IHttpControllerTypeResolver** rozhraní se získat seznam typy kontrolerů webového rozhraní API. Výchozí implementace **IHttpControllerTypeResolver** vrátí všechny veřejné třídy, které implementují (a) **IHttpController**, (b) není abstraktní a (c) mít název, který končí na "Controller".

## <a name="action-selection"></a>Výběr akce

Po výběru kontroler rozhraní vybere akci voláním **IHttpActionSelector.SelectAction** metody. Tato metoda přebírá **HttpControllerContext** a vrátí **HttpActionDescriptor**.

Poskytuje výchozí implementaci **ApiControllerActionSelector** třídy. Pokud chcete vybrat akci, dohlíží na následující:

- Metoda HTTP požadavku.
- Zástupný text "{action}" v šabloně trasy, pokud jsou k dispozici.
- Parametry akce v kontroleru.

Před zobrazením algoritmus výběru, Nejdřív musíte seznámit pár věcí o akce kontroleru.

**Které metody na řadiči, jsou považovány za "akce"?** Když vyberete akci, rozhraní pouze prohledá veřejné instanci metody ve kontroleru. Také, vyloučí ["speciální název"](https://msdn.microsoft.com/library/system.reflection.methodbase.isspecialname) metod (konstruktorů, události, přetížení operátoru a tak dále) a metod zděděných z **objektu ApiController** třídy.

**Metody HTTP.** Rozhraní framework vybírá pouze akce, které odpovídají metoda HTTP požadavku stanoven následujícím způsobem:

1. Můžete určit metodu HTTP s atributem: **AcceptVerbs**, **HttpDelete**, **HttpGet**, **HttpHead**, **httpoptions měl**, **HttpPatch**, **HttpPost**, nebo **HttpPut**.
2. Jinak Pokud název metody kontroleru začíná řetězcem "Get", "Post", "Vložit", "Odstranit", "Head", "Options" nebo "Opravnou", pak podle konvence akci podporuje metody HTTP.
3. Pokud žádná z výše uvedených podporuje metodu POST.

**Vazby parametru.** Vazby parametru je způsob, jak vytvořit hodnotu pro parametr webového rozhraní API. Tady je výchozí pravidlo pro vazbu parametru:

- Jednoduché typy pocházejí z identifikátoru URI.
- Komplexní typy pocházejí z textu požadavku.

Jednoduché typy zahrnují všechny [primitivní typy rozhraní .NET Framework](https://msdn.microsoft.com/library/system.type.isprimitive), plus **data a času**, **desítkové**, **Guid**, **řetězec** , a **TimeSpan**. Pro každou akci můžete přečíst maximálně jeden parametr těla požadavku.

> [!NOTE]
> Je možné přepsat výchozí pravidla pro vazbu. Zobrazit [vazbu parametru WebAPI pod pokličkou](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx).


Tedy tady je výběr algoritmu akce.

1. Vytvoří seznam všech akcí na řadiči, které odpovídají metoda požadavku HTTP.
2. Pokud slovníku trasy má záznam "action", odeberte akce, jejíž název se neshoduje s Tato hodnota.
3. Vyzkoušejte tak, aby odpovídaly parametry akce na identifikátor URI, následujícím způsobem: 

    1. Pro každou akci Získejte seznam parametrů, které jsou jednoduchý typ, pokud vazba získá parametr z identifikátoru URI. Vylučte volitelné parametry.
    2. Z tohoto seznamu pokuste se najít shodu pro každý název parametru ve slovníku trasy nebo v řetězci dotazu identifikátoru URI. Shody jsou malá a velká písmena a nezávisí na pořadí parametrů.
    3. Vyberte akci, kde každý parametr v seznamu existuje shoda v identifikátoru URI.
    4. Pokud více tuto jednu akci se bude splňovat tyto podmínky, vyberte si jednu s nejvíce odpovídá parametru.
4. Ignorovat akce s **[NonAction]** atribut.

Krok #3 je pravděpodobně matoucí nejvíce. Základní myšlenka je, že parametr dostat jeho hodnotu z identifikátoru URI, z textu požadavku nebo z vlastní vazby. Pro parametry, které pochází z identifikátoru URI chceme se ujistit, že identifikátor URI ve skutečnosti obsahuje hodnotu pro tento parametr, a to buď v cestě (pomocí slovníku trasy), nebo v řetězci dotazu.

Představte si třeba následující akce:

[!code-csharp[Main](routing-and-action-selection/samples/sample7.cs)]

*Id* vazby parametru na identifikátor URI. Proto tato akce může odpovídat pouze identifikátor URI, který obsahuje hodnotu pro "id" ve slovníku trasy nebo v řetězci dotazu.

Volitelné parametry jsou výjimku, protože to jsou volitelné. Pro volitelný parametr je OK, pokud vazby nelze získat hodnotu z identifikátoru URI.

Komplexní typy jsou výjimky pro jiný důvod. Komplexní typ může být svázán pouze k identifikátoru URI prostřednictvím vlastní vazby. Ale v takovém případě rozhraní nemůže předem vědět, jestli parametr by svázat s konkrétní identifikátorem URI. Pokud chcete zjistit, je nutné vyvolat vazby. Cílem algoritmus výběru je ze statické popis před vyvoláním žádné vazby. Vyberte akci. Proto komplexní typy jsou vyloučeny z porovnávací algoritmus.

Po výběru akce jsou vyvolány všechny vazby parametru.

Shrnutí:

- Metoda HTTP požadavku musí odpovídat akce.
- Název akce musí odpovídat "action" položky ve slovníku trasy, pokud jsou k dispozici.
- Pro každý parametr akce Pokud tento parametr je převzata z identifikátoru URI, pak název parametru musí najít ve slovníku trasy nebo v řetězci dotazu identifikátoru URI. (Volitelné parametry a parametry s komplexní typy jsou vyloučeny.)
- Pokuste se shodovat s největším počtem parametrů. Nejlepší shody může být metoda bez parametrů.

## <a name="extended-example"></a>Rozšířený příklad

Trasy:

[!code-csharp[Main](routing-and-action-selection/samples/sample8.cs)]

Kontroler:

[!code-csharp[Main](routing-and-action-selection/samples/sample9.cs)]

Požadavek protokolu HTTP:

[!code-console[Main](routing-and-action-selection/samples/sample10.cmd)]

### <a name="route-matching"></a>Odpovídající trasy

Identifikátor URI odpovídá trasa s názvem "DefaultApi". Slovníku trasy obsahuje následující položky:

- kontroler: "produktů"
- ID: "1"

Slovníku trasy neobsahuje parametry řetězce dotazu, "verze" a "details", ale ty budou mít nadále za během výběr akce.

### <a name="controller-selection"></a>Výběr řadiče

Z položky "controller" ve slovníku trasy, je typ kontroleru `ProductsController`.

### <a name="action-selection"></a>Výběr akce

Požadavek HTTP je požadavek GET. Jsou akce kontroleru, které podporují GET `GetAll`, `GetById`, a `FindProductsByName`. Slovníku trasy neobsahuje položku pro "action", proto nepotřebujeme tak, aby odpovídaly názvu akce.

Dále pokusíme se porovnat názvy parametrů pro akce, pouze na akce GET.

| Akce | Parametry shody |
| --- | --- |
| `GetAll` | žádná |
| `GetById` | "id" |
| `FindProductsByName` | "název" |

Všimněte si, *verze* parametr `GetById` nepovažuje, protože se jedná volitelný parametr.

`GetAll` Metoda odpovídá triviálně. `GetById` Metoda také odpovídající, protože obsahuje "id" slovníku trasy. `FindProductsByName` Metoda se neshoduje.

`GetById` Metoda služby wins, protože odpovídá jeden parametr, a bez parametrů pro `GetAll`. Metoda je volána s následující hodnoty parametrů:

- *ID* = 1
- *verze* = 1.5

Všimněte si, že i když *verze* nebyl použit algoritmus výběru pochází hodnotu parametru řetězce dotazu URI.

## <a name="extension-points"></a>Rozšiřovací body

Webové rozhraní API poskytuje Rozšiřovací body pro některé části procesu směrování.

| Rozhraní | Popis |
| --- | --- |
| **IHttpControllerSelector** | Vybere kontroler. |
| **IHttpControllerTypeResolver** | Získá seznam typů řadičů. **DefaultHttpControllerSelector** z tohoto seznamu zvolí typ kontroleru. |
| **IAssembliesResolver** | Získá seznam sestavení projektu. **IHttpControllerTypeResolver** rozhraní používá tento seznam k nalezení typů řadičů. |
| **IHttpControllerActivator** | Vytvoří nové instance kontroleru. |
| **IHttpActionSelector** | Vybere akci. |
| **IHttpActionInvoker** | Vyvolá akci. |

Pokud chcete poskytnout vlastní implementaci pro kterékoli z těchto rozhraní, použijte **služby** kolekce na **HttpConfiguration** objektu:

[!code-csharp[Main](routing-and-action-selection/samples/sample11.cs)]
