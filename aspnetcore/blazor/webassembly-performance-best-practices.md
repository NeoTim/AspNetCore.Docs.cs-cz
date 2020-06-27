---
title: Blazor WebAssemblyOsvědčené postupy týkající se ASP.NET Core výkonu
author: pranavkm
description: Tipy pro zvýšení výkonu v Blazor WebAssembly aplikacích ASP.NET Core a předcházení běžným problémům s výkonem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: f7bd0d356030e6ddb95c77d7376995320e3ec40e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401880"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="decf7-103">Blazor WebAssemblyOsvědčené postupy týkající se ASP.NET Core výkonu</span><span class="sxs-lookup"><span data-stu-id="decf7-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="decf7-104">Od [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="decf7-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="decf7-105">Tento článek poskytuje pokyny pro ASP.NET Core Blazor WebAssembly osvědčené postupy pro výkon.</span><span class="sxs-lookup"><span data-stu-id="decf7-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="decf7-106">Vyhnout se zbytečnému vykreslování komponent</span><span class="sxs-lookup"><span data-stu-id="decf7-106">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="decf7-107">rozdílový algoritmus zabraňuje převykreslování komponenty, když algoritmus vnímat, že se komponenta nezměnila.</span><span class="sxs-lookup"><span data-stu-id="decf7-107">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="decf7-108">Přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> pro jemně odstupňovanou kontrolu nad vykreslováním komponent.</span><span class="sxs-lookup"><span data-stu-id="decf7-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="decf7-109">Při vytváření součásti pouze uživatelského rozhraní, která se po počátečním vykreslení nemění, nakonfigurujte, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> aby vracela `false` :</span><span class="sxs-lookup"><span data-stu-id="decf7-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="decf7-110">Většina aplikací nevyžaduje jemně odstupňovaný ovládací prvek, ale <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> dá se použít k selektivnímu vygenerování součásti, která reaguje na událost uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="decf7-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="decf7-111">Použití <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> může být také důležité ve scénářích, kdy se vykresluje velký počet komponent.</span><span class="sxs-lookup"><span data-stu-id="decf7-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="decf7-112">Vezměte v úvahu mřížku, kde použití <xref:Microsoft.AspNetCore.Components.EventCallback> v jedné součásti v jedné buňce v mřížce volá <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> mřížku.</span><span class="sxs-lookup"><span data-stu-id="decf7-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="decf7-113">Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> způsobí opakované vykreslení všech podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="decf7-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="decf7-114">Pokud má pouze malý počet buněk vyžadovat převykreslování, použijte, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> aby nedocházelo ke snížení výkonu zbytečných vykreslení.</span><span class="sxs-lookup"><span data-stu-id="decf7-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="decf7-115">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="decf7-115">In the following example:</span></span>

* <span data-ttu-id="decf7-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>je přepsána a nastavena na hodnotu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pole, které je zpočátku `false` při načtení součásti.</span><span class="sxs-lookup"><span data-stu-id="decf7-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="decf7-117">Když je vybráno tlačítko, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> je nastaveno na `true` , což vynutí, aby se komponenta znovu vykreslila s aktualizovanou `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="decf7-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="decf7-118">Ihned po <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> opětovném vygenerování nastaví hodnotu <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> zpět na, `false` aby se zabránilo dalšímu novému vygenerování, až do okamžiku, kdy bude tlačítko příště vybráno.</span><span class="sxs-lookup"><span data-stu-id="decf7-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

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

<span data-ttu-id="decf7-119">Další informace naleznete v tématu <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="decf7-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="decf7-120">Virtualizace opakovaně použitelných fragmentů</span><span class="sxs-lookup"><span data-stu-id="decf7-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="decf7-121">Komponenty nabízejí pohodlný přístup k vytvoření opakovaně použitelných fragmentů kódu a značek.</span><span class="sxs-lookup"><span data-stu-id="decf7-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="decf7-122">Obecně doporučujeme vytváření individuálních komponent, které nejlépe odpovídají požadavkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="decf7-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="decf7-123">Jedna výstraha znamená, že každá další podřízená komponenta přispívá k celkovému času potřebnýmu k vykreslení nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="decf7-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="decf7-124">U většiny aplikací je další režie zanedbatelná.</span><span class="sxs-lookup"><span data-stu-id="decf7-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="decf7-125">Aplikace, které vytváří velký počet komponent, by měly zvážit použití strategií ke snížení režijních nákladů na zpracování, jako je omezení počtu vykreslených komponent.</span><span class="sxs-lookup"><span data-stu-id="decf7-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="decf7-126">Například mřížka nebo seznam, který vykresluje stovky řádků obsahujících komponenty, je náročné na vykreslování procesoru.</span><span class="sxs-lookup"><span data-stu-id="decf7-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="decf7-127">Zvažte možnost Virtualizovat rozložení mřížky nebo seznamu tak, aby se v určitou dobu vykreslila pouze podmnožina komponent.</span><span class="sxs-lookup"><span data-stu-id="decf7-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="decf7-128">Příklad vykreslování podmnožiny komponent naleznete v následujících součástech v [ `Virtualization` ukázkové aplikaci (ASPNET/Samples úložiště GitHub)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="decf7-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="decf7-129">`Virtualize`Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): komponenta napsaná v jazyce C#, která implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> pro vykreslení sady datových řádků počasí na základě posouvání uživatele.</span><span class="sxs-lookup"><span data-stu-id="decf7-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="decf7-130">`FetchData`Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): používá `Virtualize` komponentu k zobrazení 25 řádků dat počasí současně.</span><span class="sxs-lookup"><span data-stu-id="decf7-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="decf7-131">Vyhněte se interoperabilitě JavaScriptu při zařazování dat</span><span class="sxs-lookup"><span data-stu-id="decf7-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="decf7-132">V rozhraní Blazor WebAssembly interoperabilita volání JavaScript (js) musí procházet hranici WebAssembly-js.</span><span class="sxs-lookup"><span data-stu-id="decf7-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="decf7-133">Serializace a deserializace obsahu napříč dvěma kontexty vytváří režijní náklady na zpracování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="decf7-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="decf7-134">Časté volání interoperability JS často nepříznivě ovlivňuje výkon.</span><span class="sxs-lookup"><span data-stu-id="decf7-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="decf7-135">Chcete-li omezit zařazování dat napříč hranicí, určete, zda aplikace může konsolidovat mnoho malých datových částí do jedné velké datové části, aby nedocházelo k velkému rozsahu přepínání kontextu mezi službami WebAssembly a JS.</span><span class="sxs-lookup"><span data-stu-id="decf7-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="decf7-136">Použít System.Text.Jsna</span><span class="sxs-lookup"><span data-stu-id="decf7-136">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="decf7-137">implementace interoperability JS spoléhá na <xref:System.Text.Json> , což je vysoce výkonné knihovny serializace JSON s neomezeným přidělením paměti.</span><span class="sxs-lookup"><span data-stu-id="decf7-137">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="decf7-138">Použití <xref:System.Text.Json> nemá za následek další velikost datové části aplikace nad přidáním jedné nebo více alternativních knihoven JSON.</span><span class="sxs-lookup"><span data-stu-id="decf7-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="decf7-139">Pokyny k migraci najdete v tématu [Postup migrace z `Newtonsoft.Json` na `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="decf7-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="decf7-140">V případě potřeby použijte synchronní a nezařazené rozhraní API pro interoperabilitu JS.</span><span class="sxs-lookup"><span data-stu-id="decf7-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor WebAssembly<span data-ttu-id="decf7-141">nabízí dvě další verze nástroje <xref:Microsoft.JSInterop.IJSRuntime> nad jedinou verzí dostupnou pro Blazor Server aplikace:</span><span class="sxs-lookup"><span data-stu-id="decf7-141"> offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="decf7-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime>umožňuje vyvolání volání interoperability JS synchronně, což má méně režie než asynchronní verze:</span><span class="sxs-lookup"><span data-stu-id="decf7-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

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

* <span data-ttu-id="decf7-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>povoluje volání Interop v nezařazeném JS:</span><span class="sxs-lookup"><span data-stu-id="decf7-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
  > <span data-ttu-id="decf7-144">I když použití <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> má minimální režii spojené s těmito rozhraními API, jsou aktuálně nedokumentovaná rozhraní API pro interakci s těmito rozhraními API a můžou v budoucích verzích podléhat zásadním změnám.</span><span class="sxs-lookup"><span data-stu-id="decf7-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="decf7-145">Zmenšit velikost aplikace</span><span class="sxs-lookup"><span data-stu-id="decf7-145">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="decf7-146">Propojování IL (Intermediate Language)</span><span class="sxs-lookup"><span data-stu-id="decf7-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="decf7-147">[Propojení Blazor WebAssembly aplikace](xref:blazor/host-and-deploy/configure-linker) zmenšuje velikost Aplikace oříznutím nepoužívaného kódu v binárních souborech aplikace.</span><span class="sxs-lookup"><span data-stu-id="decf7-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="decf7-148">Ve výchozím nastavení je linker povolen pouze při sestavování v `Release` konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="decf7-148">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="decf7-149">Pokud to chcete využít, publikujte aplikaci pro nasazení pomocí [`dotnet publish`](/dotnet/core/tools/dotnet-publish) příkazu s možností [-c |--konfigurace](/dotnet/core/tools/dotnet-publish#options) nastavenou na `Release` :</span><span class="sxs-lookup"><span data-stu-id="decf7-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a><span data-ttu-id="decf7-150">Komprese</span><span class="sxs-lookup"><span data-stu-id="decf7-150">Compression</span></span>

<span data-ttu-id="decf7-151">Při Blazor WebAssembly publikování aplikace je výstup během publikování staticky komprimován, aby se snížila velikost aplikace a odstranila se režie pro kompresi za běhu.</span><span class="sxs-lookup"><span data-stu-id="decf7-151">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> Blazor<span data-ttu-id="decf7-152">spoléhá na server, aby provedl negotation obsahu a sloužil staticky komprimovaným souborům.</span><span class="sxs-lookup"><span data-stu-id="decf7-152"> relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="decf7-153">Po nasazení aplikace ověřte, jestli aplikace obsluhuje komprimované soubory.</span><span class="sxs-lookup"><span data-stu-id="decf7-153">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="decf7-154">Zkontrolujte kartu síť v Vývojářské nástroje prohlížeče a ověřte, zda jsou soubory obsluhovány `Content-Encoding: br` nebo `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="decf7-154">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="decf7-155">Pokud hostitel neobsluhuje komprimované soubory, postupujte podle pokynů v tématu <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="decf7-155">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="decf7-156">Zakázat nepoužívané funkce</span><span class="sxs-lookup"><span data-stu-id="decf7-156">Disable unused features</span></span>

Blazor WebAssembly<span data-ttu-id="decf7-157">modul runtime obsahuje následující funkce .NET, které je možné zakázat, pokud je aplikace nepotřebuje pro menší velikost datové části:</span><span class="sxs-lookup"><span data-stu-id="decf7-157">'s runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="decf7-158">K dispozici je datový soubor pro správné informace o časovém pásmu.</span><span class="sxs-lookup"><span data-stu-id="decf7-158">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="decf7-159">Pokud aplikace tuto funkci nevyžaduje, zvažte její zakázání nastavením `BlazorEnableTimeZoneSupport` vlastnosti MSBuild v souboru projektu aplikace na `false` :</span><span class="sxs-lookup"><span data-stu-id="decf7-159">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="decf7-160">K zajištění správného fungování rozhraní API, jako je například práce, jsou zahrnuty informace o kolaci <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="decf7-160">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="decf7-161">Pokud jste si jisti, že aplikace nevyžaduje data kolace, zvažte její zakázání nastavením `BlazorWebAssemblyPreserveCollationData` vlastnosti MSBuild v souboru projektu aplikace na `false` :</span><span class="sxs-lookup"><span data-stu-id="decf7-161">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```