---
title: Zpracování chyb v Blazor aplikacích ASP.NET Core
author: guardrex
description: Zjistěte, Blazor jak Blazor ASP.NET Core spravuje neošetřené výjimky a jak vyvíjet aplikace, které detekují a zpracovávají chyby.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382272"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>Zpracování chyb v Blazor aplikacích ASP.NET Core

Podle [Steve Sanderson](https://github.com/SteveSandersonMS)

Tento článek popisuje, jak Blazor spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.

## <a name="detailed-errors-during-development"></a>Podrobné chyby během vývoje

Když Blazor aplikace během vývoje nefunguje správně, zobrazení podrobných informací o chybách z aplikace pomáhá při řešení potíží a řešení problému. Když dojde k Blazor chybě, aplikace zobrazí zlatý pruh v dolní části obrazovky:

* Během vývoje vás zlatá lišta přesměruje na konzolu prohlížeče, kde můžete vidět výjimku.
* Ve výrobě zlatý pruh upozorní uživatele, že došlo k chybě a doporučuje aktualizovat prohlížeč.

Uživatelské rozhraní pro toto prostředí zpracování Blazor chyb je součástí šablony projektu.

V Blazor aplikaci WebAssembly přizpůsobte prostředí v *wwwroot/index.html* souboru:

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

V Blazor aplikaci Server přizpůsobte prostředí v souboru *Pages/_Host.cshtml:*

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

Prvek `blazor-error-ui` je skryt styly Blazor obsaženými v šablonách *(wwwroot/css/site.css)* a poté zobrazen, když dojde k chybě:

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Jak Blazor serverová aplikace reaguje na neošetřené výjimky

BlazorServer je stavové rozhraní. Zatímco uživatelé pracují s aplikací, udržují připojení k serveru známému jako *okruh*. Obvod obsahuje instance aktivní součásti a mnoho dalších aspektů stavu, například:

* Nejnovější vykreslený výstup komponent.
* Aktuální sada delegátů zpracování událostí, které by mohly být vyvolány událostmi na straně klienta.

Pokud uživatel otevře aplikaci na více kartách prohlížeče, má více nezávislých obvodů.

Blazorzachází s většinou neošetřených výjimek jako fatální pro okruh, kde k nim dochází. Pokud je okruh ukončen z důvodu neošetřené výjimky, uživatel může pokračovat v interakci s aplikací pouze opětovným načtením stránky a vytvořit nový okruh. Obvody mimo ten, který je ukončen, což jsou obvody pro ostatní uživatele nebo jiné karty prohlížeče, nejsou ovlivněny. Tento scénář je podobný desktopové&mdash;aplikaci, která havaruje, že havarovaná aplikace musí být restartována, ale ostatní aplikace nejsou ovlivněny.

Okruh je ukončen, pokud dojde k neošetřené výjimce z následujících důvodů:

* Neošetřená výjimka často ponechá okruh v nedefinovaném stavu.
* Normální provoz aplikace nelze zaručit po neošetřené výjimce.
* Pokud okruh pokračuje, mohou se v aplikaci objevit chyby zabezpečení.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Správa neošetřených výjimek v kódu vývojáře

Aby aplikace mohla pokračovat po chybě, musí mít logiku zpracování chyb. Další části tohoto článku popisují potenciální zdroje neošetřených výjimek.

V produkčním prostředí nevykresluj zprávy o výjimkách rozhraní nebo trasování zásobníku v unovém rozhraní. Vykreslování zpráv výjimek nebo trasování zásobníku může:

* Zpřístupňte citlivé informace koncovým uživatelům.
* Pomozte uživateli se zlými úmysly odhalit slabiny v aplikaci, které mohou ohrozit zabezpečení aplikace, serveru nebo sítě.

## <a name="log-errors-with-a-persistent-provider"></a>Protokolovat chyby u trvalého zprostředkovatele

Pokud dojde k neošetřené výjimce, <xref:Microsoft.Extensions.Logging.ILogger> je výjimka zaznamenána do instancí nakonfigurovaných v kontejneru služby. Ve výchozím Blazor nastavení se aplikace přihlašují ke výstupu konzoly pomocí zprostředkovatele protokolování konzoly. Zvažte protokolování do trvalejšího umístění s poskytovatelem, který spravuje velikost protokolu a střídání protokolů. Další informace naleznete v tématu <xref:fundamentals/logging/index>.

Během vývoje Blazor obvykle odešle úplné podrobnosti o výjimkách do konzole prohlížeče na pomoc při ladění. V produkčním prostředí jsou podrobné chyby v konzole prohlížeče ve výchozím nastavení zakázány, což znamená, že chyby nejsou odesílány klientům, ale úplné podrobnosti výjimky jsou stále zaznamenány na straně serveru. Další informace naleznete v tématu <xref:fundamentals/error-handling>.

Musíte se rozhodnout, které incidenty se mají protokolovat a úroveň závažnosti zaznamenaných incidentů. Nepřátelští uživatelé mohou být schopni spustit chyby úmyslně. Nezahlcovat například incident z chyby, kdy je v adrese URL komponenty, která zobrazuje podrobnosti o produktu, zadánneznámý `ProductId` případ. Ne všechny chyby by měly být považovány za incidenty s vysokou závažností pro protokolování.

## <a name="places-where-errors-may-occur"></a>Místa, kde může dojít k chybám

Architektura a kód aplikace může vyvolat neošetřené výjimky v některém z následujících umístění:

* [Vytvoření instance komponenty](#component-instantiation)
* [Metody životního cyklu](#lifecycle-methods)
* [Vykreslovací logika](#rendering-logic)
* [Obslužné rutiny událostí](#event-handlers)
* [Likvidace součástí](#component-disposal)
* [Interoperabilita JavaScriptu](#javascript-interop)
* [BlazorVykreslení serveru](#blazor-server-prerendering)

Předchozí neošetřené výjimky jsou popsány v následujících částech tohoto článku.

### <a name="component-instantiation"></a>Vytvoření instance komponenty

Když Blazor vytvoříte instanci komponenty:

* Konstruktor komponenty je vyvolán.
* Konstruktory všech služeb di singleton DI dodávané konstruktoru [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) komponenty [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) prostřednictvím směrnice nebo atributu jsou vyvolány.

Server Blazor okruh selže, když všechny provedené konstruktor `[Inject]` nebo setter pro libovolnou vlastnost vyvolá neošetřené výjimky. Výjimka je závažná, protože rozhraní framework nelze vytvořit instanci součásti. Pokud logika konstruktoru může vyvolat výjimky, aplikace by měla soutisk výjimky pomocí [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) prohlášení s zpracování chyb a protokolování.

### <a name="lifecycle-methods"></a>Metody životního cyklu

Během životnosti komponenty Blazor vyvolá následující [metody životního cyklu](xref:blazor/lifecycle):

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Pokud jakákoli metoda životního cyklu vyvolá výjimku synchronně nebo asynchronně, výjimka Blazor je závažná pro serverový obvod. Pro součásti řešit chyby v metodách životního cyklu, přidejte logiku zpracování chyb.

V následujícím příkladu, kde `OnParametersSetAsync` volá metodu k získání produktu:

* Výjimka vyvolána `ProductRepository.GetProductByIdAsync` v metodě `try-catch` je zpracována příkazem.
* Při `catch` spuštění bloku:
  * `loadFailed`je nastavena na `true`, která slouží k zobrazení chybové zprávy uživateli.
  * Chyba je zaznamenána.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Vykreslovací logika

Deklarativní značky v souboru `.razor` komponenty jsou `BuildRenderTree`zkompilovány do metody C# s názvem . Když komponenta vykreslí, `BuildRenderTree` spustí a vytvoří datovou strukturu popisující prvky, text a podřízené součásti vykreslené součásti.

Vykreslovací logika může vyvolat výjimku. Příklad tohoto scénáře nastane, `@someObject.PropertyName` když `@someObject` `null`je vyhodnocena, ale je . Neošetřená výjimka vyzdvižená logikou vykreslování je pro obvod serveru závažná. Blazor

Chcete-li zabránit výjimku nulového odkazu `null` v vykreslovací logice, zkontrolujte objekt před přístupem k jeho členům. V následujícím příkladu nejsou vlastnosti `person.Address` `person.Address` přístupné, pokud je `null`:

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Předchozí kód předpokládá, `person` že není `null`. Struktura kódu často zaručuje, že objekt existuje v době vykreslení komponenty. V těchto případech není nutné zkontrolovat `null` v logice vykreslování. V předchozím příkladu může být zaručeno, že existuje, `person` protože `person` je vytvořen při vytvoření instance komponenty.

### <a name="event-handlers"></a>Obslužné rutiny událostí

Kód na straně klienta aktivuje vyvolání kódu jazyka C# při vytváření obslužných rutin událostí pomocí:

* `@onclick`
* `@onchange`
* Další `@on...` atributy
* `@bind`

Kód obslužné rutiny události může vyvolat neošetřenou výjimku v těchto scénářích.

Pokud obslužná rutina události vyvolá neošetřenou výjimku (například Blazor databázový dotaz selže), výjimka je závažná pro serverový obvod. Pokud aplikace volá kód, který může selhat z externích důvodů, soutisk výjimky pomocí [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) prohlášení s zpracování chyb a protokolování.

Pokud uživatelský kód není soutisk a zpracování výjimky, rozhraní protokoluje výjimku a ukončí okruh.

### <a name="component-disposal"></a>Likvidace součástí

Komponenta může být odebrána z uživatelského rozhraní, například proto, že uživatel přejde na jinou stránku. Když je komponenta, která implementuje, <xref:System.IDisposable?displayProperty=fullName> odebrána z <xref:System.IDisposable.Dispose*> ui, rozhraní nazývá metodu komponenty.

Pokud `Dispose` metoda komponenty vyvolá neošetřenou výjimku, výjimka Blazor je závažná pro obvod serveru. Pokud logika likvidace může vyvolat výjimky, aplikace by měla soutisk výjimky pomocí [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) prohlášení s zpracování chyb a protokolování.

Další informace o likvidaci <xref:blazor/lifecycle#component-disposal-with-idisposable>součástí naleznete v tématu .

### <a name="javascript-interop"></a>Interoperabilita JavaScriptu

`IJSRuntime.InvokeAsync<T>`umožňuje kódu .NET provádět asynchronní volání modulu runtime javascriptu v prohlížeči uživatele.

Následující podmínky platí pro `InvokeAsync<T>`zpracování chyb s :

* Pokud volání `InvokeAsync<T>` nezdaří synchronně, dojde k výjimce .NET. Volání může `InvokeAsync<T>` selhat, například protože zadané argumenty nelze serializovat. Kód vývojáře musí zachytit výjimku. Pokud kód aplikace v obslužné rutině události nebo metodě životního cyklu Blazor komponenty nezpracovává výjimku, výsledná výjimka je pro obvod serveru závažná.
* Pokud volání `InvokeAsync<T>` nezdaří asynchronně, .NET <xref:System.Threading.Tasks.Task> selže. Volání může `InvokeAsync<T>` selhat, například proto, že kód na straně `Promise` JavaScript `rejected`vyvolá výjimku nebo vrátí, který byl dokončen jako . Kód vývojáře musí zachytit výjimku. Pokud používáte operátor [await,](/dotnet/csharp/language-reference/keywords/await) zvažte zabalení volání metody v příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s chybovým zpracováním a protokolováním. V opačném případě selhání kódu má za následek neošetřené výjimky, která je závažná pro server obvodu. Blazor
* Ve výchozím nastavení `InvokeAsync<T>` musí být volání dokončeno v určité lhůtě, jinak je čas volání out. Výchozí časový limit je jedna minuta. Časový čas chrání kód před ztrátou připojení k síti nebo kódu Jazyka JavaScript, který nikdy neodešle zpět zprávu o dokončení. Pokud je časový čas volání, `Task` výsledné <xref:System.OperationCanceledException>selže s . Soutisk a zpracování výjimky s protokolováním.

Podobně javascriptový kód může iniciovat volání [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) metod .NET označených atributem. Pokud tyto metody .NET vyvolat neošetřené výjimky:

* Výjimka není považována za závažnou Blazor pro serverový okruh.
* JavaScript-strana `Promise` je odmítnuta.

Máte možnost použít kód zpracování chyb na straně .NET nebo javascriptové straně volání metody.

Další informace najdete v těchto článcích:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>BlazorPředběžné vykreslování serveru

Blazorsoučásti mohou být předem vykresleny pomocí [pomocníka pro značku komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) tak, aby jejich vykreslené značky HTML byly vráceny jako součást počátečního požadavku HTTP uživatele. To funguje takto:

* Vytvoření nového okruhu pro všechny předvykreslované součásti, které jsou součástí stejné stránky.
* Generování počátečního KÓDU HTML.
* Zacházet s `disconnected` okruhem jako do prohlížeče SignalR uživatele naváže připojení zpět na stejný server. Po navázání připojení je obnovena interaktivita na okruhu a aktualizovány značky HTML komponent.

Pokud některé součásti vyvolá neošetřené výjimky během prerendering, například během metody životního cyklu nebo v logice vykreslování:

* Výjimka je fatální pro obvod.
* Výjimka je vyvolána zásobníku `Component` volání z pomocníka tageru. Proto se nezdaří celý požadavek HTTP, pokud výjimka je explicitně zachycena kódem vývojáře.

Za normálních okolností při předběžném vykreslování selže, pokračování v sestavení a vykreslení komponenty nedává smysl, protože pracovní součást nelze vykreslit.

Chcete-li tolerovat chyby, které mohou nastat během předběžného vykreslování, musí být logika zpracování chyb umístěna uvnitř součásti, která může vyvolat výjimky. Pomocí příkazů [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) se zpracováním chyb a protokolováním. Místo zabalení `Component` pomocníka tagu do příkazu `try-catch` umístěte logiku zpracování `Component` chyb do komponenty vykreslené pomocníkem značek.

## <a name="advanced-scenarios"></a>Pokročilé scénáře

### <a name="recursive-rendering"></a>Rekurzivní vykreslování

Součásti mohou být vnořeny rekurzivně. To je užitečné pro rekurzivní datové struktury. Komponenta `TreeNode` může například `TreeNode` vykreslit více součástí pro každou podřízené součásti uzlu.

Při rekurzivním vykreslování se vyhněte vzorcům kódování, které vedou k nekonečné rekurzi:

* Neobnovujte datovou strukturu, která obsahuje cyklus. Například nevykreslovat uzel stromu, jehož podřízené položky zahrnuje sám.
* Nevytvářejte řetězec rozvržení, které obsahují cyklus. Nevytvářejte například rozložení, jehož rozložení je samo o sobě.
* Nedovolte koncovému uživateli porušovat rekurzní invarianty (pravidla) prostřednictvím zadávání škodlivých dat nebo volání interop javascriptu.

Nekonečné smyčky při vykreslování:

* Způsobí, že proces vykreslování bude pokračovat navždy.
* Je ekvivalentní k vytvoření neukončené smyčky.

V těchto scénářích Blazor se nezdaří ohrožený server obvod u tváře a podproces se obvykle pokusí:

* Spotřebovávat tolik času procesoru, kolik je povoleno operačním systémem, po neomezenou dobu.
* Spotřebovávat neomezené množství paměti serveru. Spotřebovávající neomezenou paměť je ekvivalentní scénář, kde neukončené smyčky přidá položky do kolekce na každé iteraci.

Chcete-li se vyhnout nekonečné rekurze vzory, ujistěte se, že rekurzivní vykreslování kód obsahuje vhodné podmínky zastavení.

### <a name="custom-render-tree-logic"></a>Vlastní logika stromu vykreslení

Většina Blazor komponent jsou implementovány jako *.razor* soubory a jsou `RenderTreeBuilder` kompilovány k vytvoření logiky, která pracuje na vykreslení jejich výstup. Vývojář může ručně `RenderTreeBuilder` implementovat logiku pomocí procedurálního kódu Jazyka C#. Další informace naleznete v tématu <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> Použití ruční logiky vytváření vykreslování stromu je považován za pokročilý a nebezpečný scénář, nedoporučuje se pro vývoj obecných součástí.

Pokud `RenderTreeBuilder` je kód zapsán, vývojář musí zaručit správnost kódu. Vývojář musí například zajistit, aby:

* Volání `OpenElement` a `CloseElement` jsou správně vyvážené.
* Atributy jsou přidávány pouze na správných místech.

Nesprávné ruční vykreslení stromu tvůrce logiky může způsobit libovolné nedefinované chování, včetně selhání, server zablokuje a slabá místa zabezpečení.

Zvažte ruční vykreslovat strom stavitel logiku na stejné úrovni složitosti a se stejnou úrovní *nebezpečí* jako psaní kódu sestavení nebo msil pokyny ručně.
