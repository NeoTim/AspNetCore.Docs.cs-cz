---
title: Správa stavu ASP.NET Core Blazor
author: guardrex
description: Přečtěte si, jak zachovat stav v aplikacích Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: 990d392b0e1658774256626eb277701e40287b79
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726918"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>Správa stavu ASP.NET Core Blazor

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Server Blazor je stavová architektura aplikace. Ve většině případů aplikace udržuje průběžné připojení k serveru. Stav uživatele je uložený v paměti serveru v *okruhu*. 

Příkladem stavu drženého pro okruh uživatele jsou:

* Vykreslené uživatelské rozhraní&mdash;hierarchii instancí komponent a jejich poslední výstup vykreslování.
* Hodnoty všech polí a vlastností v instancích součástí.
* Data uchovávaná v instancích služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , která jsou vymezena okruhu.

> [!NOTE]
> Tento článek řeší trvalost stavu v Blazor serverových aplikacích. Blazor aplikace WebAssembly můžou využít [trvalost stavu na straně klienta v prohlížeči](#client-side-in-the-browser) , ale kromě rozsahu tohoto článku vyžadují vlastní řešení nebo balíčky třetích stran.

## <a name="opno-locblazor-circuits"></a>okruhy Blazor

Pokud dojde ke ztrátě dočasného připojení k síti, Blazor se pokusí uživatele znovu připojit k původnímu okruhu, aby mohli i nadále používat aplikaci. Ale opětovné připojení uživatele k původnímu okruhu v paměti serveru není vždycky možné:

* Server nemůže trvale zachovávat odpojený okruh. Server musí uvolnit odpojený okruh po vypršení časového limitu nebo v případě, že je server pod tlakem paměti.
* V prostředích s více servery nasazení s vyrovnáváním zatížení můžou všechny požadavky na zpracování serveru být v daném okamžiku nedostupné. Jednotlivé servery můžou selhat nebo automaticky odebrat, pokud už nepotřebujete pro zpracování celkového objemu požadavků. Původní server nemusí být k dispozici, když se uživatel pokusí znovu připojit.
* Uživatel může zavřít a znovu otevřít prohlížeč nebo znovu načíst stránku, což odstraní všechny stavy uchovávané v paměti prohlížeče. Například hodnoty nastavené prostřednictvím volání interoperability JavaScriptu jsou ztraceny.

Když se uživatel nedá znovu připojit k původnímu okruhu, uživatel dostane nový okruh s prázdným stavem. Jedná se o ekvivalent zavření a opětovného otevření desktopové aplikace.

## <a name="preserve-state-across-circuits"></a>Zachovat stav napříč okruhy

V některých scénářích je žádoucí zachovat stav napříč okruhy. Aplikace může uchovávat důležitá data pro uživatele, pokud:

* Webový server bude nedostupný.
* V prohlížeči uživatele je nucen spustit nový okruh s novým webovým serverem.

Obecně platí, že udržování stavu napříč okruhy se vztahuje na scénáře, kdy uživatelé aktivně vytvářejí data, nikoli jenom čtení dat, která už existují.

Chcete-li zachovat stav nad rámec jednoho okruhu, *neukládejte pouze data v paměti serveru*. Aplikace musí uchovávat data do jiného umístění úložiště. Trvalost stavu není automaticky&mdash;musíte provést kroky při vývoji aplikace pro implementaci stavového uchovávání dat.

Trvalost dat se obvykle vyžaduje jenom pro stav vysoké hodnoty, který uživatelé vynaložené úsilím vytvořit. V následujících příkladech trvalého stavu ušetříte čas nebo pomůcky při komerčních činnostech:

* Webformu ve více krocích &ndash; pro uživatele časově náročné, aby znovu zadal data pro několik dokončených kroků procesu s více kroky, pokud dojde ke ztrátě jejich stavu. Uživatel ztratí stav v tomto scénáři, pokud přechází z formuláře s více kroky a později se vrátí do formuláře.
* Nákupní košík &ndash; všech komerčních důležitých komponent aplikace, které představují potenciální výnosy, se dají udržovat. Uživatel, který ztratí svůj stav, a tedy i jeho nákupní košík, může koupit méně produktů nebo služeb při návratu do webu později.

Obvykle není nutné zachovávat snadno-znovu vytvořený stav, jako je například uživatelské jméno zadané do přihlašovacího dialogu, který nebyl odeslán.

> [!IMPORTANT]
> Aplikace může zachovat jenom *stav aplikace*. Uživatelská rozhraní nelze zachovat, například instance komponent a jejich stromy vykreslování. Komponenty a stromy vykreslování nejsou obecně serializovatelné. Aby bylo možné zachovat podobný text jako stav uživatelského rozhraní, jako jsou například rozbalené uzly prvku TreeView, aplikace musí mít vlastní kód pro modelování chování jako serializovatelný stav aplikace.

## <a name="where-to-persist-state"></a>Kam zachovat stav

Existují tři společná umístění pro trvalý stav v aplikaci Blazor Server. Každý přístup nejlépe vyhovuje různým scénářům a má odlišná omezení:

* [Na straně serveru v databázi](#server-side-in-a-database)
* [Adresa URL](#url)
* [Klientská strana v prohlížeči](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Na straně serveru v databázi

Pro trvalá trvalá data nebo pro všechna data, která musí být rozložená na více uživatelů nebo zařízení, je k nejlepší možnost databáze na straně serveru téměř určitě. Mezi možnosti patří:

* Relační databáze SQL
* Ukládání hodnot klíče
* Úložiště objektů BLOB
* Úložiště tabulek

Po uložení dat v databázi může nový okruh spustit uživatel kdykoli. Data uživatele jsou zachována a k dispozici v jakémkoli novém okruhu.

Další informace o možnostech Azure Data Storage najdete v [dokumentaci Azure Storage](/azure/storage/) a v [databázích Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>zprostředkovatele identity

Pro přechodná data představující stav navigace modelujte data jako součást adresy URL. Mezi příklady stavu zahrnutých v adrese URL patří:

* ID zobrazené entity
* Aktuální číslo stránky v mřížce na stránkovaném webu

Obsah panelu Adresa prohlížeče se zachová:

* Pokud uživatel ručně znovu načte stránku.
* Pokud dojde k nedostupnosti webového serveru&mdash;bude uživatel muset stránku znovu načíst, aby se mohl připojit k jinému serveru.

Informace o definování vzorů adres URL pomocí direktivy `@page` naleznete v tématu <xref:blazor/routing>.

### <a name="client-side-in-the-browser"></a>Klientská strana v prohlížeči

Pro přechodná data, která uživatel aktivně vytváří, je běžným záložním úložištěm `localStorage` a `sessionStorage` kolekce v prohlížeči. Pokud dojde k opuštění okruhu, nepotřebuje aplikace spravovat nebo vymazat uložený stav, což je výhodou pro úložiště na straně serveru.

> [!NOTE]
> "Klientská strana" v této části odkazuje na scénáře na straně klienta v prohlížeči, nikoli na [model hostováníBlazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly). `localStorage` a `sessionStorage` lze použít v aplikacích Blazor WebAssembly, ale pouze psaním vlastního kódu nebo pomocí balíčku třetí strany.

`localStorage` a `sessionStorage` se liší následujícím způsobem:

* `localStorage` je vymezen v prohlížeči uživatele. Pokud uživatel stránku znovu načte nebo zavře a znovu otevře prohlížeč, stav přetrvává. Pokud uživatel otevře více karet prohlížeče, stav se sdílí napříč kartami. Data v `localStorage` přetrvají, dokud je explicitně neodstraníte.
* `sessionStorage` je vymezen na kartu prohlížeče uživatele. Pokud uživatel znovu načte kartu, stav se přetrvá. Pokud uživatel zavře kartu nebo prohlížeč, dojde ke ztrátě stavu. Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.

Obecně je `sessionStorage` bezpečnější použít. `sessionStorage` se vyhnete riziku, že uživatel otevře více karet a narazí na následující:

* Chyby v úložišti stavů napříč kartami.
* Matoucí chování, když karta Přepisuje stav jiných karet.

`localStorage` je lepší volbou v případě, že aplikace musí zachovat stav v rámci zavírání a znovu otevřít prohlížeč.

Upozornění pro použití úložiště prohlížeče:

* Podobně jako při použití databáze na straně serveru je načítání a ukládání dat asynchronní.
* Na rozdíl od databáze na straně serveru není úložiště během předgenerování k dispozici, protože požadovaná stránka v prohlížeči neexistuje během fáze předvykreslování.
* Úložiště několika kilobajtů dat je pro Blazor serverových aplikací přijatelné. Po několika kilobajtech je potřeba vzít v úvahu dopad na výkon, protože data se načítají a ukládají v síti.
* Uživatelé můžou data zobrazit nebo s nimi manipulovat. [Ochrana dat](xref:security/data-protection/introduction) ASP.NET Core může riziko zmírnit.

## <a name="third-party-browser-storage-solutions"></a>Řešení úložiště v prohlížeči třetích stran

Balíčky NuGet třetích stran poskytují rozhraní API pro práci s `localStorage` a `sessionStorage`.

Je vhodné zvážit výběr balíčku, který transparentně používá [ochranu dat](xref:security/data-protection/introduction)ASP.NET Core. ASP.NET Core ochrana dat šifruje uložená data a snižuje potenciální riziko manipulace s uloženými daty. Pokud jsou data serializovaná ve formátu JSON uložená ve formě prostého textu, uživatelé můžou data zobrazit pomocí vývojářských nástrojů pro prohlížeč a také upravovat uložená data. Zabezpečení dat se vždy nejedná o problém, protože data můžou být triviální v podstatě. Například čtení nebo úprava uložené barvy prvku uživatelského rozhraní není významným bezpečnostním rizikem pro uživatele nebo organizaci. Neumožňuje uživatelům kontrolovat *citlivá data*a manipulovat s nimi.

## <a name="protected-browser-storage-experimental-package"></a>Experimentální balíček chráněného úložiště prohlížeče

Příklad balíčku NuGet, který poskytuje [ochranu dat](xref:security/data-protection/introduction) pro `localStorage` a `sessionStorage` je [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` je nepodporovaný experimentální balíček nevhodný pro použití v produkčním prostředí.

### <a name="installation"></a>Instalace

Instalace balíčku `Microsoft.AspNetCore.ProtectedBrowserStorage`:

1. V projektu Blazor server aplikace přidejte odkaz na balíček do [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. V HTML nejvyšší úrovně (například v souboru *Pages/_Host. cshtml* ve výchozí šabloně projektu) přidejte následující značku `<script>`:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. V metodě `Startup.ConfigureServices` volejte `AddProtectedBrowserStorage` a přidejte `localStorage` a `sessionStorage` služby do kolekce služeb:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Ukládání a načítání dat v rámci součásti

V každé součásti, která vyžaduje načtení nebo uložení dat do úložiště prohlížeče, použijte [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) pro vložení instance některého z následujících prvků:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Volba závisí na tom, které záložní úložiště chcete použít. V následujícím příkladu se používá `sessionStorage`:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Příkaz `@using` lze umístit do souboru *_Imports. Razor* místo do součásti. Použití souboru *_Imports. Razor* zpřístupňuje obor názvů pro větší segmenty aplikace nebo celé aplikace.

Chcete-li zachovat hodnotu `_currentCount` v součásti `Counter` šablony projektu, upravte metodu `IncrementCount` na použití `ProtectedSessionStore.SetAsync`:

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobné. Pro aplikace je pravděpodobnější ukládání celých objektů modelu, které zahrnují komplexní stav. `ProtectedSessionStore` automaticky serializovat a deserializovat data JSON.

V předchozím příkladu kódu se `_currentCount` data ukládají jako `sessionStorage['count']` v prohlížeči uživatele. Data nejsou uložená ve formátu prostého textu, ale jsou chráněná pomocí [ochrany dat](xref:security/data-protection/introduction)ASP.NET Core. Zašifrovaná data lze zobrazit, pokud je `sessionStorage['count']` vyhodnocována v konzole pro vývojáře v prohlížeči.

Chcete-li obnovit `_currentCount` data v případě, že se uživatel vrátí do `Counter` komponenty později (včetně, pokud jsou na zcela novém okruhu), použijte `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Pokud parametry komponenty obsahují navigační stav, zavolejte `ProtectedSessionStore.GetAsync` a přiřaďte výsledek v `OnParametersSetAsync`, nikoli `OnInitializedAsync`. `OnInitializedAsync` se volá pouze jednou při prvním vytvoření instance komponenty. `OnInitializedAsync` se později nevolá, pokud uživatel přejde na jinou adresu URL a zůstane na stejné stránce. Další informace naleznete v tématu <xref:blazor/lifecycle>.

> [!WARNING]
> Příklady v této části fungují pouze v případě, že server nemá povolené předvykreslování. Pokud je povoleno předvykreslování, je vygenerována chyba podobná této:
>
> > V tuto chvíli nelze vydat volání interoperability JavaScriptu. Důvodem je to, že se komponenta předem vykreslí.
>
> Buď zakažte předvykreslování nebo přidejte další kód pro práci s předvykreslováním. Další informace o psaní kódu, který funguje s předvykreslováním, najdete v části [popisovač předvykreslování](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Zpracování stavu načítání

Vzhledem k tomu, že je úložiště prohlížeče asynchronní (přístup prostřednictvím připojení k síti), je vždy časový interval, než se data načtou a budou k dispozici pro použití komponentou. Nejlepších výsledků dosáhnete, když při načítání vykreslíte zprávu o stavu načítání, místo aby se zobrazila prázdná nebo výchozí data.

Jedním z těchto způsobů je sledovat, jestli se data `null` (pořád se načítají), nebo ne. Ve výchozí součásti `Counter` je počet uchováván v `int`. Nastavit `_currentCount` s možnou hodnotou null přidáním otazníku (`?`) do typu (`int`):

```csharp
private int? _currentCount;
```

Namísto nepodmíněného zobrazení tlačítka počet a **přírůstek** vyberte, zda chcete tyto prvky zobrazit pouze v případě, že jsou data načtena:

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

### <a name="handle-prerendering"></a>Zpracovat předvykreslování

Během předvykreslování:

* Interaktivní připojení k prohlížeči uživatele neexistuje.
* Prohlížeč ještě nemá stránku, ve které může spustit kód jazyka JavaScript.

`localStorage` nebo `sessionStorage` nejsou během předvykreslování k dispozici. Pokud se komponenta pokusí o interakci s úložištěm, je vygenerována chyba podobná této:

> V tuto chvíli nelze vydat volání interoperability JavaScriptu. Důvodem je to, že se komponenta předem vykreslí.

Jedním ze způsobů, jak chybu vyřešit, je zakázat předvykreslování. To je obvykle nejlepší volba, pokud aplikace využívá úložiště založené na prohlížeči. Předvykreslování přináší složitost a nevýhoduje aplikaci, protože aplikace nemůže využít žádný užitečný obsah, dokud nebudou k dispozici `localStorage` nebo `sessionStorage`.

Chcete-li zakázat předvykreslování, otevřete soubor *Pages/_Host. cshtml* a změňte volání `render-mode` pomocníka značky `Component` na `Server`.

Předvykreslování může být užitečné pro jiné stránky, které nepoužívají `localStorage` ani `sessionStorage`. Aby bylo možné předvykreslování povolit, odložte operaci načtení, dokud se prohlížeč nepřipojí k okruhu. Následuje příklad uložení hodnoty čítače:

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Rozložte zachování stavu na společné místo.

Pokud mnoho komponent spoléhá na úložiště založené na prohlížeči, často se kód zprostředkovatele stavu znovu implementuje vytvořením duplicit kódu. Jednou z možností, jak zabránit duplikaci kódu, je vytvoření *nadřazené komponenty poskytovatele stavu* , která zapouzdřuje logiku poskytovatele stavu. Podřízené komponenty mohou pracovat s trvalými daty bez ohledu na mechanismus trvalosti stavu.

V následujícím příkladu `CounterStateProvider` komponenty jsou data čítače trvalá:

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

Chcete-li použít komponentu `CounterStateProvider`, zabalte instanci součásti kolem jakékoli jiné komponenty, která vyžaduje přístup ke stavu čítače. Chcete-li zpřístupnit stav pro všechny součásti aplikace, zabalte `CounterStateProvider` komponentu kolem `Router` v součásti `App` (*App. Razor*):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Zabalené komponenty obdrží a můžou upravovat trvalý stav čítače. Následující součást `Counter` implementuje vzor:

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

Předchozí komponenta není nutná k interakci s `ProtectedBrowserStorage`, ani nefunguje se fází "načítání".

Chcete-li se vypořádat s předem popsaným způsobem, `CounterStateProvider` lze změnit tak, aby všechny komponenty, které data čítače spotřebují automaticky, pracovaly s předvykreslováním. Podrobnosti najdete v části o [předvykreslování popisovače](#handle-prerendering) .

Obecně se doporučuje model *nadřazené komponenty zprostředkovatele stavu* :

* Pro využívání stavu v mnoha dalších součástech.
* Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má být zachován.

Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší se vyhnout zpracování načítání a ukládání stavu globálně.
