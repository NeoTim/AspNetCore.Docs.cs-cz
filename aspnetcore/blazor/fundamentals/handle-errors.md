---
title: Zpracování chyb v Blazor aplikacích ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core Blazor způsob, jakým Blazor spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: e777991f4cbfd22b441fb198144bbdf023b4df6b
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242781"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Zpracování chyb v Blazor aplikacích ASP.NET Core

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)

Tento článek popisuje Blazor , jak spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.

## <a name="detailed-errors-during-development"></a>Podrobné chyby při vývoji

Když Blazor aplikace nefunguje správně během vývoje, při řešení potíží a řešení těchto potíží získá podrobné informace o chybě z aplikace. Když dojde k chybě, Blazor aplikace zobrazí v dolní části obrazovky žlutý pruh:

* Během vývoje se zlatý panel vás přesměruje na konzolu prohlížeče, kde vidíte výjimku.
* V produkčním okně upozorňuje uživatel, že došlo k chybě, a doporučuje aktualizovat prohlížeč.

Uživatelské rozhraní tohoto prostředí pro zpracování chyb je součástí Blazor šablon projektu.

V Blazor aplikaci pro WebAssembly, přizpůsobte si prostředí v `wwwroot/index.html` souboru:

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

V Blazor aplikaci serveru upravte prostředí v `Pages/_Host.cshtml` souboru:

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

`blazor-error-ui`Element je skrytý styly obsaženými v Blazor šablonách ( `wwwroot/css/site.css` ) a pak se zobrazí, když dojde k chybě:

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a>Jak Blazor Serverová aplikace reaguje na neošetřené výjimky

BlazorServer je stavový rámec. I když uživatelé pracují s aplikací, udržují připojení k serveru známému jako *okruh*. Okruh obsahuje aktivní instance komponent a mnoho dalších aspektů stavu, například:

* Poslední Vykreslený výstup komponent.
* Aktuální sada delegátů zpracovávajících události, které mohou být aktivovány událostmi na straně klienta.

Pokud uživatel otevře aplikaci na více kartách prohlížeče, má několik nezávislých okruhů.

Blazorzpracovává většinu neošetřených výjimek jako závažného okruhu, ve kterém se vyskytují. Pokud je okruh ukončen z důvodu neošetřené výjimky, uživatel může pokračovat v interakci s aplikací pouze tak, že znovu načte stránku, aby vytvořila nový okruh. Nejsou ovlivněny okruhy mimo tu, která je ukončená, což jsou okruhy pro ostatní uživatele nebo jiné karty prohlížeče. Tento scénář je podobný aplikaci klasické pracovní plochy, při které dojde k chybě. Chybná aplikace se musí restartovat, ale ostatní aplikace to neovlivní.

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

Pokud dojde k neošetřené výjimce, je výjimka zaznamenána do <xref:Microsoft.Extensions.Logging.ILogger> instancí nakonfigurovaných v kontejneru služby. Ve výchozím nastavení se Blazor aplikace protokolují do výstupu konzoly s poskytovatelem protokolování konzoly. Zvažte možnost protokolování do trvalého umístění u poskytovatele, který spravuje velikost protokolu a rotaci protokolů. Další informace naleznete v tématu <xref:fundamentals/logging/index>.

Během vývoje Blazor obvykle posílá kompletní informace o výjimkách do konzoly prohlížeče, aby bylo možné v ladění. V produkčním prostředí jsou podrobné chyby v konzole prohlížeče ve výchozím nastavení zakázané, což znamená, že se do klientů neodesílají chyby, ale úplné podrobnosti o výjimce se pořád protokolují na straně serveru. Další informace naleznete v tématu <xref:fundamentals/error-handling>.

Musíte se rozhodnout, které incidenty se mají protokolovat, a úroveň závažnosti protokolovaných incidentů. Nepřátelským uživatelům může být možné aktivovat chyby záměrně. Například Neprotokolujte incident z chyby, kde `ProductId` je uvedena neznámá adresa URL komponenty, která zobrazuje podrobnosti o produktu. Ne všechny chyby by se měly považovat za incidenty s vysokou závažností pro protokolování.

