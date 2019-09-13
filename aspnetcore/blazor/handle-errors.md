---
title: Zpracování chyb v aplikacích ASP.NET Core Blazor
author: guardrex
description: Zjistěte, jak ASP.NET Core Blazor, jak Blazor spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/06/2019
uid: blazor/handle-errors
ms.openlocfilehash: d3e261e83f375574339a8ce3428e8bfb73df4307
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963991"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Zpracování chyb v aplikacích ASP.NET Core Blazor

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)

Tento článek popisuje, jak Blazor spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a>Jak Blazor Framework reaguje na neošetřené výjimky

Blazor Server je stavový systém. I když uživatelé pracují s aplikací, udržují připojení k serveru známému jako *okruh*. Okruh obsahuje aktivní instance komponent a mnoho dalších aspektů stavu, například:

* Poslední Vykreslený výstup komponent.
* Aktuální sada delegátů zpracovávajících události, které mohou být aktivovány událostmi na straně klienta.

Pokud uživatel otevře aplikaci na více kartách prohlížeče, má několik nezávislých okruhů.

Blazor považuje většinu neošetřených výjimek za závažné v okruhu, ve kterém se vyskytují. Pokud je okruh ukončen z důvodu neošetřené výjimky, uživatel může pokračovat v interakci s aplikací pouze tak, že znovu načte stránku, aby vytvořila nový okruh. Nejsou ovlivněny okruhy mimo tu, která je ukončená, což jsou okruhy pro ostatní uživatele nebo jiné karty prohlížeče. Tento scénář je podobný jako desktopová aplikace, která&mdash;způsobí zhroucení chybných aplikací, které se musí restartovat, ale ostatní aplikace to neovlivní.

Okruh se ukončí, když dojde k neošetřené výjimce z následujících důvodů:

* Neošetřená výjimka často opustí okruh v nedefinovaném stavu.
* Po neošetřené výjimce nelze zaručit normální operaci aplikace.
* V případě, že okruh pokračuje, mohou se v aplikaci zobrazit slabá místa zabezpečení.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Správa neošetřených výjimek v kódu pro vývojáře

Aby aplikace pokračovala i po chybě, aplikace musí mít logiku zpracování chyb. Pozdější části tohoto článku popisují možné zdroje neošetřených výjimek.

V produkčním prostředí nevykresluje zprávy o výjimkách rozhraní nebo trasování zásobníku v uživatelském rozhraní. Vykreslování zpráv výjimek nebo trasování zásobníku může:

* Vyzradit citlivé informace koncovým uživatelům.
* Pomáhat uživateli se zlými úmysly zjišťovat slabiny v aplikaci, která může ohrozit zabezpečení aplikace, serveru nebo sítě.

## <a name="log-errors-with-a-persistent-provider"></a>Protokolovat chyby trvalého poskytovatele

Pokud dojde k neošetřené výjimce, je výjimka zaznamenána <xref:Microsoft.Extensions.Logging.ILogger> do instancí nakonfigurovaných v kontejneru služby. Ve výchozím nastavení se aplikace Blazor do výstupu konzoly přihlásí pomocí zprostředkovatele protokolování konzoly. Zvažte možnost protokolování do trvalého umístění u poskytovatele, který spravuje velikost protokolu a rotaci protokolů. Další informace naleznete v tématu <xref:fundamentals/logging/index>.

Během vývoje Blazor obvykle odesílá úplné podrobnosti o výjimkách do konzoly prohlížeče, aby bylo možné v ladění. V produkčním prostředí jsou podrobné chyby v konzole prohlížeče ve výchozím nastavení zakázané, což znamená, že se do klientů neodesílají chyby, ale úplné podrobnosti o výjimce se pořád protokolují na straně serveru. Další informace naleznete v tématu <xref:fundamentals/error-handling>.

Musíte se rozhodnout, které incidenty se mají protokolovat, a úroveň závažnosti protokolovaných incidentů. Nepřátelským uživatelům může být možné aktivovat chyby záměrně. Například Neprotokolujte incident z chyby, kde je uvedena neznámá `ProductId` adresa URL komponenty, která zobrazuje podrobnosti o produktu. Ne všechny chyby by se měly považovat za incidenty s vysokou závažností pro protokolování.

## <a name="places-where-errors-may-occur"></a>Místa, kde může dojít k chybám

