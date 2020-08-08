---
title: Odkaz na prohlížeč v ASP.NET Core
author: ncarandini
description: Vysvětluje, jak je odkaz v prohlížeči funkcí sady Visual Studio, která propojuje vývojové prostředí s jedním nebo více webovými prohlížeči.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 737abc8b9abea45654a86a4dc20584948ece9fc5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013174"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="4d213-103">Odkaz na prohlížeč v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d213-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="4d213-104">[Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="4d213-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="4d213-105">Odkaz na prohlížeč je funkce sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4d213-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="4d213-106">Vytvoří komunikační kanál mezi vývojovým prostředím a jedním nebo více webovými prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="4d213-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="4d213-107">Odkaz na prohlížeč můžete použít k aktualizaci webové aplikace v několika prohlížečích najednou, což je užitečné pro testování v různých prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="4d213-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="4d213-108">Nastavení odkazu na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="4d213-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4d213-109">Přidejte do projektu balíček [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="4d213-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="4d213-110">Pro ASP.NET Core Razor stránky nebo projekty MVC také povolte kompilaci za běhu Razor souborů (*. cshtml*), jak je popsáno v <xref:mvc/views/view-compilation> .</span><span class="sxs-lookup"><span data-stu-id="4d213-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="4d213-111">Razorzměny syntaxe se aplikují jenom v případě, že je povolená kompilace za běhu.</span><span class="sxs-lookup"><span data-stu-id="4d213-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="4d213-112">Při převodu projektu ASP.NET Core 2,0 na ASP.NET Core 2,1 a přechodu na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app)nainstalujte balíček [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pro funkce připojení prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4d213-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="4d213-113">Šablony projektu ASP.NET Core 2,1 `Microsoft.AspNetCore.App` ve výchozím nastavení používají Metapackage.</span><span class="sxs-lookup"><span data-stu-id="4d213-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="4d213-114">Šablony projektů **webové aplikace**ASP.NET Core 2,0 **, prázdné**a **webové rozhraní API** používají soubor [Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage), který obsahuje odkaz na balíček pro [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="4d213-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="4d213-115">Proto použití `Microsoft.AspNetCore.All` Metapackage nevyžaduje žádné další kroky k tomu, aby byl odkaz na prohlížeč dostupný pro použití.</span><span class="sxs-lookup"><span data-stu-id="4d213-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="4d213-116">Šablona projektu **webové aplikace** ASP.NET Core 1. x obsahuje odkaz na balíček pro balíček [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="4d213-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="4d213-117">Jiné typy projektů vyžadují, abyste přidali odkaz na balíček `Microsoft.VisualStudio.Web.BrowserLink` .</span><span class="sxs-lookup"><span data-stu-id="4d213-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="4d213-118">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="4d213-118">Configuration</span></span>

<span data-ttu-id="4d213-119">Zavolejte `UseBrowserLink` v `Startup.Configure` metodě:</span><span class="sxs-lookup"><span data-stu-id="4d213-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="4d213-120">`UseBrowserLink`Volání je obvykle umístěno uvnitř `if` bloku, který povoluje pouze odkaz na prohlížeč ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4d213-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="4d213-121">Například:</span><span class="sxs-lookup"><span data-stu-id="4d213-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="4d213-122">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4d213-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="4d213-123">Jak používat Browser Link</span><span class="sxs-lookup"><span data-stu-id="4d213-123">How to use Browser Link</span></span>

<span data-ttu-id="4d213-124">Pokud máte otevřený projekt ASP.NET Core, Visual Studio zobrazí ovládací prvek panelu nástrojů pro odkaz na prohlížeč vedle ovládacího prvku panel nástrojů **cíl ladění** :</span><span class="sxs-lookup"><span data-stu-id="4d213-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Rozevírací nabídka připojit k prohlížeči](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="4d213-126">Z ovládacího prvku panel nástrojů odkaz na prohlížeč můžete:</span><span class="sxs-lookup"><span data-stu-id="4d213-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="4d213-127">Aktualizujte webovou aplikaci v několika prohlížečích najednou.</span><span class="sxs-lookup"><span data-stu-id="4d213-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="4d213-128">Otevřete **řídicí panel odkaz na prohlížeč**.</span><span class="sxs-lookup"><span data-stu-id="4d213-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="4d213-129">Povolí nebo zakáže **odkaz na prohlížeč**.</span><span class="sxs-lookup"><span data-stu-id="4d213-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="4d213-130">Poznámka: ve výchozím nastavení v aplikaci Visual Studio je odkaz na prohlížeč zakázán.</span><span class="sxs-lookup"><span data-stu-id="4d213-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="4d213-131">Povolí nebo zakáže [automatickou synchronizaci šablon stylů CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="4d213-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="4d213-132">Aktualizace webové aplikace v několika prohlížečích najednou</span><span class="sxs-lookup"><span data-stu-id="4d213-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="4d213-133">Chcete-li zvolit jeden webový prohlížeč, který má být spuštěn při spuštění projektu, použijte rozevírací nabídku v ovládacím prvku panel nástrojů **cíl ladění** :</span><span class="sxs-lookup"><span data-stu-id="4d213-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Rozevírací nabídka F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="4d213-135">Chcete-li otevřít více prohlížečů najednou, vyberte možnost **Procházet se...** ze stejného rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="4d213-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="4d213-136">Podržte stisknutou klávesu <kbd>CTRL</kbd> a vyberte požadované prohlížeče a potom klikněte na **Procházet**:</span><span class="sxs-lookup"><span data-stu-id="4d213-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Otevřít spoustu prohlížečů najednou](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="4d213-138">Následující snímek obrazovky ukazuje Visual Studio s otevřeným zobrazením index a dvěma otevřenými prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="4d213-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Příklad synchronizace se dvěma prohlížeči](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="4d213-140">Najeďte myší na ovládací prvek panelu nástrojů propojení prohlížeče, aby se zobrazily prohlížeče, které jsou připojené k projektu:</span><span class="sxs-lookup"><span data-stu-id="4d213-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Hrot přechodu myši](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="4d213-142">Změňte zobrazení indexu a po kliknutí na tlačítko pro obnovení propojení prohlížeče se aktualizují všechny připojené prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="4d213-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![prohlížeče – synchronizace změn](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="4d213-144">Odkaz na prohlížeč funguje taky s prohlížeči, které spouštíte z vnějšku sady Visual Studio, a přejdete na adresu URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="4d213-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="4d213-145">Řídicí panel propojení prohlížeče</span><span class="sxs-lookup"><span data-stu-id="4d213-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="4d213-146">Otevřete okno **řídicí panel pro propojení prohlížeče** z rozevírací nabídky odkaz na prohlížeč ke správě připojení s otevřenými prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="4d213-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![otevřít – browserslink – řídicí panel](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="4d213-148">Pokud není připojený žádný prohlížeč, můžete spustit relaci bez ladění tak, že vyberete odkaz **Zobrazit v prohlížeči** :</span><span class="sxs-lookup"><span data-stu-id="4d213-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink – řídicí panel – No – připojení](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="4d213-150">V opačném případě jsou připojené prohlížeče zobrazeny s cestou ke stránce, kterou zobrazují jednotlivé prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="4d213-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-řídicí panel – dvě připojení](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="4d213-152">Můžete také kliknout na název jednotlivého prohlížeče a aktualizovat pouze tento prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="4d213-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="4d213-153">Povolit nebo zakázat odkaz na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="4d213-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="4d213-154">Když znovu povolíte odkaz na prohlížeč po jeho zakázání, je nutné aktualizovat prohlížeče, aby se znovu připojily.</span><span class="sxs-lookup"><span data-stu-id="4d213-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="4d213-155">Povolí nebo zakáže automatickou synchronizaci šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="4d213-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="4d213-156">Pokud je povolena automatická synchronizace šablon stylů CSS, připojené prohlížeče se automaticky aktualizují, když provedete změny v souborech CSS.</span><span class="sxs-lookup"><span data-stu-id="4d213-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="4d213-157">Jak to funguje</span><span class="sxs-lookup"><span data-stu-id="4d213-157">How it works</span></span>

<span data-ttu-id="4d213-158">Odkaz na prohlížeč používá [SignalR](xref:signalr/introduction) k vytvoření komunikačního kanálu mezi Visual Studio a prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="4d213-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="4d213-159">Pokud je povolen odkaz na prohlížeč, Visual Studio funguje jako SignalR Server, ke kterému se může připojit více klientů (prohlížečů).</span><span class="sxs-lookup"><span data-stu-id="4d213-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="4d213-160">Odkaz na prohlížeč také zaregistruje součást middleware v kanálu žádosti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d213-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="4d213-161">Tato součást vloží speciální `<script>` odkazy na všechny žádosti stránky ze serveru.</span><span class="sxs-lookup"><span data-stu-id="4d213-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="4d213-162">Odkazy na skript můžete zobrazit tak, že v prohlížeči vyberete **Zobrazit zdroj** a posunete se na konec `<body>` obsahu značky:</span><span class="sxs-lookup"><span data-stu-id="4d213-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="4d213-163">Vaše zdrojové soubory se nemění.</span><span class="sxs-lookup"><span data-stu-id="4d213-163">Your source files aren't modified.</span></span> <span data-ttu-id="4d213-164">Komponenta middlewaru vloží odkaz na skript dynamicky.</span><span class="sxs-lookup"><span data-stu-id="4d213-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="4d213-165">Vzhledem k tomu, že kód na straně prohlížeče je JavaScript, funguje na všech prohlížečích, které SignalR podporují bez nutnosti použití modulu plug-in prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4d213-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