Další informace naleznete v tématu <xref:blazor/fundamentals/logging>.

## <a name="places-where-errors-may-occur"></a>Místa, kde může dojít k chybám

Rozhraní a kód aplikace mohou aktivovat neošetřené výjimky v žádném z následujících umístění:

* [Instance komponenty](#component-instantiation)
* [Metody životního cyklu](#lifecycle-methods)
* [Logika vykreslování](#rendering-logic)
* [Obslužné rutiny událostí](#event-handlers)
* [Vyřazení součásti](#component-disposal)
* [Interoperabilita JavaScriptu](#javascript-interop)
* [BlazorRevykreslování serveru](#blazor-server-prerendering)

Předchozí neošetřené výjimky jsou popsány v následujících částech tohoto článku.

### <a name="component-instantiation"></a>Instance komponenty

Při Blazor vytváření instance komponenty:

* Je vyvolán konstruktor součásti.
* Jsou vyvolány konstruktory jakékoli nejednoznačné služby DI Services dodávané do konstruktoru komponenty prostřednictvím [`@inject`](xref:mvc/views/razor#inject) direktivy nebo [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) atributu.

BlazorServerový okruh se nezdařil, pokud kterýkoli z spouštěného konstruktoru nebo setter pro jakoukoliv `[Inject]` vlastnost vyvolá neošetřenou výjimku. Výjimka je závažná, protože architektura nemůže vytvořit instanci komponenty. Pokud logika konstruktoru může vyvolat výjimky, aplikace by měla zachytit výjimky pomocí [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) příkazu s zpracováním chyb a protokolováním.

### <a name="lifecycle-methods"></a>Metody životního cyklu

Během životnosti komponenty Blazor vyvolá následující [metody životního cyklu](xref:blazor/components/lifecycle):

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

Pokud jakákoli metoda životního cyklu vyvolá výjimku synchronně nebo asynchronně, je výjimka závažná pro Blazor okruh serveru. Pro součásti, které se zabývat chybami v metodách životního cyklu, přidejte logiku zpracování chyb.

V následujícím příkladu, který <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> volá metodu pro získání produktu:

* Výjimka vyvolaná v `ProductRepository.GetProductByIdAsync` metodě je zpracována [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) příkazem.
* Po `catch` spuštění bloku:
  * `loadFailed`je nastaven na `true` , který se používá k zobrazení chybové zprávy uživateli.
  * Chyba je zaznamenána do protokolu.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Logika vykreslování

Deklarativní označení v `.razor` souboru komponenty je zkompilováno do metody jazyka C# s názvem <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> . Když komponenta vykreslí, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> provede a sestaví strukturu dat popisující prvky, text a podřízené komponenty vykreslené komponenty.

Logika vykreslování může vyvolat výjimku. Příklad tohoto scénáře nastane `@someObject.PropertyName` , pokud je vyhodnocen `@someObject` , ale je `null` . Neošetřená výjimka vyvolaná logikou vykreslování je závažná pro Blazor okruh serveru.

Chcete-li zabránit výjimce odkazu s hodnotou null v logice vykreslování, vyhledejte `null` objekt před přístupem k jeho členům. V následujícím příkladu `person.Address` nejsou k vlastnostem k dispozici, pokud `person.Address` je `null` :

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Předchozí kód předpokládá, že `person` není `null` . Struktura kódu často zaručuje, že objekt existuje v době, kdy je komponenta vykreslena. V těchto případech není nutné kontrolovat `null` v logice vykreslování. V předchozím příkladu `person` může být zaručeno, že existuje, protože `person` je vytvořena při vytvoření instance komponenty.

### <a name="event-handlers"></a>Obslužné rutiny událostí

Kód na straně klienta vyvolá volání kódu jazyka C# při vytváření obslužných rutin událostí pomocí:

* `@onclick`
* `@onchange`
* Další `@on...` atributy
* `@bind`

Kód obslužné rutiny události může v těchto scénářích vyvolat neošetřenou výjimku.

Pokud obslužná rutina události vyvolá neošetřenou výjimku (například databázový dotaz neuspěje), je výjimka závažná pro Blazor okruh serveru. Pokud aplikace volá kód, který může selhat z externích důvodů, zachytávání výjimek pomocí [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) příkazu s zpracováním chyb a protokolováním.

Pokud uživatelský kód neprovede soutisk a zpracuje výjimku, rozhraní zaprotokoluje výjimku a ukončí okruh.

### <a name="component-disposal"></a>Vyřazení součásti

Součást může být odebrána z uživatelského rozhraní, například proto, že uživatel přešel na jinou stránku. Při odebrání součásti, která <xref:System.IDisposable?displayProperty=fullName> je implementována z uživatelského rozhraní, rozhraní zavolá metodu komponenty <xref:System.IDisposable.Dispose%2A> .

Pokud `Dispose` metoda komponenty vyvolá neošetřenou výjimku, je výjimka závažná pro Blazor okruh serveru. Pokud logika vyřazení může vyvolat výjimky, aplikace by měla zachytit výjimky pomocí [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) příkazu s zpracováním chyb a protokolováním.

Další informace o vyřazení součástí najdete v tématu <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

### <a name="javascript-interop"></a>Interoperabilita JavaScriptu

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>umožňuje kódu .NET provádět asynchronní volání prostředí JavaScript Runtime v prohlížeči uživatele.

Následující podmínky se vztahují na zpracování chyb pomocí <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :

* Pokud volání <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> neproběhne synchronně, dojde k výjimce .NET. Volání <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> může selhat například proto, že zadané argumenty nemohou být serializovány. Kód pro vývojáře musí zachytit výjimku. Pokud kód aplikace v metodě obslužné rutiny události nebo životní cyklus komponenty nezpracovává výjimku, je výsledná výjimka závažná pro Blazor okruh serveru.
* Pokud se volání <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> asynchronně nezdařilo, rozhraní .NET se nepovede <xref:System.Threading.Tasks.Task> . Volání <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> může selhat například proto, že kód na straně JavaScriptu vyvolá výjimku nebo vrátí hodnotu `Promise` , která se dokončila jako `rejected` . Kód pro vývojáře musí zachytit výjimku. Při použití [`await`](/dotnet/csharp/language-reference/keywords/await) operátoru zvažte zabalení volání metody v [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) příkazu s zpracováním chyb a protokolováním. V opačném případě selhání kódu způsobí neošetřenou výjimku, která je závažná pro Blazor okruh serveru.
* Ve výchozím nastavení volání <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> musí být dokončena v určitou dobu nebo jinak vyprší časový limit volání. Výchozí doba časového limitu je jedna minuta. Časový limit chrání kód proti ztrátě v připojení k síti nebo kódu JavaScriptu, který nikdy neposílá zpět zprávu o dokončení. Pokud vyprší časový limit volání, výsledný výsledek <xref:System.Threading.Tasks> se nezdařil <xref:System.OperationCanceledException> . Depeše a zpracovává výjimku pomocí protokolování.

Podobně kód JavaScriptu může iniciovat volání metod .NET, které jsou označeny [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) atributem. Pokud tyto metody rozhraní .NET vyvolají neošetřenou výjimku:

* Výjimka není považována za závažnou pro Blazor okruh serveru.
* Na straně JavaScriptu `Promise` se zamítlo.

Máte možnost použít kód pro zpracování chyb na straně .NET nebo na straně JavaScriptu volání metody.

Další informace najdete v následujících článcích:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>BlazorPředvykreslování serveru

Blazorkomponenty mohou být předem vykresleny pomocí [pomocníka tag komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , aby byly vykreslené značky HTML vráceny jako součást počátečního požadavku HTTP uživatele. Funguje to takto:

* Vytváření nového okruhu pro všechny předem vykreslené komponenty, které jsou součástí stejné stránky.
* Generování počátečního kódu HTML.
* Okruh se zpracovává, `disconnected` dokud prohlížeč uživatele nevytvoří SignalR připojení zpátky ke stejnému serveru. Po navázání spojení se v okruhu obnoví interakce mezi aktivitami a kód HTML značek se aktualizuje.

Pokud nějaká komponenta vyvolá neošetřenou výjimku při předvykreslování, například během metody životního cyklu nebo v logice vykreslování:

* Výjimka je pro okruh závažná.
* Výjimka vyvolá zásobník volání z <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocné rutiny značky. Proto se celý požadavek HTTP nezdařil, pokud není výjimka výslovně zachycena kódem vývojáře.

Za běžných okolností, když se předvykreslování nepovede, pokračuje sestavení a vykreslení komponenty nesmysl, protože pracovní komponenta se nedá vykreslit.

Chcete-li tolerovat chyby, ke kterým může dojít při předvykreslování, musí být logika zpracování chyb umístěna v rámci součásti, která může vyvolat výjimky. Použijte [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) příkazy s zpracováním a protokolováním chyb. Místo balení <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocníka značek v [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) příkazu umístěte logiku zpracování chyb do komponenty vygenerované <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocníkem značek.

## <a name="advanced-scenarios"></a>Pokročilé scénáře

### <a name="recursive-rendering"></a>Rekurzivní vykreslování

Komponenty lze rekurzivně vnořovat. To je užitečné pro reprezentaci rekurzivních datových struktur. Například `TreeNode` Komponenta může vykreslovat více `TreeNode` komponent pro každý podřízený uzel.

Při rekurzivním vykreslování Vyhněte se vzorům kódování, které vedou k nekonečné rekurzi:

* Neprovádějte rekurzivní vykreslování struktury dat, která obsahuje cyklus. Například nevykreslují uzel stromu, jehož podřízené položky zahrnují sám sebe.
* Nevytvářejte řetěz rozložení, které obsahují cyklus. Nevytvářejte například rozložení, jehož rozložení je samotné.
* Neumožněte koncovému uživateli narušovat invariantní rekurzivních dat (pravidla) prostřednictvím škodlivých vstupů dat nebo volání interoperability JavaScriptu.

Nekonečná smyčka během vykreslování:

* Způsobí, že proces vykreslování bude trvale pokračovat.
* Je ekvivalentní vytvořit neukončenou smyčku.

V těchto scénářích dojde k Blazor chybě ovlivněného okruhu serveru a vlákno se obvykle pokouší:

* Spotřebujte tolik času procesoru povolený operačním systémem, a to po dobu neurčitou.
* Spotřebovává neomezený objem paměti serveru. Spotřebovávání neomezené paměti je ekvivalentem k situaci, kdy neukončená smyčka přidá položky do kolekce při každé iteraci.

Aby se zabránilo nekonečným vzorům rekurzování, ujistěte se, že kód rekurzivního vykreslování obsahuje vhodné podmínky

### <a name="custom-render-tree-logic"></a>Vlastní logika stromu vykreslování

Většina Blazor komponent je implementována jako `.razor` soubory a je zkompilována k vytvoření logiky, která pracuje s a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> vykreslí výstup. Vývojář může logiku ručně implementovat <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> pomocí procedurálního kódu jazyka C#. Další informace naleznete v tématu <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> Použití logiky tvůrce stromu ručního vykreslování je považováno za pokročilý a nebezpečný scénář, nedoporučuje se pro obecný vývoj komponent.

Pokud <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> je napsán kód, vývojář musí zaručit správnost kódu. Vývojář například musí zajistit, aby:

* Volání <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> jsou správně vyvážená.
* Atributy se přidávají jenom na správných místech.

Nesprávná logika tvůrce stromu ručního vykreslování může způsobit libovolné nedefinované chování, včetně havárií, zablokování serveru a ohrožení zabezpečení.

Zvažte ruční vykreslování logiky tvůrce stromu na stejné úrovni složitosti a se stejnou úrovní *nebezpečí* při psaní kódu sestavení nebo instrukcí jazyka MSIL ručně.