Rozhraní a kód aplikace mohou aktivovat neošetřené výjimky v žádném z následujících umístění:

* [Instance komponenty](#component-instantiation)
* [Metody životního cyklu](#lifecycle-methods)
* [Logika vykreslování](#rendering-logic)
* [Obslužné rutiny událostí](#event-handlers)
* [Vyřazení součásti](#component-disposal)
* [Zprostředkovatel komunikace s JavaScriptem](#javascript-interop)
* [Obslužné rutiny okruhu](#circuit-handlers)
* [Vyřazení okruhů](#circuit-disposal)
* [Předvykreslování](#prerendering)

Předchozí neošetřené výjimky jsou popsány v následujících částech tohoto článku.

### <a name="component-instantiation"></a>Instance komponenty

Když Blazor vytvoří instanci komponenty:

* Je vyvolán konstruktor součásti.
* Jsou vyvolány konstruktory jakékoliv nejednoznačné služby di Services dodávané konstruktoru komponenty prostřednictvím [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) direktivy nebo atributu [[vložení]](xref:blazor/dependency-injection#request-a-service-in-a-component) . 

Okruh se nezdařil, pokud kterýkoli z spouštěného konstruktoru nebo `[Inject]` setter pro jakoukoliv vlastnost vyvolá neošetřenou výjimku. Výjimka je závažná, protože architektura nemůže vytvořit instanci komponenty. Pokud logika konstruktoru může vyvolat výjimky, aplikace by měla zachytit výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.

### <a name="lifecycle-methods"></a>Metody životního cyklu

Během životnosti komponenty Blazor vyvolá metody životního cyklu:

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Pokud jakákoli metoda životního cyklu vyvolá výjimku synchronně nebo asynchronně, je výjimka pro okruh závažná. Pro součásti, které se zabývat chybami v metodách životního cyklu, přidejte logiku zpracování chyb.

V následujícím příkladu, který `OnParametersSetAsync` volá metodu pro získání produktu:

* Výjimka vyvolaná v `ProductRepository.GetProductByIdAsync` metodě je zpracována `try-catch` příkazem.
* Po spuštění `catch` bloku:
  * `loadFailed`je nastaven na `true`, který se používá k zobrazení chybové zprávy uživateli.
  * Chyba je zaznamenána do protokolu.

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Logika vykreslování

Deklarativní označení v `.razor` souboru komponenty je zkompilováno do metody s C# názvem `BuildRenderTree`. Když komponenta vykreslí, `BuildRenderTree` provede a sestaví strukturu dat popisující prvky, text a podřízené komponenty vykreslené komponenty.

Logika vykreslování může vyvolat výjimku. Příklad tohoto scénáře nastane, pokud `@someObject.PropertyName` je vyhodnocen, ale `null` `@someObject` je. Neošetřená výjimka vyvolaná logikou vykreslování je pro okruh závažná.

Chcete-li zabránit výjimce odkazu s hodnotou null v logice `null` vykreslování, vyhledejte objekt před přístupem k jeho členům. V následujícím příkladu nejsou k `person.Address` vlastnostem k dispozici `null`, pokud `person.Address` je:

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Předchozí kód předpokládá, že `person` není `null`. Struktura kódu často zaručuje, že objekt existuje v době, kdy je komponenta vykreslena. V těchto případech není nutné kontrolovat `null` v logice vykreslování. V předchozím příkladu může být `person` zaručeno, že existuje, `person` protože je vytvořena při vytvoření instance komponenty.

### <a name="event-handlers"></a>Obslužné rutiny událostí

Kód na straně klienta vyvolá volání C# kódu při vytváření obslužných rutin událostí pomocí:

* `@onclick`
* `@onchange`
* Další `@on...` atributy
* `@bind`

Kód obslužné rutiny události může v těchto scénářích vyvolat neošetřenou výjimku.

Pokud obslužná rutina události vyvolá neošetřenou výjimku (například databázový dotaz neuspěje), je výjimka pro okruh závažná. Pokud aplikace volá kód, který může selhat z externích důvodů, Zachyťte výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.

Pokud uživatelský kód neprovede soutisk a zpracuje výjimku, rozhraní zaprotokoluje výjimku a ukončí okruh.

### <a name="component-disposal"></a>Vyřazení součásti

Součást může být odebrána z uživatelského rozhraní, například proto, že uživatel přešel na jinou stránku. Při odebrání součásti, která <xref:System.IDisposable?displayProperty=fullName> je implementována z uživatelského rozhraní, rozhraní zavolá <xref:System.IDisposable.Dispose*> metodu komponenty. 

Pokud `Dispose` metoda komponenty vyvolá neošetřenou výjimku, je výjimka pro okruh závažná. Pokud logika vyřazení může vyvolat výjimky, aplikace by měla zachytit výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.

Další informace o vyřazení součástí najdete v <xref:blazor/components#component-disposal-with-idisposable>tématu.

### <a name="javascript-interop"></a>Interoperabilita JavaScriptu

`IJSRuntime.InvokeAsync<T>`umožňuje kódu .NET provádět asynchronní volání prostředí JavaScript Runtime v prohlížeči uživatele.

Následující podmínky se vztahují na zpracování chyb pomocí `InvokeAsync<T>`:

* Pokud volání `InvokeAsync<T>` neproběhne synchronně, dojde k výjimce .NET. Volání `InvokeAsync<T>` my selže, například proto, že zadané argumenty nemohou být serializovány. Kód pro vývojáře musí zachytit výjimku. Pokud kód aplikace v metodě obslužné rutiny události nebo životní cyklus komponenty nezpracovává výjimku, je výsledná výjimka pro okruh závažná.
* Pokud se volání `InvokeAsync<T>` asynchronně nezdařilo, rozhraní .NET <xref:System.Threading.Tasks.Task> se nepovede. Volání `InvokeAsync<T>` může selhat například proto, že kód na straně JavaScriptu vyvolá výjimku nebo `Promise` vrátí hodnotu, která se dokončila jako `rejected`. Kód pro vývojáře musí zachytit výjimku. Při použití operátoru [await](/dotnet/csharp/language-reference/keywords/await) zvažte zabalení volání metody v příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním. V opačném případě selhání kódu způsobí neošetřenou výjimku, která je pro okruh závažná.
* Ve výchozím nastavení volání musí `InvokeAsync<T>` být dokončena v určitou dobu nebo jinak vyprší časový limit volání. Výchozí doba časového limitu je jedna minuta. Časový limit chrání kód proti ztrátě v připojení k síti nebo kódu JavaScriptu, který nikdy neposílá zpět zprávu o dokončení. Pokud vyprší časový limit volání, výsledný `Task` výsledek <xref:System.OperationCanceledException>se nezdařil. Depeše a zpracovává výjimku pomocí protokolování.

Podobně kód JavaScriptu může iniciovat volání metod .NET, které jsou označeny [atributem [JSInvokable]](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions). Pokud tyto metody rozhraní .NET vyvolají neošetřenou výjimku:

* Výjimka není pro okruh považována za závažnou.
* Na straně `Promise` JavaScriptu se zamítlo.

Máte možnost použít kód pro zpracování chyb na straně .NET nebo na straně JavaScriptu volání metody.

Další informace naleznete v tématu <xref:blazor/javascript-interop>.

### <a name="circuit-handlers"></a>Obslužné rutiny okruhu

Blazor umožňuje kódu definovat *obslužnou rutinu okruhu*, která obdrží oznámení v případě, že dojde ke změně stavu okruhu uživatele. Používají se následující stavy:

* `initialized`
* `connected`
* `disconnected`
* `disposed`

Oznámení se spravují pomocí registrace služby di, která dědí z `CircuitHandler` abstraktní základní třídy.

Pokud metody obslužné rutiny vlastního okruhu vyvolávají neošetřenou výjimku, je výjimka pro okruh závažná. Chcete-li tolerovat výjimky v kódu obslužné rutiny nebo volané metody, zabalte kód v jednom nebo více příkazech [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.

### <a name="circuit-disposal"></a>Vyřazení okruhů

Když je okruh ukončený, protože uživatel je odpojený a rozhraní čistí stav okruhu, rozhraní uvolní obor DI okruhu. Při likvidaci oboru se uvolní jakékoli DI služby, které implementují <xref:System.IDisposable?displayProperty=fullName>okruhy s rozsahem. Pokud jakákoli služba DI vyvolá neošetřenou výjimku při vyřazení, rozhraní zaprotokoluje výjimku.

### <a name="prerendering"></a>Předvykreslování

Komponenty Blazor lze předem vykreslovat pomocí `Html.RenderComponentAsync` , aby byly vykreslené značky HTML vráceny jako součást počátečního požadavku HTTP uživatele. Funguje to takto:

* Vytváří se nový okruh obsahující všechny předem vydané komponenty, které jsou součástí stejné stránky.
* Generování počátečního kódu HTML.
* Okruh se zpracovává, `disconnected` až do chvíle, kdy prohlížeč uživatele vytvoří připojení ke stejnému serveru, aby obnovil činnost daného okruhu.

Pokud nějaká komponenta vyvolá neošetřenou výjimku při předvykreslování, například během metody životního cyklu nebo v logice vykreslování:

* Výjimka je pro okruh závažná.
* Výjimka vyvolá zásobník volání ze `Html.RenderComponentAsync` volání. Proto se celý požadavek HTTP nezdařil, pokud není výjimka výslovně zachycena kódem vývojáře.

Za běžných okolností, když se předvykreslování nepovede, pokračuje sestavení a vykreslení komponenty nesmysl, protože pracovní komponenta se nedá vykreslit.

Chcete-li tolerovat chyby, ke kterým může dojít při předvykreslování, musí být logika zpracování chyb umístěna v rámci součásti, která může vyvolat výjimky. Použijte příkazy [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s ošetřením a protokolováním chyb. Namísto zabalení volání `RenderComponentAsync` do `try-catch` v příkazu, umístěte logiku zpracování chyb `RenderComponentAsync`do komponenty vygenerované.

## <a name="advanced-scenarios"></a>Pokročilé scénáře

### <a name="recursive-rendering"></a>Rekurzivní vykreslování

Komponenty lze rekurzivně vnořovat. To je užitečné pro reprezentaci rekurzivních datových struktur. Například `TreeNode` komponenta může vykreslovat více `TreeNode` komponent pro každý podřízený uzel.

Při rekurzivním vykreslování Vyhněte se vzorům kódování, které vedou k nekonečné rekurzi:

* Neprovádějte rekurzivní vykreslování struktury dat, která obsahuje cyklus. Například nevykreslují uzel stromu, jehož podřízené položky zahrnují sám sebe.
* Nevytvářejte řetěz rozložení, které obsahují cyklus. Nevytvářejte například rozložení, jehož rozložení je samotné.
* Neumožněte koncovému uživateli narušovat invariantní rekurzivních dat (pravidla) prostřednictvím škodlivých vstupů dat nebo volání interoperability JavaScriptu.

Nekonečná smyčka během vykreslování:

* Způsobí, že proces vykreslování bude trvale pokračovat.
* Je ekvivalentní vytvořit neukončenou smyčku.

V těchto scénářích přestává ovlivněný okruh a vlákno se obvykle pokouší:

* Spotřebujte tolik času procesoru povolený operačním systémem, a to po dobu neurčitou.
* Spotřebovává neomezený objem paměti serveru. Spotřebovávání neomezené paměti je ekvivalentem k situaci, kdy neukončená smyčka přidá položky do kolekce při každé iteraci.

Aby se zabránilo nekonečným vzorům rekurzování, ujistěte se, že kód rekurzivního vykreslování obsahuje vhodné podmínky

### <a name="custom-render-tree-logic"></a>Vlastní logika stromu vykreslování

Většina komponent Blazor je implementována jako soubory *. Razor* a je zkompilována k vytvoření logiky, `RenderTreeBuilder` která pracuje s a vykreslí výstup. Vývojář může ručně implementovat `RenderTreeBuilder` logiku pomocí procedurálního C# kódu. Další informace naleznete v tématu <xref:blazor/components#manual-rendertreebuilder-logic>.

> [!WARNING]
> Použití logiky tvůrce stromu ručního vykreslování je považováno za pokročilý a nebezpečný scénář, nedoporučuje se pro obecný vývoj komponent.

Pokud `RenderTreeBuilder` je napsán kód, vývojář musí zaručit správnost kódu. Vývojář například musí zajistit, aby:

* `OpenElement` Volání a `CloseElement` jsou správně vyvážená.
* Atributy se přidávají jenom na správných místech.

Nesprávná logika tvůrce stromu ručního vykreslování může způsobit libovolné nedefinované chování, včetně havárií, zablokování serveru a ohrožení zabezpečení.

Zvažte ruční vykreslování logiky tvůrce stromu na stejné úrovni složitosti a se stejnou úrovní *nebezpečí* při psaní kódu sestavení nebo instrukcí jazyka MSIL ručně.
