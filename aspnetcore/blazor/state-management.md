---
title: ASP.NET Blazor Správa hlavního státu
author: guardrex
description: Přečtěte si, Blazor jak zachovat stav v serverových aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218866"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>ASP.NET Blazor Správa hlavního státu

Podle [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorServer je stavové rozhraní aplikace. Ve většině času aplikace udržuje trvalé připojení k serveru. Stav uživatele je uložen v paměti serveru v *okruhu*. 

Příklady stavu drženého pro okruh uživatele:

* Rendrované uI&mdash;hierarchie instancí komponent a jejich nejnovější výstup vykreslení.
* Hodnoty všech polí a vlastností v instancích komponent.
* Data uchovávaná v instancích [služby vkládání závislostí (DI),](xref:fundamentals/dependency-injection) které jsou vymezeny na okruh.

> [!NOTE]
> Tento článek se zabývá Blazor trvaloststavu v serverových aplikacích. BlazorAplikace WebAssembly můžete využít [trvalost stavu na straně klienta v prohlížeči,](#client-side-in-the-browser) ale vyžadují vlastní řešení nebo balíčky třetích stran nad rámec tohoto článku.

## <a name="opno-locblazor-circuits"></a>BlazorObvody

Pokud uživatel dojde ke ztrátě Blazor dočasného připojení k síti, pokusí se znovu připojit uživatele k původnímu okruhu, aby mohl pokračovat v používání aplikace. Opětovné připojení uživatele k původnímu okruhu v paměti serveru však není vždy možné:

* Server nemůže udržet odpojený obvod navždy. Server musí uvolnit odpojený obvod po uplynutí časového omezení nebo po nedostatku paměti.
* V prostředích nasazení s vyrovnáváním zatížení s vyrovnáváním zatížení mohou být všechny požadavky na zpracování serveru v daném okamžiku nedostupné. Jednotlivé servery mohou selhat nebo být automaticky odebrány, pokud již není nutné zpracovávat celkový objem požadavků. Původní server nemusí být k dispozici, pokud se uživatel pokusí znovu připojit.
* Uživatel může zavřít a znovu otevřít svůj prohlížeč nebo znovu načíst stránku, která odebere jakýkoli stav, který je v paměti prohlížeče. Například hodnoty nastavené prostřednictvím interop volání JavaScriptu jsou ztraceny.

Když uživatel nemůže být znovu připojen k původnímu okruhu, obdrží nový okruh s prázdným stavem. To je ekvivalentní k zavření a opětovnému otevření aplikace klasické pracovní plochy.

## <a name="preserve-state-across-circuits"></a>Zachovat stav napříč obvody

V některých scénářích zachování stavu napříč obvody je žádoucí. Aplikace může uchovávat důležitá data pro uživatele, pokud:

* Webový server přestane být k dispozici.
* Prohlížeč uživatele je nucen spustit nový okruh s novým webovým serverem.

Obecně platí, že udržování stavu napříč okruhy platí pro scénáře, kde uživatelé aktivně vytvářejí data, nikoli pouze čtení dat, která již existuje.

Chcete-li zachovat stav mimo jeden okruh, *neuklápěte pouze data do paměti serveru*. Aplikace musí zachovat data do jiného umístění úložiště. Trvalosti stavu není&mdash;automatické, musíte provést kroky při vývoji aplikace k implementaci stavové trvalosti dat.

Trvalost dat je obvykle vyžadována pouze pro stav s vysokou hodnotou, který uživatelé vynaložili úsilí k vytvoření. V následujících příkladech zachování stavu buď šetří čas nebo pomáhá v komerčních aktivitách:

* Webform &ndash; vícekroků Je časově náročné, aby uživatel znovu zadá data pro několik dokončených kroků vícekrokového procesu, pokud dojde ke ztrátě jejich stavu. Uživatel ztratí stav v tomto scénáři, pokud přejít od formuláře více kroků a vrátit se do formuláře později.
* Nákupní &ndash; košík Lze udržovat jakoukoli komerčně důležitou součást aplikace, která představuje potenciální výnosy. Uživatel, který ztratí svůj stav, a tedy i nákupní košík, může po návratu na web později zakoupit méně produktů nebo služeb.

Obvykle není nutné zachovat snadno znovu vytvořený stav, například uživatelské jméno zadané do přihlašovacího dialogového okna, které nebylo odesláno.

> [!IMPORTANT]
> Aplikace může zachovat pouze *stav aplikace*. Ui nelze trvalé, jako jsou instance komponent a jejich vykreslení stromy. Součásti a vykreslovací stromy nejsou obecně serializovatelné. Chcete-li zachovat něco podobného stavu ui, jako jsou rozšířené uzly TreeView, aplikace musí mít vlastní kód pro modelování chování jako serializovatelný stav aplikace.

## <a name="where-to-persist-state"></a>Kde přetrvávat stav

Existují tři běžná umístění pro Blazor trvalý stav v aplikaci Server. Každý přístup je nejvhodnější pro různé scénáře a má různé upozornění:

* [Na straně serveru v databázi](#server-side-in-a-database)
* [Adresa URL](#url)
* [Na straně klienta v prohlížeči](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Na straně serveru v databázi

Pro trvalost trvalých dat nebo pro všechna data, která musí span více uživatelů nebo zařízení, nezávislé databáze na straně serveru je téměř jistě nejlepší volbou. Mezi možnosti patří:

* Relační databáze SQL
* Ukládání hodnot klíče
* Úložiště objektů blob
* Úložiště tabulek

Po uložení dat do databáze může uživatel kdykoli spustit nový okruh. Data uživatele jsou uchována a jsou k dispozici v každém novém okruhu.

Další informace o možnostech úložiště dat Azure najdete v tématu [Dokumentace k úložišti Azure](/azure/storage/) a Databáze [Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>zprostředkovatele identity

Pro přechodná data představující stav navigace modelujte data jako součást adresy URL. Příklady stavu modelovaného v adrese URL:

* ID zobrazené entity.
* Aktuální číslo stránky ve stránkované mřížce.

Obsah adresního řádku prohlížeče zůstane zachován:

* Pokud uživatel ručně znovu načte stránku.
* Pokud webový server&mdash;přestane být k dispozici, je uživatel nucen znovu načíst stránku, aby se mohl připojit k jinému serveru.

Informace o definování vzorů adres `@page` URL pomocí <xref:blazor/routing>direktivy naleznete v tématu .

### <a name="client-side-in-the-browser"></a>Na straně klienta v prohlížeči

Pro přechodná data, která uživatel aktivně vytváří, společné úložiště zálohování `localStorage` `sessionStorage` je prohlížeče a kolekce. Aplikace není nutné spravovat nebo vymazat uložený stav, pokud je okruh opuštěný, což je výhoda oproti úložišti na straně serveru.

> [!NOTE]
> "Na straně klienta" v této části odkazuje na scénáře na straně klienta v prohlížeči, nikoli [ Blazor webassembly hosting model](xref:blazor/hosting-models#blazor-webassembly). `localStorage`a `sessionStorage` lze jej Blazor použít v aplikacích WebAssembly, ale pouze napsáním vlastního kódu nebo použitím balíčku třetí strany.

`localStorage`a `sessionStorage` liší se takto:

* `localStorage`je vymezen a to do prohlížeče uživatele. Pokud uživatel znovu načte stránku nebo zavře a znovu otevře prohlížeč, stav přetrvává. Pokud uživatel otevře více karet prohlížeče, stav je sdílen mezi kartami. Data přetrvávají, `localStorage` dokud není explicitně vymazána.
* `sessionStorage`je vymezen a to na kartu prohlížeče uživatele. Pokud uživatel znovu načte kartu, stav přetrvává. Pokud uživatel zavře kartu nebo prohlížeč, stav se ztratí. Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.

Obecně `sessionStorage` platí, že je bezpečnější používat. `sessionStorage`zabraňuje riziku, že uživatel otevře více karet a narazí na následující:

* Chyby ve stavu úložiště na kartách.
* Matoucí chování, když karta přepíše stav ostatních karet.

`localStorage`je lepší volbou, pokud aplikace musí zachovat stav při zavírání a opětovném otevření prohlížeče.

Upozornění pro použití úložiště prohlížeče:

* Podobně jako použití databáze na straně serveru, načítání a ukládání dat jsou asynchronní.
* Na rozdíl od databáze na straně serveru není úložiště během předběžného vykreslování k dispozici, protože požadovaná stránka neexistuje v prohlížeči během fáze předběžného vykreslování.
* Úložiště několika kilobajtů dat je rozumné Blazor zachovat pro serverové aplikace. Kromě několika kilobajtů je třeba zvážit důsledky pro výkon, protože data jsou načtena a uložena v síti.
* Uživatelé mohou zobrazit data nebo s nimi manipulovat. ASP.NET základní [ochrana dat](xref:security/data-protection/introduction) může zmírnit riziko.

## <a name="third-party-browser-storage-solutions"></a>Řešení pro ukládání prohlížečů jiných výrobců

Balíčky NuGet jiných výrobců poskytují `localStorage` api `sessionStorage`pro práci s a .

Stojí za to zvážit výběr balíčku, který transparentně používá ASP.NET [ochrany dat](xref:security/data-protection/introduction)core . ASP.NET Core Data Protection šifruje uložená data a snižuje potenciální riziko manipulace s uloženými daty. Pokud jsou serializovaná data JSON uložena ve formátu prostého textu, mohou uživatelé data zobrazit pomocí nástrojů pro vývojáře prohlížeče a také upravit uložená data. Zabezpečení dat není vždy problém, protože data mohou být triviální povahy. Například čtení nebo úprava uložené barvy prvku uživatelského rozhraní není významné bezpečnostní riziko pro uživatele nebo organizaci. Neumožňuje uživatelům kontrolovat nebo manipulovat s *citlivými daty*.

## <a name="protected-browser-storage-experimental-package"></a>Experimentální balíček chráněného úložiště prohlížečů

Příklad balíčku NuGet, který poskytuje `localStorage` [ochranu dat](xref:security/data-protection/introduction) pro a `sessionStorage` je [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`je nepodporovaný experimentální balíček, který není v současné době vhodný pro produkční použití.

### <a name="installation"></a>Instalace

Instalace `Microsoft.AspNetCore.ProtectedBrowserStorage` balíčku:

1. V Blazor projektu aplikace Server přidejte odkaz na balíček [microsoft.aspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. V html nejvyšší úrovně (například v souboru *Pages/_Host.cshtml* ve výchozí `<script>` šabloně projektu) přidejte následující značku:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. V `Startup.ConfigureServices` metodě `AddProtectedBrowserStorage` volání `localStorage` přidat `sessionStorage` a služby kolekce služeb:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Uložení a načtení dat v rámci komponenty

V každé součásti, která vyžaduje načítání [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) nebo ukládání dat do úložiště prohlížeče, použijte k vložení instance jedné z následujících možností:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Volba závisí na tom, které záložní úložiště chcete použít. V následujícím příkladu se `sessionStorage` používá:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Příkaz `@using` lze umístit do souboru *_Imports.razor* namísto v součásti. Použití souboru *_Imports.razor* zpřístupní obor názvů větším segmentům aplikace nebo celé aplikaci.

Chcete-li `_currentCount` zachovat `Counter` hodnotu v součásti `IncrementCount` šablony projektu, upravte metodu, která má být používána `ProtectedSessionStore.SetAsync`:

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobným scénářem. Aplikace s větší pravděpodobností ukládají celé objekty modelu, které obsahují složitý stav. `ProtectedSessionStore`automaticky serializuje a reserializuje data JSON.

V předchozím příkladu kódu `_currentCount` jsou data `sessionStorage['count']` uložena jako v prohlížeči uživatele. Data nejsou uložena ve formátu prostého textu, ale jsou chráněna pomocí ASP.NET [ochrany dat](xref:security/data-protection/introduction)společnosti Core . Šifrovaná data lze `sessionStorage['count']` vidět, pokud je vyhodnocena v konzole pro vývojáře prohlížeče.

Chcete-li `_currentCount` obnovit data, pokud `Counter` se uživatel vrátí k součásti později (včetně toho, zda jsou na zcela novém okruhu), použijte `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Pokud parametry komponenty zahrnují stav `ProtectedSessionStore.GetAsync` navigace, zavolejte `OnParametersSetAsync`a `OnInitializedAsync`přiřaďte výsledek v , nikoli . `OnInitializedAsync`je volána pouze jednou při první instanci komponenty. `OnInitializedAsync`není volána znovu později, pokud uživatel přejde na jinou adresu URL, zatímco zůstane na stejné stránce. Další informace naleznete v tématu <xref:blazor/lifecycle>.

> [!WARNING]
> Příklady v této části fungují pouze v případě, že server nemá povoleno předběžné vykreslování. Při zapnutém předběžném vykreslování je generována chyba podobná:
>
> > V tuto chvíli nelze vydat interop volání jazyka JavaScript. Důvodem je, že součást je právě vykreslována.
>
> Buď zakázat předběžné vykreslování nebo přidat další kód pro práci s prerendering. Další informace o psaní kódu, který funguje s předběžným vykreslováním, najdete v části [Zpracování předběžného vykreslování.](#handle-prerendering)

### <a name="handle-the-loading-state"></a>Zpracování stavu načítání

Vzhledem k tomu, že úložiště prohlížeče je asynchronní (přístupné prostřednictvím síťového připojení), je vždy doba, než jsou data načtena a k dispozici pro použití komponentou. Pro dosažení nejlepších výsledků vykreslování zprávy stavu načítání při načítání probíhá namísto zobrazení prázdných nebo výchozích dat.

Jedním z přístupů je `null` sledovat, zda jsou data (stále načítání) nebo ne. Ve výchozí `Counter` součásti je počet `int`držen v . Přidejte `_currentCount` k typu`?``int`():

```csharp
private int? _currentCount;
```

Místo bezpodmínečného zobrazení tlačítka počet a **přírůstek** zvolte zobrazení těchto prvků pouze v případě, že jsou načtena data:

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Předběžné vykreslování popisovače

Během předběžného vykreslování:

* Interaktivní připojení k prohlížeči uživatele neexistuje.
* Prohlížeč ještě nemá stránku, na které by mohl spustit kód JavaScriptu.

`localStorage`nebo `sessionStorage` nejsou k dispozici během předběžného vykreslování. Pokud se komponenta pokusí o interakci s úložištěm, je generována chyba podobná:

> V tuto chvíli nelze vydat interop volání jazyka JavaScript. Důvodem je, že součást je právě vykreslována.

Jedním ze způsobů, jak chybu vyřešit, je zakázat předběžné vykreslování. To je obvykle nejlepší volbou, pokud aplikace využívá úložiště založené na prohlížeči. Předběžné vykreslování zvyšuje složitost a neprospívá aplikaci, `sessionStorage` protože aplikace nemůže předem vykreslit žádný užitečný obsah, dokud není `localStorage` k dispozici.

Chcete-li zakázat předběžné vykreslování, `render-mode` otevřete soubor *Pages/_Host.cshtml* a změňte [pomocníka pro značku komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.

Předběžné vykreslování může být `localStorage` `sessionStorage`užitečné pro jiné stránky, které nepoužívají nebo . Chcete-li zachovat povolenou předběžnou interpretaci, odložte operaci načítání, dokud není prohlížeč připojen k okruhu. Následuje příklad pro ukládání hodnoty čítače:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Faktor z zachování stavu na společné místo

Pokud mnoho součástí spoléhá na úložiště založené na prohlížeči, reimplementing kód zprostředkovatele stavu mnohokrát vytvoří duplikaci kódu. Jednou z možností, jak se vyhnout duplikaci kódu, je vytvoření *nadřazené součásti zprostředkovatele stavu,* která zapouzdřuje logiku zprostředkovatele stavu. Podřízené součásti mohou pracovat s trvalá data bez ohledu na mechanismus trvalosti stavu.

V následujícím příkladu `CounterStateProvider` komponenty jsou data čítačů zachována:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
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
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Komponenta `CounterStateProvider` zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.

Chcete-li `CounterStateProvider` použít komponentu, obtékejte instanci komponenty kolem jakékoli jiné součásti, která vyžaduje přístup ke stavu čítače. Chcete-li, aby byl stav přístupný všem `CounterStateProvider` součástem `Router` v `App` aplikaci, obtékejte komponentu kolem komponenty (*App.razor*):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Zabalené součásti přijímat a můžete upravit trvalý stav čítače. Následující `Counter` součást implementuje vzor:

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

Předchozí součást není nutné pro interakci `ProtectedBrowserStorage`s , ani se nezabývá "načítání" fáze.

Chcete-li pracovat s prerendering, jak je popsáno výše, lze změnit tak, `CounterStateProvider` aby všechny součásti, které spotřebovávají data čítače automaticky pracovat s prerendering. Podrobnosti najdete v části [Zpracování předběžného vykreslování.](#handle-prerendering)

Obecně se doporučuje vzor *nadřazené součásti zprostředkovatele stavu:*

* Chcete-li spotřebovat stav v mnoha dalších součástech.
* Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má přetrvávat.

Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší se vyhnout zpracování načítání a ukládání stavu globálně.
