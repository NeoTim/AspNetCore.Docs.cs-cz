---
title: Správa stavu ASP.NET Core Blazor
author: guardrex
description: Přečtěte si, jak zachovat stav v Blazor aplikacích na straně serveru.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/state-management
ms.openlocfilehash: 000736dde53670d1df76f41cc7cf4f95ef48800a
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800349"
---
# <a name="aspnet-core-blazor-state-management"></a>Správa stavu ASP.NET Core Blazor

Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor Server je stavová architektura aplikace. Ve většině případů aplikace udržuje průběžné připojení k serveru. Stav uživatele je uložený v paměti serveru v *okruhu*. 

Příkladem stavu drženého pro okruh uživatele jsou:

* Vykreslené uživatelské&mdash;rozhraní hierarchie instancí komponent a jejich poslední výstup vykreslování.
* Hodnoty všech polí a vlastností v instancích součástí.
* Data uchovávaná v instancích služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , která jsou vymezena okruhu.

> [!NOTE]
> Tento článek řeší trvalost stavu v Blazor aplikacích na straně serveru. Blazor aplikace na straně klienta můžou využít [trvalost stavu na straně klienta v prohlížeči](#client-side-in-the-browser) , ale kromě rozsahu tohoto článku vyžadují vlastní řešení nebo balíčky třetích stran.

## <a name="blazor-circuits"></a>Okruhy Blazor

Pokud se uživateli vyskytne Dočasná ztráta síťového připojení, Blazor se pokusí znovu připojit uživatele k původnímu okruhu, aby mohli i nadále používat aplikaci. Ale opětovné připojení uživatele k původnímu okruhu v paměti serveru není vždycky možné:

* Server nemůže trvale zachovávat odpojený okruh. Server musí uvolnit odpojený okruh po vypršení časového limitu nebo v případě, že je server pod tlakem paměti.
* V prostředích s více servery nasazení s vyrovnáváním zatížení můžou všechny požadavky na zpracování serveru být v daném okamžiku nedostupné. Jednotlivé servery můžou selhat nebo automaticky odebrat, pokud už nepotřebujete pro zpracování celkového objemu požadavků. Původní server nemusí být k dispozici, když se uživatel pokusí znovu připojit.
* Uživatel může zavřít a znovu otevřít prohlížeč nebo znovu načíst stránku, což odstraní všechny stavy uchovávané v paměti prohlížeče. Například hodnoty nastavené prostřednictvím volání interoperability JavaScriptu jsou ztraceny.

Když se uživatel nedá znovu připojit k původnímu okruhu, uživatel dostane nový okruh s prázdným stavem. Jedná se o ekvivalent zavření a opětovného otevření desktopové aplikace.

## <a name="preserve-state-across-circuits"></a>Zachovat stav napříč okruhy

V některých scénářích je žádoucí zachovat stav napříč okruhy. Aplikace může uchovávat důležitá data pro uživatele, pokud:

* Webový server bude nedostupný.
* V prohlížeči uživatele je nucen spustit nový okruh s novým webovým serverem.

Obecně platí, že udržování stavu napříč okruhy se vztahuje na scénáře, kdy uživatelé aktivně vytvářejí data, nikoli jenom čtení dat, která už existují.

Chcete-li zachovat stav nad rámec jednoho okruhu, *neukládejte pouze data v paměti serveru*. Aplikace musí uchovávat data do jiného umístění úložiště. Trvalost stavu není automatické&mdash;, musíte provést kroky při vývoji aplikace pro implementaci stavového uchovávání dat.

Trvalost dat se obvykle vyžaduje jenom pro stav vysoké hodnoty, který uživatelé vynaložené úsilím vytvořit. V následujících příkladech trvalého stavu ušetříte čas nebo pomůcky při komerčních činnostech:

* U více kroků WebForm &ndash; je časově náročné, aby uživatel znovu zadal data pro několik dokončených kroků procesu s více kroky, pokud dojde ke ztrátě jejich stavu. Uživatel ztratí stav v tomto scénáři, pokud přechází z formuláře s více kroky a později se vrátí do formuláře.
* Nákupní košík &ndash; : všechny obchodní důležité komponenty aplikace, které představují potenciální tržby, se dají udržovat. Uživatel, který ztratí svůj stav, a tedy i jeho nákupní košík, může koupit méně produktů nebo služeb při návratu do webu později.

Obvykle není nutné zachovávat snadno-znovu vytvořený stav, jako je například uživatelské jméno zadané do přihlašovacího dialogu, který nebyl odeslán.

> [!IMPORTANT]
> Aplikace může zachovat jenom *stav aplikace*. Uživatelská rozhraní nelze zachovat, například instance komponent a jejich stromy vykreslování. Komponenty a stromy vykreslování nejsou obecně serializovatelné. Aby bylo možné zachovat podobný text jako stav uživatelského rozhraní, jako jsou například rozbalené uzly prvku TreeView, aplikace musí mít vlastní kód pro modelování chování jako serializovatelný stav aplikace.

## <a name="where-to-persist-state"></a>Kam zachovat stav

Existují tři společná umístění pro zachování stavu v Blazor aplikaci na straně serveru. Každý přístup nejlépe vyhovuje různým scénářům a má odlišná omezení:

* [Na straně serveru v databázi](#server-side-in-a-database)
* [Adresa URL](#url)
* [Klientská strana v prohlížeči](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Na straně serveru v databázi

Pro trvalá trvalá data nebo pro všechna data, která musí být rozložená na více uživatelů nebo zařízení, je k nejlepší možnost databáze na straně serveru téměř určitě. Vaše možnosti jsou:

* Relační databáze SQL
* Úložiště hodnot klíčů
* Úložiště objektů BLOB
* Úložiště tabulek

Po uložení dat v databázi může nový okruh spustit uživatel kdykoli. Data uživatele jsou zachována a k dispozici v jakémkoli novém okruhu.

Další informace o možnostech Azure Data Storage najdete v [dokumentaci Azure Storage](/azure/storage/) a v [databázích Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>Adresa URL

Pro přechodná data představující stav navigace modelujte data jako součást adresy URL. Mezi příklady stavu zahrnutých v adrese URL patří:

* ID zobrazené entity
* Aktuální číslo stránky v mřížce na stránkovaném webu

Obsah panelu Adresa prohlížeče se zachová:

* Pokud uživatel ručně znovu načte stránku.
* Pokud dojde k nedostupnosti&mdash;webového serveru, uživatel bude muset znovu načíst stránku, aby se mohl připojit k jinému serveru.

Informace o definování vzorů adres URL pomocí `@page` direktivy naleznete <xref:blazor/routing>v tématu.

### <a name="client-side-in-the-browser"></a>Klientská strana v prohlížeči

Pro přechodná data, která uživatel aktivně vytváří, je běžným záložním úložištěm prohlížeč `localStorage` a `sessionStorage` kolekce. Pokud dojde k opuštění okruhu, nepotřebuje aplikace spravovat nebo vymazat uložený stav, což je výhodou pro úložiště na straně serveru.

> [!NOTE]
> "Klientská strana" v této části odkazuje na scénáře na straně klienta v prohlížeči, nikoli na [Blazor model hostování na straně](xref:blazor/hosting-models#client-side)klienta. `localStorage`a `sessionStorage` dá se použít v Blazor aplikacích na straně klienta, ale jenom psaním vlastního kódu nebo pomocí balíčku třetí strany.

`localStorage`a `sessionStorage` liší se následujícím způsobem:

* `localStorage`je vymezen v prohlížeči uživatele. Pokud uživatel stránku znovu načte nebo zavře a znovu otevře prohlížeč, stav přetrvává. Pokud uživatel otevře více karet prohlížeče, stav se sdílí napříč kartami. Data přetrvají `localStorage` do doby, než je explicitně zrušeno.
* `sessionStorage`je vymezen na kartu prohlížeče uživatele. Pokud uživatel znovu načte kartu, stav se přetrvá. Pokud uživatel zavře kartu nebo prohlížeč, dojde ke ztrátě stavu. Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.

`sessionStorage` Obecně je bezpečnější použít. `sessionStorage`Vyhněte se riziku, že uživatel otevře více karet a narazí na následující:

* Chyby v úložišti stavů napříč kartami.
* Matoucí chování, když karta Přepisuje stav jiných karet.

`localStorage`je lepší volbou, pokud aplikace musí zachovat stav v rámci zavírání a znovu otevřít prohlížeč.

Upozornění pro použití úložiště prohlížeče:

* Podobně jako při použití databáze na straně serveru je načítání a ukládání dat asynchronní.
* Na rozdíl od databáze na straně serveru není úložiště během předgenerování k dispozici, protože požadovaná stránka v prohlížeči neexistuje během fáze předvykreslování.
* Úložiště několika kilobajtů dat je pro Blazor aplikace na straně serveru přijatelné. Po několika kilobajtech je potřeba vzít v úvahu dopad na výkon, protože data se načítají a ukládají v síti.
* Uživatelé můžou data zobrazit nebo s nimi manipulovat. [Ochrana dat](xref:security/data-protection/introduction) ASP.NET Core může riziko zmírnit.

## <a name="third-party-browser-storage-solutions"></a>Řešení úložiště v prohlížeči třetích stran

Balíčky NuGet třetích stran poskytují rozhraní API pro práci s `localStorage` a `sessionStorage`.

Je vhodné zvážit výběr balíčku, který transparentně používá [ochranu dat](xref:security/data-protection/introduction)ASP.NET Core. ASP.NET Core ochrana dat šifruje uložená data a snižuje potenciální riziko manipulace s uloženými daty. Pokud jsou data serializovaná ve formátu JSON uložená ve formě prostého textu, uživatelé můžou data zobrazit pomocí vývojářských nástrojů pro prohlížeč a také upravovat uložená data. Zabezpečení dat se vždy nejedná o problém, protože data můžou být triviální v podstatě. Například čtení nebo úprava uložené barvy prvku uživatelského rozhraní není významným bezpečnostním rizikem pro uživatele nebo organizaci. Neumožňuje uživatelům kontrolovat *citlivá data*a manipulovat s nimi.

## <a name="protected-browser-storage-experimental-package"></a>Experimentální balíček chráněného úložiště prohlížeče

Příklad balíčku NuGet, který poskytuje [ochranu dat](xref:security/data-protection/introduction) pro `localStorage` a `sessionStorage` je [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`je nepodporovaný experimentální balíček nevhodný pro produkční použití v tuto chvíli.

### <a name="installation"></a>Instalace

Postup instalace `Microsoft.AspNetCore.ProtectedBrowserStorage` balíčku:

1. V projektu aplikace Blazor na straně serveru přidejte odkaz na balíček do [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. V HTML nejvyšší úrovně (například v souboru *Pages/_Host. cshtml* ve výchozí šabloně projektu) přidejte následující `<script>` značku:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. V metodě zavolejte `AddProtectedBrowserStorage` na Přidat `localStorage` a `sessionStorage` služby do kolekce služeb: `Startup.ConfigureServices`

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Ukládání a načítání dat v rámci součásti

V každé součásti, která vyžaduje načtení nebo uložení dat do úložiště prohlížeče, [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) použijte k vložení instance některého z následujících prvků:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Volba závisí na tom, které záložní úložiště chcete použít. V následujícím příkladu `sessionStorage` se používá:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Příkaz lze umístit do souboru *_Imports. Razor* místo v součásti. `@using` Použití souboru *_Imports. Razor* zpřístupňuje obor názvů pro větší segmenty aplikace nebo celé aplikace.

Chcete-li `currentCount` zachovat hodnotu `Counter` v součásti šablony `IncrementCount` projektu, upravte metodu, kterou chcete použít `ProtectedSessionStore.SetAsync`:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobné. Pro aplikace je pravděpodobnější ukládání celých objektů modelu, které zahrnují komplexní stav. `ProtectedSessionStore`automaticky serializace a deserializace dat JSON.

V předchozím příkladu `currentCount` kódu se data ukládají jako `sessionStorage['count']` v prohlížeči uživatele. Data nejsou uložená ve formátu prostého textu, ale jsou chráněná pomocí [ochrany dat](xref:security/data-protection/introduction)ASP.NET Core. Šifrovaná data se můžou zobrazit, pokud `sessionStorage['count']` se vyhodnotí v konzole pro vývojáře v prohlížeči.

Chcete-li `currentCount` obnovit data, pokud se uživatel vrátí `Counter` do komponenty později (včetně toho, jestli jsou na zcela novém okruhu) `ProtectedSessionStore.GetAsync`, použijte:

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Pokud parametry komponenty obsahují stav navigace, zavolejte `ProtectedSessionStore.GetAsync` a přiřaďte výsledek v `OnParametersSetAsync`, nikoli `OnInitializedAsync`. `OnInitializedAsync`je volána pouze jednou při prvním vytvoření instance komponenty. `OnInitializedAsync`není voláno později, pokud uživatel přejde na jinou adresu URL a zůstane na stejné stránce.

> [!WARNING]
> Příklady v této části fungují pouze v případě, že server nemá povolené předvykreslování. Pokud je povoleno předvykreslování, je vygenerována chyba podobná této:
>
> > V tuto chvíli nelze vydat volání interoperability JavaScriptu. Důvodem je to, že se komponenta předem vykreslí.
>
> Buď zakažte předvykreslování nebo přidejte další kód pro práci s předvykreslováním. Další informace o psaní kódu, který funguje s předvykreslováním, najdete v části [popisovač předvykreslování](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Zpracování stavu načítání

Vzhledem k tomu, že je úložiště prohlížeče asynchronní (přístup prostřednictvím připojení k síti), je vždy časový interval, než se data načtou a budou k dispozici pro použití komponentou. Nejlepších výsledků dosáhnete, když při načítání vykreslíte zprávu o stavu načítání, místo aby se zobrazila prázdná nebo výchozí data.

Jedním z těchto způsobů je sledovat, jestli jsou `null` data (pořád se načítají), nebo ne. Ve výchozí `Counter` součásti je počet uchováván `int`v. Převést `currentCount` na typ s možnou hodnotou null`?`přidáním otazníku ()`int`do typu ():

```csharp
private int? currentCount;
```

Namísto nepodmíněného zobrazení tlačítka počet a **přírůstek** vyberte, zda chcete tyto prvky zobrazit pouze v případě, že jsou data načtena:

```cshtml
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

`localStorage`nebo `sessionStorage` nejsou během předvykreslování k dispozici. Pokud se komponenta pokusí o interakci s úložištěm, je vygenerována chyba podobná této:

> V tuto chvíli nelze vydat volání interoperability JavaScriptu. Důvodem je to, že se komponenta předem vykreslí.

Jedním ze způsobů, jak chybu vyřešit, je zakázat předvykreslování. To je obvykle nejlepší volba, pokud aplikace využívá úložiště založené na prohlížeči. Předvykreslování přináší složitost a nevýhoduje aplikaci, protože aplikace nemůže využít žádný užitečný obsah do `localStorage` té `sessionStorage` doby, než je k dispozici.

Chcete-li zakázat předvykreslování, otevřete soubor *Pages/_Host. cshtml* a změňte volání `Html.RenderComponentAsync<App>(RenderMode.Server)`na.

Předvykreslování může být užitečné pro jiné stránky, které `localStorage` nepoužívají `sessionStorage`nebo. Aby bylo možné předvykreslování povolit, odložte operaci načtení, dokud se prohlížeč nepřipojí k okruhu. Následuje příklad uložení hodnoty čítače:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore
@inject IComponentContext ComponentContext

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isWaitingForConnection;

    protected override async Task OnInitializedAsync()
    {
        if (ComponentContext.IsConnected)
        {
            // It looks like the app isn't prerendering, so the data can be
            // immediately loaded from browser storage.
            await LoadStateAsync();
        }
        else
        {
            // Prerendering is in progress, so the app defers the load operation
            // until later.
            isWaitingForConnection = true;
        }
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        // By this stage, the client has connected back to the server, and
        // browser services are available. If the app didn't load the data earlier,
        // the app should do so now and then trigger a new render.
        if (firstRender && isWaitingForConnection)
        {
            isWaitingForConnection = false;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Rozložte zachování stavu na společné místo.

Pokud mnoho komponent spoléhá na úložiště založené na prohlížeči, často se kód zprostředkovatele stavu znovu implementuje vytvořením duplicit kódu. Jednou z možností, jak zabránit duplikaci kódu, je vytvoření *nadřazené komponenty poskytovatele stavu* , která zapouzdřuje logiku poskytovatele stavu. Podřízené komponenty mohou pracovat s trvalými daty bez ohledu na mechanismus trvalosti stavu.

V následujícím příkladu `CounterStateProvider` komponenty jsou data čítače trvalá:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider` Komponenta zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.

Chcete-li `CounterStateProvider` použít komponentu, zabalte instanci součásti kolem jakékoli jiné komponenty, která vyžaduje přístup ke stavu čítače. Chcete-li zpřístupnit stav pro všechny `CounterStateProvider` součásti aplikace, zabalte komponentu `Router` kolem komponenty v `App` součásti (*App. Razor*):

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Zabalené komponenty obdrží a můžou upravovat trvalý stav čítače. Vzor implementuje `Counter` následující součást:

```cshtml
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

Chcete-li se vypořádat s předem popsaným způsobem, `CounterStateProvider` lze upravit tak, aby všechny součásti, které spotřebovávají data čítače, pracovaly automaticky s předem vykreslením. Podrobnosti najdete v části o [předvykreslování popisovače](#handle-prerendering) .

Obecně se doporučuje model *nadřazené komponenty zprostředkovatele stavu* :

* Pro využívání stavu v mnoha dalších součástech.
* Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má být zachován.

Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší se vyhnout zpracování načítání a ukládání stavu globálně.
