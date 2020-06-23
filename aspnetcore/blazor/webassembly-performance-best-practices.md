---
title: Osvědčené postupy pro vyASP.NET Coreení Blazor výkonu WebAssembly
author: pranavkm
description: Tipy pro zvýšení výkonu v Blazor aplikacích ASP.NET Core WebAssembly a předcházení běžným problémům s výkonem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 2b6d4e706856cb28f26c2502feca4f959ca4abac
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243028"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>Osvědčené postupy pro vyASP.NET Coreení Blazor výkonu WebAssembly

Od [Pranav Krishnamoorthy](https://github.com/pranavkm)

Tento článek poskytuje pokyny pro ASP.NET Core Blazor osvědčené postupy pro výkon WebAssembly.

## <a name="avoid-unnecessary-component-renders"></a>Vyhnout se zbytečnému vykreslování komponent

Blazorrozdílový algoritmus zabraňuje převykreslování komponenty, když algoritmus vnímat, že se komponenta nezměnila. Přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> pro jemně odstupňovanou kontrolu nad vykreslováním komponent.

Při vytváření součásti pouze uživatelského rozhraní, která se po počátečním vykreslení nemění, nakonfigurujte, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> aby vracela `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Většina aplikací nevyžaduje jemně odstupňovaný ovládací prvek, ale <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> dá se použít i k selektivnímu vygenerování součásti, která reaguje na událost uživatelského rozhraní.

V následujícím příkladu:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>je přepsána a nastavena na hodnotu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pole, které je zpočátku `false` při načtení součásti.
* Když je vybráno tlačítko, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> je nastaveno na `true` , což vynutí, aby se komponenta znovu vykreslila s aktualizovanou `currentCount` .
* Ihned po <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> opětovném vygenerování nastaví hodnotu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> zpět na, `false` aby se zabránilo dalšímu novému vygenerování, až do okamžiku, kdy bude tlačítko příště vybráno.

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

Další informace naleznete v tématu <xref:blazor/components/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Virtualizace opakovaně použitelných fragmentů

Komponenty nabízejí pohodlný přístup k vytvoření opakovaně použitelných fragmentů kódu a značek. Obecně doporučujeme vytváření individuálních komponent, které nejlépe odpovídají požadavkům aplikace. Jedna výstraha znamená, že každá další podřízená komponenta přispívá k celkovému času potřebnýmu k vykreslení nadřazené komponenty. U většiny aplikací je další režie zanedbatelná. Aplikace, které vytváří velký počet komponent, by měly zvážit použití strategií ke snížení režijních nákladů na zpracování, jako je omezení počtu vykreslených komponent.

Například mřížka nebo seznam, který vykresluje stovky řádků obsahujících komponenty, je náročné na vykreslování procesoru. Zvažte možnost Virtualizovat rozložení mřížky nebo seznamu tak, aby se v určitou dobu vykreslila pouze podmnožina komponent. Příklad vykreslování podmnožiny komponent naleznete v následujících součástech v [ `Virtualization` ukázkové aplikaci (ASPNET/Samples úložiště GitHub)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize`Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): komponenta napsaná v jazyce C#, která implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> pro vykreslení sady datových řádků počasí na základě posouvání uživatele.
* `FetchData`Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): používá `Virtualize` komponentu k zobrazení 25 řádků dat počasí současně.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Vyhněte se interoperabilitě JavaScriptu při zařazování dat

V Blazor rámci objektu WebAssembly musí volání Interop jazyka JavaScript (js) procházet hranici WebAssembly-js. Serializace a deserializace obsahu napříč dvěma kontexty vytváří režijní náklady na zpracování pro aplikaci. Časté volání interoperability JS často nepříznivě ovlivňuje výkon. Chcete-li omezit zařazování dat napříč hranicí, určete, zda aplikace může konsolidovat mnoho malých datových částí do jedné velké datové části, aby nedocházelo k velkému rozsahu přepínání kontextu mezi službami WebAssembly a JS.

## <a name="use-systemtextjson"></a>Použít System.Text.Jsna

Blazorimplementace interoperability JS spoléhá na <xref:System.Text.Json> , což je vysoce výkonné knihovny serializace JSON s neomezeným přidělením paměti. Použití <xref:System.Text.Json> nemá za následek další velikost datové části aplikace nad přidáním jedné nebo více alternativních knihoven JSON.

Pokyny k migraci najdete v tématu [Postup migrace z `Newtonsoft.Json` na `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>V případě potřeby použijte synchronní a nezařazené rozhraní API pro interoperabilitu JS.

BlazorWebAssembly nabízí dvě další verze nástroje <xref:Microsoft.JSInterop.IJSRuntime> v rámci jedné verze, která je dostupná pro Blazor serverové aplikace:

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>umožňuje vyvolání volání interoperability JS synchronně, což má méně režie než asynchronní verze:

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>povoluje volání Interop v nezařazeném JS:

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > I když použití <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> má minimální režii spojené s těmito rozhraními API, jsou aktuálně nedokumentovaná rozhraní API pro interakci s těmito rozhraními API a můžou v budoucích verzích podléhat zásadním změnám.

## <a name="reduce-app-size"></a>Zmenšit velikost aplikace

### <a name="intermediate-language-il-linking"></a>Propojování IL (Intermediate Language)

[Propojení Blazor Aplikace WebAssembly](xref:blazor/host-and-deploy/configure-linker) omezuje velikost Aplikace oříznutím nepoužívaného kódu v binárních souborech aplikace. Ve výchozím nastavení je linker povolen pouze při sestavování v `Release` konfiguraci. Pokud to chcete využít, publikujte aplikaci pro nasazení pomocí [`dotnet publish`](/dotnet/core/tools/dotnet-publish) příkazu s možností [-c |--konfigurace](/dotnet/core/tools/dotnet-publish#options) nastavenou na `Release` :

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a>Komprese

Když Blazor je publikována aplikace WebAssembly, výstup je během publikování staticky komprimován, aby se snížila velikost aplikace a odstranila se režie pro kompresi za běhu. Blazorspoléhá na server, aby provedl negotation obsahu a sloužil staticky komprimovaným souborům.

Po nasazení aplikace ověřte, jestli aplikace obsluhuje komprimované soubory. Zkontrolujte kartu síť v Vývojářské nástroje prohlížeče a ověřte, zda jsou soubory obsluhovány `Content-Encoding: br` nebo `Content-Encoding: gz` . Pokud hostitel neobsluhuje komprimované soubory, postupujte podle pokynů v tématu <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Zakázat nepoužívané funkce

BlazorModul runtime pro WebAssembly obsahuje následující funkce .NET, které je možné zakázat, pokud je aplikace nepotřebuje pro menší velikost datové části:

* K dispozici je datový soubor pro správné informace o časovém pásmu. Pokud aplikace tuto funkci nevyžaduje, zvažte její zakázání nastavením `BlazorEnableTimeZoneSupport` vlastnosti MSBuild v souboru projektu aplikace na `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* K zajištění správného fungování rozhraní API, jako je například práce, jsou zahrnuty informace o kolaci <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> . Pokud jste si jisti, že aplikace nevyžaduje data kolace, zvažte její zakázání nastavením `BlazorWebAssemblyPreserveCollationData` vlastnosti MSBuild v souboru projektu aplikace na `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
