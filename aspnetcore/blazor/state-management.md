---
title: BlazorSpráva stavu ASP.NET Core
author: guardrex
description: Přečtěte si, jak zachovat stav v Blazor Server aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: a74f056447839c4cf057948f26a9ece9b5799656
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606711"
---
# <a name="aspnet-core-no-locblazor-state-management"></a>BlazorSpráva stavu ASP.NET Core

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS) a [Luke Latham](https://github.com/guardrex)

::: zone pivot="webassembly"

Stav uživatele vytvořený v Blazor WebAssembly aplikaci je uložený v paměti prohlížeče.

Mezi příklady stavu uživatele v paměti prohlížeče patří:

* Hierarchie instancí komponent a jejich poslední výstup vykreslování ve vykresleném uživatelském rozhraní.
* Hodnoty polí a vlastností v instancích součástí.
* Data uchovávaná v instancích služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .
* Hodnoty nastavené prostřednictvím volání [interoperability JavaScript](xref:blazor/call-javascript-from-dotnet) .

Když uživatel zavře a znovu otevře prohlížeč nebo znovu načte stránku, stav uživatele uložený v paměti prohlížeče bude ztracen.

## <a name="persist-state-across-browser-sessions"></a>Trvalý stav napříč relacemi prohlížeče

Obecně platí, že se udržuje stav napříč relacemi prohlížeče, kde uživatelé aktivně vytvářejí data a nečtou jenom data, která už existují.

Aby bylo možné zachovat stav napříč relacemi prohlížeče, musí aplikace uchovávat data do jiného umístění úložiště než do paměti prohlížeče. Trvalost stavu není automatické. Je nutné provést kroky při vývoji aplikace k implementaci stavové trvalosti dat.

Trvalost dat se obvykle vyžaduje jenom pro stav vysoké hodnoty, který uživatelé vynaložené úsilím vytvořit. V následujících příkladech trvalého stavu ušetříte čas nebo pomůcky při komerčních činnostech:

* Webové formuláře s více kroky: časově náročné pro uživatele, aby znovu zadal data pro několik dokončených kroků webového formuláře s více kroky, pokud dojde ke ztrátě jejich stavu. Uživatel ztratí stav v tomto scénáři, pokud přechází z formuláře a vrátí se později.
* Nákupní košíky: všechny obchodní důležité komponenty aplikace, které představují potenciální tržby, se dají udržovat. Uživatel, který ztratí svůj stav, a tedy i jeho nákupní košík, může koupit méně produktů nebo služeb při návratu do webu později.

Aplikace může zachovat jenom *stav aplikace*. Uživatelská rozhraní nelze zachovat, například instance komponent a jejich stromy vykreslování. Komponenty a stromy vykreslování nejsou obecně serializovatelné. Aby bylo možné zachovat stav uživatelského rozhraní, jako jsou například rozbalené uzly ovládacího prvku stromového zobrazení, musí aplikace používat vlastní kód pro modelování chování uživatelského rozhraní jako serializovatelnýho stavu aplikace.

## <a name="where-to-persist-state"></a>Kam zachovat stav

Pro trvalý stav existují společná umístění:

* [Úložiště na straně serveru](#server-side-storage)
* [Adresa URL](#url)
* [Úložiště prohlížeče](#browser-storage)
* [Služba kontejneru stavu v paměti](#in-memory-state-container-service)

### <a name="server-side-storage"></a>Úložiště na straně serveru

V případě trvalé trvalosti dat, která zahrnuje více uživatelů a zařízení, může aplikace používat nezávislé serverové úložiště, ke kterému se přistupoval prostřednictvím webového rozhraní API. Mezi možnosti patří:

* Blob Storage
* Úložiště hodnot klíčů
* Relační databáze
* Úložiště Table

Po uložení dat se stav uživatele zachová a bude dostupný v jakékoli nové relaci prohlížeče.

Vzhledem k tomu Blazor WebAssembly , že aplikace běží zcela v prohlížeči uživatele, vyžadují další míry pro přístup k zabezpečeným externím systémům, jako jsou služby úložiště a databáze. Blazor WebAssembly aplikace jsou zabezpečené stejným způsobem jako aplikace s jedním stránkou (jednostránkové). Aplikace obvykle ověřuje uživatele prostřednictvím [OAuth](https://oauth.net) / [OpenID Connect (OIDC)](https://openid.net/connect/) a pak spolupracuje se službami úložiště a databázemi prostřednictvím volání webového rozhraní API do aplikace na straně serveru. Aplikace na straně serveru vypravuje přenos dat mezi Blazor WebAssembly aplikací a službou úložiště nebo databází. Blazor WebAssemblyAplikace udržuje dočasné připojení k aplikaci na straně serveru, zatímco aplikace na straně serveru má trvalé připojení k úložišti.

Další informace naleznete v následujících zdrojích:

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Blazor*Zabezpečení a Identity * zboží

Další informace o možnostech Azure Data Storage najdete v následujících tématech:

* [Databáze Azure](https://azure.microsoft.com/product-categories/databases/)
* [Dokumentace k Azure Storage](/azure/storage/)

### <a name="url"></a>URL

Pro přechodná data představující stav navigace modelujte data jako součást adresy URL. Mezi příklady stavu uživatele v adrese URL patří:

* ID zobrazené entity
* Aktuální číslo stránky v mřížce na stránkovaném webu

Pokud uživatel ručně znovu načte stránku, obsah panelu Adresa prohlížeče se zachová.

Informace o definování vzorů adres URL pomocí [`@page`](xref:mvc/views/razor#page) direktivy naleznete v tématu <xref:blazor/fundamentals/routing> .

### <a name="browser-storage"></a>Úložiště prohlížeče

Pro přechodná data, která uživatel aktivně vytváří, se běžně používané umístění úložiště nachází v prohlížeči [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) a [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) kolekcích:

* `localStorage` je vymezen v okně prohlížeče. Pokud uživatel stránku znovu načte nebo zavře a znovu otevře prohlížeč, stav přetrvává. Pokud uživatel otevře více karet prohlížeče, stav se sdílí napříč kartami. Data přetrvají do `localStorage` doby, než je explicitně zrušeno.
* `sessionStorage` je vymezen na kartu prohlížeče. Pokud uživatel znovu načte kartu, stav se přetrvá. Pokud uživatel zavře kartu nebo prohlížeč, dojde ke ztrátě stavu. Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.

> [!NOTE]
> `localStorage` a `sessionStorage` dá se použít v Blazor WebAssembly aplikacích, ale jenom pomocí psaní vlastního kódu nebo pomocí balíčku třetí strany.

Obecně `sessionStorage` je bezpečnější použít. `sessionStorage` Vyhněte se riziku, že uživatel otevře více karet a narazí na následující:

* Chyby v úložišti stavů napříč kartami.
* Matoucí chování, když karta Přepisuje stav jiných karet.

`localStorage` je lepší volbou, pokud aplikace musí zachovat stav v rámci zavírání a znovu otevřít prohlížeč.

> [!WARNING]
> Uživatelé mohou zobrazit nebo manipulovat s daty uloženými v `localStorage` a `sessionStorage` .

## <a name="in-memory-state-container-service"></a>Služba kontejneru stavu v paměti

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a>Další zdroje informací

* [Uložení stavu aplikace před operací ověřování](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

Blazor Server je stavová architektura aplikace. Ve většině případů aplikace udržuje připojení k serveru. Stav uživatele je uložený v paměti serveru v *okruhu*. 

Příklady stavu uživatele uchovávaného v okruhu zahrnují:

* Hierarchie instancí komponent a jejich poslední výstup vykreslování ve vykresleném uživatelském rozhraní.
* Hodnoty polí a vlastností v instancích součástí.
* Data uchovávaná v instancích služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , která jsou vymezena okruhu.

Stav uživatele se může také najít v proměnných JavaScriptu v paměti prohlížeče prostřednictvím volání [interoperability JavaScriptu](xref:blazor/call-javascript-from-dotnet) .

Pokud dojde ke ztrátě dočasného připojení k síti, Blazor pokusí se uživatel znovu připojit k původnímu okruhu s původním stavem. Ale opětovné připojení uživatele k původnímu okruhu v paměti serveru není vždycky možné:

* Server nemůže trvale zachovávat odpojený okruh. Server musí uvolnit odpojený okruh po vypršení časového limitu nebo v případě, že je server pod tlakem paměti.
* V prostředích pro nasazení s více servery a vyrovnáváním zatížení můžou jednotlivé servery selhat nebo je automaticky odebrat, když už nepotřebujete pro zpracování celkového objemu požadavků. V případě, že se uživatel pokusí znovu připojit, může dojít k nedostupnosti původních požadavků na zpracování serveru pro uživatele.
* Uživatel může zavřít a znovu otevřít prohlížeč nebo znovu načíst stránku, což odstraní všechny stavy uchovávané v paměti prohlížeče. Například hodnoty proměnných JavaScriptu nastavené prostřednictvím volání interoperability JavaScript se ztratí.

Když se uživatel nedá znovu připojit k původnímu okruhu, uživatel dostane nový okruh s prázdným stavem. Jedná se o ekvivalent zavření a opětovného otevření desktopové aplikace.

## <a name="persist-state-across-circuits"></a>Zachovat stav napříč okruhy

Obecně platí, že se udržuje stav napříč okruhy, kde uživatelé aktivně vytvářejí data, a ne jednoduše čtou data, která již existují.

Aby bylo možné zachovat stav napříč okruhy, musí aplikace uchovávat data do jiného umístění úložiště než z paměti serveru. Trvalost stavu není automatické. Je nutné provést kroky při vývoji aplikace k implementaci stavové trvalosti dat.

Trvalost dat se obvykle vyžaduje jenom pro stav vysoké hodnoty, který uživatelé vynaložené úsilím vytvořit. V následujících příkladech trvalého stavu ušetříte čas nebo pomůcky při komerčních činnostech:

* Webové formuláře s více kroky: časově náročné pro uživatele, aby znovu zadal data pro několik dokončených kroků webového formuláře s více kroky, pokud dojde ke ztrátě jejich stavu. Uživatel ztratí stav v tomto scénáři, pokud přechází z formuláře a vrátí se později.
* Nákupní košíky: všechny obchodní důležité komponenty aplikace, které představují potenciální tržby, se dají udržovat. Uživatel, který ztratí svůj stav, a tedy i jeho nákupní košík, může koupit méně produktů nebo služeb při návratu do webu později.

Aplikace může zachovat jenom *stav aplikace*. Uživatelská rozhraní nelze zachovat, například instance komponent a jejich stromy vykreslování. Komponenty a stromy vykreslování nejsou obecně serializovatelné. Aby bylo možné zachovat stav uživatelského rozhraní, jako jsou například rozbalené uzly ovládacího prvku stromového zobrazení, musí aplikace používat vlastní kód pro modelování chování uživatelského rozhraní jako serializovatelnýho stavu aplikace.

## <a name="where-to-persist-state"></a>Kam zachovat stav

Pro trvalý stav existují společná umístění:

* [Úložiště na straně serveru](#server-side-storage)
* [Adresa URL](#url)
* [Úložiště prohlížeče](#browser-storage)
* [Služba kontejneru stavu v paměti](#in-memory-state-container-service)

### <a name="server-side-storage"></a>Úložiště na straně serveru

V případě trvalé trvalosti dat, která zahrnuje více uživatelů a zařízení, může aplikace používat úložiště na straně serveru. Mezi možnosti patří:

* Blob Storage
* Úložiště hodnot klíčů
* Relační databáze
* Úložiště Table

Po uložení dat se stav uživatele zachová a bude dostupný v jakémkoli novém okruhu.

Další informace o možnostech Azure Data Storage najdete v následujících tématech:

* [Databáze Azure](https://azure.microsoft.com/product-categories/databases/)
* [Dokumentace k Azure Storage](/azure/storage/)

### <a name="url"></a>URL

Pro přechodná data představující stav navigace modelujte data jako součást adresy URL. Mezi příklady stavu uživatele v adrese URL patří:

* ID zobrazené entity
* Aktuální číslo stránky v mřížce na stránkovaném webu

Obsah panelu Adresa prohlížeče se zachová:

* Pokud uživatel ručně znovu načte stránku.
* Pokud dojde k nedostupnosti webového serveru a uživatel je nucen znovu načíst stránku, aby se mohl připojit k jinému serveru.

Informace o definování vzorů adres URL pomocí [`@page`](xref:mvc/views/razor#page) direktivy naleznete v tématu <xref:blazor/fundamentals/routing> .

### <a name="browser-storage"></a>Úložiště prohlížeče

Pro přechodná data, která uživatel aktivně vytváří, se běžně používané umístění úložiště nachází v prohlížeči [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) a [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) kolekcích:

* `localStorage` je vymezen v okně prohlížeče. Pokud uživatel stránku znovu načte nebo zavře a znovu otevře prohlížeč, stav přetrvává. Pokud uživatel otevře více karet prohlížeče, stav se sdílí napříč kartami. Data přetrvají do `localStorage` doby, než je explicitně zrušeno.
* `sessionStorage` je vymezen na kartu prohlížeče. Pokud uživatel znovu načte kartu, stav se přetrvá. Pokud uživatel zavře kartu nebo prohlížeč, dojde ke ztrátě stavu. Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.

Obecně `sessionStorage` je bezpečnější použít. `sessionStorage` Vyhněte se riziku, že uživatel otevře více karet a narazí na následující:

* Chyby v úložišti stavů napříč kartami.
* Matoucí chování, když karta Přepisuje stav jiných karet.

`localStorage` je lepší volbou, pokud aplikace musí zachovat stav v rámci zavírání a znovu otevřít prohlížeč.

Upozornění pro použití úložiště prohlížeče:

* Podobně jako při použití databáze na straně serveru je načítání a ukládání dat asynchronní.
* Na rozdíl od databáze na straně serveru není úložiště během předgenerování k dispozici, protože požadovaná stránka v prohlížeči neexistuje během fáze předvykreslování.
* Ukládání několika kilobajtů dat je pro Blazor Server aplikace přijatelné. Po několika kilobajtech je potřeba vzít v úvahu dopad na výkon, protože data se načítají a ukládají v síti.
* Uživatelé můžou data zobrazit nebo s nimi manipulovat. [Ochrana dat ASP.NET Core](xref:security/data-protection/introduction) může riziko zmírnit. Například [ASP.NET Core chráněné úložiště prohlížeče](#aspnet-core-protected-browser-storage) používá ASP.NET Core ochrany dat.

Balíčky NuGet třetích stran poskytují rozhraní API pro práci s `localStorage` a `sessionStorage` . Je vhodné zvážit výběr balíčku, který transparentně používá [ASP.NET Core ochranu dat](xref:security/data-protection/introduction). Ochrana dat šifruje uložená data a snižuje potenciální riziko manipulace s uloženými daty. Pokud jsou data serializovaná do formátu JSON uložená v prostém textu, uživatelé můžou data zobrazit pomocí vývojářských nástrojů pro prohlížeč a také upravovat uložená data. Zabezpečení dat se vždy nejedná o problém, protože data můžou být triviální v podstatě. Například čtení nebo úprava uložené barvy prvku uživatelského rozhraní není významným bezpečnostním rizikem pro uživatele nebo organizaci. Neumožňuje uživatelům kontrolovat *citlivá data*a manipulovat s nimi.

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>ASP.NET Core chráněné úložiště prohlížeče

ASP.NET Core chráněné úložiště prohlížeče využívá [ASP.NET Core ochrany dat](xref:security/data-protection/introduction) pro [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) a [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!NOTE]
> Chráněné úložiště prohlížeče spoléhá na ASP.NET Core ochrany dat a podporuje se jenom pro Blazor Server aplikace.

### <a name="configuration"></a>Konfigurace

1. Přidejte odkaz na balíček do [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions) .
1. V `Startup.ConfigureServices` volejte volání metody `AddProtectedBrowserStorage` Add `localStorage` a `sessionStorage` Services do kolekce služeb:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Ukládání a načítání dat v rámci součásti

V každé součásti, která vyžaduje načtení nebo uložení dat do úložiště prohlížeče, použijte [`@inject`](xref:mvc/views/razor#inject) direktivu pro vložení instance některého z následujících prvků:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Volba závisí na tom, jaké umístění úložiště prohlížeče chcete použít. V následujícím příkladu `sessionStorage` se používá:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using`Direktiva může být umístěna do `_Imports.razor` souboru aplikace namísto do součásti. Použití `_Imports.razor` souboru zpřístupňuje obor názvů pro větší segmenty aplikace nebo celé aplikace.

Chcete-li zachovat `currentCount` hodnotu v `Counter` součásti aplikace na základě Blazor Server šablony projektu, upravte `IncrementCount` metodu, kterou chcete použít `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobné. Pro aplikace je pravděpodobnější ukládání celých objektů modelu, které zahrnují komplexní stav. `ProtectedSessionStore` automaticky serializace a deserializace dat JSON pro ukládání komplexních objektů stavu.

V předchozím příkladu kódu `currentCount` se data ukládají jako `sessionStorage['count']` v prohlížeči uživatele. Data nejsou uložená v prostém textu, ale místo toho jsou chráněná pomocí ASP.NET Core ochrany dat. Zašifrovaná data lze zkontrolovat, pokud `sessionStorage['count']` je vyhodnocena v konzole pro vývojáře v prohlížeči.

Chcete-li obnovit `currentCount` data, pokud se uživatel vrátí do `Counter` komponenty později, včetně toho, jestli je uživatel na novém okruhu, použijte `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

Pokud parametry komponenty obsahují stav navigace, zavolejte `ProtectedSessionStore.GetAsync` a přiřaďte `null` nevýsledek v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nikoli <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se volá pouze jednou při prvním vytvoření instance komponenty. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> není voláno později, pokud uživatel přejde na jinou adresu URL a zůstane na stejné stránce. Další informace naleznete v tématu <xref:blazor/components/lifecycle>.

> [!WARNING]
> Příklady v této části fungují pouze v případě, že server nemá povolené předvykreslování. Pokud je povoleno předvykreslování, je vygenerována chyba s vysvětlením, že volání interoperability JavaScriptu nelze vydat, protože součást je předem vykreslena.
>
> Buď zakažte předvykreslování nebo přidejte další kód pro práci s předvykreslováním. Další informace o psaní kódu, který funguje s předvykreslováním, najdete v části [popisovač předvykreslování](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Zpracování stavu načítání

Vzhledem k tomu, že je úložiště prohlížeče přístupné asynchronně přes síťové připojení, je vždy časový interval před načtením dat a k dispozici pro komponentu. Nejlepších výsledků dosáhnete, když při načítání vykreslíte zprávu o stavu načítání, místo aby se zobrazila prázdná nebo výchozí data.

Jedním z těchto způsobů je sledovat, jestli data jsou `null` , což znamená, že se data pořád načítají. Ve výchozí `Counter` součásti je počet uchováván v `int` . Převést na typ s [ `currentCount` možnou hodnotou null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) přidáním otazníku ( `?` ) do typu ( `int` ):

```csharp
private int? currentCount;
```

Namísto nepodmíněného zobrazení čítače a **`Increment`** tlačítka zobrazte tyto prvky pouze v případě, že jsou data načtena kontrolou <xref:System.Nullable%601.HasValue%2A> :

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Zpracovat předvykreslování

Během předvykreslování:

* Interaktivní připojení k prohlížeči uživatele neexistuje.
* Prohlížeč ještě nemá stránku, ve které může spustit kód jazyka JavaScript.

`localStorage` nebo `sessionStorage` nejsou během předvykreslování k dispozici. Pokud se komponenta pokusí pracovat s úložištěm, je vygenerována chyba s vysvětlením, že volání interoperability JavaScriptu nelze vydat, protože součást je předem vykreslena.

Jedním ze způsobů, jak chybu vyřešit, je zakázat předvykreslování. To je obvykle nejlepší volba, pokud aplikace využívá úložiště založené na prohlížeči. Předvykreslování přináší složitost a nevýhoduje aplikaci, protože aplikace nemůže využít žádný užitečný obsah do té doby, než `localStorage` `sessionStorage` je k dispozici.

Chcete-li zakázat předvykreslování, otevřete `Pages/_Host.cshtml` soubor a změňte `render-mode` atribut [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Předvykreslování může být užitečné pro jiné stránky, které nepoužívají `localStorage` nebo `sessionStorage` . Chcete-li zachovat předvykreslování, odložte operaci načítání, dokud není prohlížeč připojen k okruhu. Následuje příklad uložení hodnoty čítače:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Rozložte zachování stavu na společné místo.

Pokud mnoho komponent spoléhá na úložiště založené na prohlížeči, často se kód zprostředkovatele stavu znovu implementuje vytvořením duplicit kódu. Jednou z možností, jak zabránit duplikaci kódu, je vytvoření *nadřazené komponenty poskytovatele stavu* , která zapouzdřuje logiku poskytovatele stavu. Podřízené komponenty mohou pracovat s trvalými daty bez ohledu na mechanismus trvalosti stavu.

V následujícím příkladu `CounterStateProvider` komponenty jsou data čítače trvalá `sessionStorage` :

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`Komponenta zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.

Chcete-li použít `CounterStateProvider` komponentu, zabalte instanci součásti kolem jakékoli jiné komponenty, která vyžaduje přístup ke stavu čítače. Chcete-li zpřístupnit stav pro všechny komponenty v aplikaci, zabalte `CounterStateProvider` komponentu kolem komponenty <xref:Microsoft.AspNetCore.Components.Routing.Router> v `App` součásti ( `App.razor` ):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Zabalené komponenty obdrží a můžou upravovat trvalý stav čítače. `Counter`Vzor implementuje následující součást:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Předchozí komponenta není nutná k interakci s `ProtectedBrowserStorage` , ani nefunguje se fází "načítání".

Chcete-li se vypořádat s předem popsaným způsobem, `CounterStateProvider` lze upravit tak, aby všechny součásti, které spotřebovávají data čítače, pracovaly automaticky s předem vykreslením. Další informace najdete v části [zpracování předvykreslování](#handle-prerendering) .

Obecně se doporučuje vzor *nadřazené komponenty zprostředkovatele stavu* :

* Pro využívání stavu napříč mnoha komponentami.
* Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má být zachován.

Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší vyhnout se zachování stavu globálně.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>Experimentální balíček NuGet pro úložiště chráněný prohlížečem

ASP.NET Core chráněné úložiště prohlížeče využívá [ASP.NET Core ochrany dat](xref:security/data-protection/introduction) pro [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) a [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` je nepodporovaný, experimentální balíček nevhodný pro produkční použití.
>
> Balíček je dostupný jenom v aplikacích ASP.NET Core 3,1 Blazor Server .

### <a name="configuration"></a>Konfigurace

1. Přidejte odkaz na balíček do [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .
1. Do tohoto `Pages/_Host.cshtml` souboru přidejte do uzavírací značky tento skript `</body>` :

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. V `Startup.ConfigureServices` volejte volání metody `AddProtectedBrowserStorage` Add `localStorage` a `sessionStorage` Services do kolekce služeb:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Ukládání a načítání dat v rámci součásti

V každé součásti, která vyžaduje načtení nebo uložení dat do úložiště prohlížeče, použijte [`@inject`](xref:mvc/views/razor#inject) direktivu pro vložení instance některého z následujících prvků:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Volba závisí na tom, jaké umístění úložiště prohlížeče chcete použít. V následujícím příkladu `sessionStorage` se používá:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using`Příkaz lze umístit do `_Imports.razor` souboru místo v součásti. Použití `_Imports.razor` souboru zpřístupňuje obor názvů pro větší segmenty aplikace nebo celé aplikace.

Chcete-li zachovat `currentCount` hodnotu v `Counter` součásti aplikace na základě Blazor Server šablony projektu, upravte `IncrementCount` metodu, kterou chcete použít `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobné. Pro aplikace je pravděpodobnější ukládání celých objektů modelu, které zahrnují komplexní stav. `ProtectedSessionStore` automaticky serializace a deserializace dat JSON.

V předchozím příkladu kódu `currentCount` se data ukládají jako `sessionStorage['count']` v prohlížeči uživatele. Data nejsou uložená v prostém textu, ale místo toho jsou chráněná pomocí ASP.NET Core ochrany dat. Zašifrovaná data lze zkontrolovat, pokud `sessionStorage['count']` je vyhodnocena v konzole pro vývojáře v prohlížeči.

Chcete-li obnovit `currentCount` data, pokud se uživatel vrátí do `Counter` komponenty později, včetně toho, jestli se nachází na zcela novém okruhu, použijte `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Pokud parametry komponenty obsahují stav navigace, zavolejte `ProtectedSessionStore.GetAsync` a přiřaďte výsledek v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nikoli <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se volá pouze jednou při prvním vytvoření instance komponenty. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> není voláno později, pokud uživatel přejde na jinou adresu URL a zůstane na stejné stránce. Další informace naleznete v tématu <xref:blazor/components/lifecycle>.

> [!WARNING]
> Příklady v této části fungují pouze v případě, že server nemá povolené předvykreslování. Pokud je povoleno předvykreslování, je vygenerována chyba s vysvětlením, že volání interoperability JavaScriptu nelze vydat, protože součást je předem vykreslena.
>
> Buď zakažte předvykreslování nebo přidejte další kód pro práci s předvykreslováním. Další informace o psaní kódu, který funguje s předvykreslováním, najdete v části [popisovač předvykreslování](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Zpracování stavu načítání

Vzhledem k tomu, že je úložiště prohlížeče přístupné asynchronně přes síťové připojení, je vždy časový interval před načtením dat a k dispozici pro komponentu. Nejlepších výsledků dosáhnete, když při načítání vykreslíte zprávu o stavu načítání, místo aby se zobrazila prázdná nebo výchozí data.

Jedním z těchto způsobů je sledovat, jestli data jsou `null` , což znamená, že se data pořád načítají. Ve výchozí `Counter` součásti je počet uchováván v `int` . Převést na typ s [ `currentCount` možnou hodnotou null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) přidáním otazníku ( `?` ) do typu ( `int` ):

```csharp
private int? currentCount;
```

Namísto nepodmíněného zobrazení **`Increment`** hodnoty a tlačítka vyberte možnost zobrazit tyto prvky pouze v případě, že jsou data načtena:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Zpracovat předvykreslování

Během předvykreslování:

* Interaktivní připojení k prohlížeči uživatele neexistuje.
* Prohlížeč ještě nemá stránku, ve které může spustit kód jazyka JavaScript.

`localStorage` nebo `sessionStorage` nejsou během předvykreslování k dispozici. Pokud se komponenta pokusí pracovat s úložištěm, je vygenerována chyba s vysvětlením, že volání interoperability JavaScriptu nelze vydat, protože součást je předem vykreslena.

Jedním ze způsobů, jak chybu vyřešit, je zakázat předvykreslování. To je obvykle nejlepší volba, pokud aplikace využívá úložiště založené na prohlížeči. Předvykreslování přináší složitost a nevýhoduje aplikaci, protože aplikace nemůže využít žádný užitečný obsah do té doby, než `localStorage` `sessionStorage` je k dispozici.

Chcete-li zakázat předvykreslování, otevřete `Pages/_Host.cshtml` soubor a změňte `render-mode` atribut [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Předvykreslování může být užitečné pro jiné stránky, které nepoužívají `localStorage` nebo `sessionStorage` . Chcete-li zachovat předvykreslování, odložte operaci načítání, dokud není prohlížeč připojen k okruhu. Následuje příklad uložení hodnoty čítače:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Rozložte zachování stavu na společné místo.

Pokud mnoho komponent spoléhá na úložiště založené na prohlížeči, často se kód zprostředkovatele stavu znovu implementuje vytvořením duplicit kódu. Jednou z možností, jak zabránit duplikaci kódu, je vytvoření *nadřazené komponenty poskytovatele stavu* , která zapouzdřuje logiku poskytovatele stavu. Podřízené komponenty mohou pracovat s trvalými daty bez ohledu na mechanismus trvalosti stavu.

V následujícím příkladu `CounterStateProvider` komponenty jsou data čítače trvalá `sessionStorage` :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`Komponenta zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.

Chcete-li použít `CounterStateProvider` komponentu, zabalte instanci součásti kolem jakékoli jiné komponenty, která vyžaduje přístup ke stavu čítače. Chcete-li zpřístupnit stav pro všechny komponenty v aplikaci, zabalte `CounterStateProvider` komponentu kolem komponenty <xref:Microsoft.AspNetCore.Components.Routing.Router> v `App` součásti ( `App.razor` ):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Zabalené komponenty obdrží a můžou upravovat trvalý stav čítače. `Counter`Vzor implementuje následující součást:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Předchozí komponenta není nutná k interakci s `ProtectedBrowserStorage` , ani nefunguje se fází "načítání".

Chcete-li se vypořádat s předem popsaným způsobem, `CounterStateProvider` lze upravit tak, aby všechny součásti, které spotřebovávají data čítače, pracovaly automaticky s předem vykreslením. Další informace najdete v části [zpracování předvykreslování](#handle-prerendering) .

Obecně se doporučuje model *nadřazené komponenty zprostředkovatele stavu* :

* Pro využívání stavu napříč mnoha komponentami.
* Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má být zachován.

Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší vyhnout se zachování stavu globálně.

::: moniker-end

## <a name="in-memory-state-container-service"></a>Služba kontejneru stavu v paměti

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
