---
title: Odkaz na prohlížeč v ASP.NET jádru
author: ncarandini
description: Vysvětluje, jak odkaz prohlížeče je funkce sady Visual Studio, která propojuje vývojové prostředí s jedním nebo více webových prohlížečů.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658849"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="10451-103">Odkaz na prohlížeč v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="10451-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="10451-104">Od [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), a [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="10451-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="10451-105">Odkaz prohlížeče je funkce sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="10451-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="10451-106">Vytvoří komunikační kanál mezi vývojovým prostředím a jedním nebo více webovými prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="10451-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="10451-107">Pomocí odkazu prohlížeče můžete aktualizovat webovou aplikaci v několika prohlížečích najednou, což je užitečné pro testování mezi prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="10451-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="10451-108">Nastavení odkazu na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="10451-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10451-109">Přidejte do projektu balíček [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)</span><span class="sxs-lookup"><span data-stu-id="10451-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="10451-110">Pro ASP.NET Core Razor Pages nebo MVC projekty, také povolit runtime <xref:mvc/views/view-compilation>kompilaci Razor (*.cshtml*) soubory, jak je popsáno v .</span><span class="sxs-lookup"><span data-stu-id="10451-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="10451-111">Změny syntaxe holicího strojku se použijí pouze v případě, že byla povolena kompilace modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="10451-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="10451-112">Při převodu projektu ASP.NET Core 2.0 na ASP.NET jádrem 2.1 a při přechodu na [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app)nainstalujte balíček [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pro funkci Odkaz prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="10451-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="10451-113">Šablony projektu ASP.NET Core 2.1 `Microsoft.AspNetCore.App` používají metabalíček ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="10451-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="10451-114">Šablony projektu ASP.NET Core 2.0 **,** **Empty**a **Web API** používají [metabalíček Microsoft.AspNetCore.All](xref:fundamentals/metapackage), který obsahuje odkaz na balíček pro [microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="10451-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="10451-115">Proto použití `Microsoft.AspNetCore.All` metabalíčku nevyžaduje žádné další akce, aby odkaz prohlížeče k dispozici pro použití.</span><span class="sxs-lookup"><span data-stu-id="10451-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="10451-116">Šablona projektu ASP.NET Core 1.x **Web Application** obsahuje odkaz na balíček pro balíček [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)</span><span class="sxs-lookup"><span data-stu-id="10451-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="10451-117">Jiné typy projektů vyžadují přidání odkazu `Microsoft.VisualStudio.Web.BrowserLink`na balíček do aplikace .</span><span class="sxs-lookup"><span data-stu-id="10451-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="10451-118">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="10451-118">Configuration</span></span>

<span data-ttu-id="10451-119">Volání `UseBrowserLink` `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="10451-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="10451-120">Volání `UseBrowserLink` je obvykle umístěn `if` uvnitř bloku, který umožňuje pouze odkaz prohlížeče ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10451-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="10451-121">Příklad:</span><span class="sxs-lookup"><span data-stu-id="10451-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="10451-122">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="10451-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="10451-123">Jak používat odkaz na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="10451-123">How to use Browser Link</span></span>

<span data-ttu-id="10451-124">Pokud máte otevřený ASP.NET základní projekt, Visual Studio zobrazí ovládací prvek panelu nástrojů Odkaz prohlížeče vedle ovládacího prvku panelu nástrojů **Ladění cíle:**</span><span class="sxs-lookup"><span data-stu-id="10451-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Rozevírací nabídka Odkaz prohlížeče](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="10451-126">V ovládacím panelu nástrojů Odkaz prohlížeče můžete:</span><span class="sxs-lookup"><span data-stu-id="10451-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="10451-127">Aktualizujte webovou aplikaci v několika prohlížečích najednou.</span><span class="sxs-lookup"><span data-stu-id="10451-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="10451-128">Otevřete **řídicí panel odkazu prohlížeče**.</span><span class="sxs-lookup"><span data-stu-id="10451-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="10451-129">Povolte nebo zakažte **odkaz na prohlížeč**.</span><span class="sxs-lookup"><span data-stu-id="10451-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="10451-130">Poznámka: Odkaz prohlížeče je ve výchozím nastavení v sadě Visual Studio zakázán.</span><span class="sxs-lookup"><span data-stu-id="10451-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="10451-131">Povolte nebo zakažte [automatické synchronizaci CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="10451-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="10451-132">Aktualizace webové aplikace v několika prohlížečích najednou</span><span class="sxs-lookup"><span data-stu-id="10451-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="10451-133">Chcete-li vybrat jeden webový prohlížeč, který chcete spustit při spuštění projektu, použijte rozevírací nabídku v ovládacím panelu nástrojů **Ladění cíle:**</span><span class="sxs-lookup"><span data-stu-id="10451-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Rozevírací nabídka F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="10451-135">Chcete-li otevřít více prohlížečů najednou, zvolte **Procházet pomocí...** ze stejného rozevíracího balíčku.</span><span class="sxs-lookup"><span data-stu-id="10451-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="10451-136">Podržením klávesy <kbd>Ctrl</kbd> vyberte požadované prohlížeče a klikněte na **Procházet**:</span><span class="sxs-lookup"><span data-stu-id="10451-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Otevření mnoha prohlížečů najednou](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="10451-138">Následující snímek obrazovky ukazuje Visual Studio s otevřeným zobrazením Rejstříku a dvěma otevřenými prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="10451-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Příklad synchronizace se dvěma prohlížeči](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="10451-140">Chcete-li zobrazit prohlížeče připojené k projektu, najeďte na panel nástrojů Odkaz prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="10451-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Špička pro vznášení](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="10451-142">Změňte zobrazení rejstříku a všechny připojené prohlížeče se aktualizují, když kliknete na tlačítko Aktualizovat odkaz prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="10451-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![prohlížeče-synchronizace se změnami](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="10451-144">Odkaz na prohlížeč funguje také s prohlížeči, které spustíte z externího visual studia a přejdete na adresu URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="10451-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="10451-145">Řídicí panel odkazu prohlížeče</span><span class="sxs-lookup"><span data-stu-id="10451-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="10451-146">Chcete-li spravovat připojení s otevřenými prohlížeči, otevřete okno **Řídicí panel odkazu prohlížeče** v rozevírací nabídce Odkaz prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="10451-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="10451-148">Pokud není připojen žádný prohlížeč, můžete zahájit relaci bez ladění výběrem odkazu **Zobrazení v prohlížeči:**</span><span class="sxs-lookup"><span data-stu-id="10451-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="10451-150">V opačném případě se připojené prohlížeče zobrazí s cestou ke stránce, kterou každý prohlížeč zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="10451-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![odkaz na prohlížeč-řídicí panel-dvě připojení](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="10451-152">Můžete také kliknout na název jednotlivého prohlížeče a aktualizovat pouze tento prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="10451-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="10451-153">Povolení nebo zakázání odkazu prohlížeče</span><span class="sxs-lookup"><span data-stu-id="10451-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="10451-154">Po opětovném povolení odkazu prohlížeče po jeho zakázání je nutné aktualizovat prohlížeče, abyste je znovu připojili.</span><span class="sxs-lookup"><span data-stu-id="10451-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="10451-155">Povolení nebo zakázání automatické synchronizace CSS</span><span class="sxs-lookup"><span data-stu-id="10451-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="10451-156">Je-li povolena automatická synchronizace CSS, připojené prohlížeče se automaticky aktualizují, když provedete jakoukoli změnu souborů CSS.</span><span class="sxs-lookup"><span data-stu-id="10451-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="10451-157">Jak to funguje</span><span class="sxs-lookup"><span data-stu-id="10451-157">How it works</span></span>

<span data-ttu-id="10451-158">Odkaz prohlížeče [SignalR](xref:signalr/introduction) používá k vytvoření komunikačního kanálu mezi Visual Studio a prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="10451-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="10451-159">Je-li povoleno propojení prohlížeče, SignalR visual studio funguje jako server, který více klientů (prohlížečů) se může připojit k.</span><span class="sxs-lookup"><span data-stu-id="10451-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="10451-160">Browser Link také registruje middleware komponenty v kanálu ASP.NET požadavků Core.</span><span class="sxs-lookup"><span data-stu-id="10451-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="10451-161">Tato součást vloží `<script>` speciální odkazy do každého požadavku na stránku ze serveru.</span><span class="sxs-lookup"><span data-stu-id="10451-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="10451-162">Odkazy na skripty můžete zobrazit tak, že v prohlížeči vyberete `<body>` zdroj **zobrazení** a posunete se na konec obsahu značky:</span><span class="sxs-lookup"><span data-stu-id="10451-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="10451-163">Zdrojové soubory se nezmění.</span><span class="sxs-lookup"><span data-stu-id="10451-163">Your source files aren't modified.</span></span> <span data-ttu-id="10451-164">Middleware komponenta vloží odkazy skriptu dynamicky.</span><span class="sxs-lookup"><span data-stu-id="10451-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="10451-165">Vzhledem k tomu, že kód na straně prohlížeče SignalR je veškerý JavaScript, funguje ve všech prohlížečích, které podporují bez nutnosti modulu plug-in prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="10451-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
