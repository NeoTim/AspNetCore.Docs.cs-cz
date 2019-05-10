---
title: Browser Link v ASP.NET Core
author: ncarandini
description: Vysvětluje, jak Browser Link je funkce sady Visual Studio, který odkazuje vývojového prostředí pomocí jednoho nebo více webových prohlížečů.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
uid: client-side/using-browserlink
ms.openlocfilehash: 452ba5149563c186750466f471c7b950f0017614
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900849"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="b17fe-103">Browser Link v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b17fe-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="b17fe-104">Podle [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b17fe-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b17fe-105">Browser Link je funkce v sadě Visual Studio, který vytvoří komunikační kanál mezi vývojové prostředí a jeden nebo více webových prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="b17fe-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="b17fe-106">Můžete použít odkaz prohlížeče pro aktualizaci webové aplikace v několika prohlížečích najednou, což je užitečné pro testování prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="b17fe-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="b17fe-107">Nastavit propojení prohlížeče</span><span class="sxs-lookup"><span data-stu-id="b17fe-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b17fe-108">Při převodu projektu aplikace ASP.NET Core 2.0 k ASP.NET Core 2.1 a omezování [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), nainstalujte [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) balíček pro BrowserLink funkce.</span><span class="sxs-lookup"><span data-stu-id="b17fe-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="b17fe-109">Použijte šablony projektů ASP.NET Core 2.1 `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="b17fe-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b17fe-110">ASP.NET Core 2.0 **webovou aplikaci**, **prázdný**, a **webového rozhraní API** projektu pomocí šablony [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) , který obsahuje odkaz na balíček pro [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="b17fe-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="b17fe-111">Proto pomocí `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all vyžaduje, abyste měli k dispozici pro použití Browser Link žádná další akce.</span><span class="sxs-lookup"><span data-stu-id="b17fe-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="b17fe-112">ASP.NET Core 1.x **webovou aplikaci** šablona projekt obsahuje odkaz na balíček pro [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="b17fe-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="b17fe-113">**Prázdný** nebo **webového rozhraní API** šablony projektů vyžadují, abyste přidat odkaz na balíček pro `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="b17fe-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="b17fe-114">Protože tato funkce sady Visual Studio, je nejjednodušší způsob přidání balíčku do **prázdný** nebo **webového rozhraní API** šablony projektu, je otevřít **Konzola správce balíčků** (**Zobrazení** > **ostatní Windows** > **Konzola správce balíčků**) a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b17fe-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="b17fe-115">Alternativně můžete použít **Správce balíčků NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b17fe-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="b17fe-116">Klikněte pravým tlačítkem na název projektu v **Průzkumníka řešení** a zvolte **spravovat balíčky NuGet**:</span><span class="sxs-lookup"><span data-stu-id="b17fe-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![Správce balíčků NuGet otevřít](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="b17fe-118">Najít a nainstalovat balíček:</span><span class="sxs-lookup"><span data-stu-id="b17fe-118">Find and install the package:</span></span>

![Přidání balíčku pomocí Správce balíčků NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="b17fe-120">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="b17fe-120">Configuration</span></span>

<span data-ttu-id="b17fe-121">V `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="b17fe-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="b17fe-122">Obvykle je kód uvnitř `if` blok, který povoluje pouze odkazy v prohlížeči ve vývojovém prostředí, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="b17fe-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="b17fe-123">Další informace naleznete v tématu [Používání více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b17fe-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="b17fe-124">Postup použití funkce Browser Link</span><span class="sxs-lookup"><span data-stu-id="b17fe-124">How to use Browser Link</span></span>

<span data-ttu-id="b17fe-125">Pokud máte otevřít projekt ASP.NET Core, Visual Studio zobrazí vedle ovládacím prvkem panel nástrojů odkazy v prohlížeči **cíl ladění** ovládací prvek panelu nástrojů:</span><span class="sxs-lookup"><span data-stu-id="b17fe-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Browser Link rozevírací nabídky](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="b17fe-127">Browser Link ovládací prvek panelu nástrojů můžete:</span><span class="sxs-lookup"><span data-stu-id="b17fe-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="b17fe-128">Aktualizace webové aplikace v několika prohlížečích najednou.</span><span class="sxs-lookup"><span data-stu-id="b17fe-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="b17fe-129">Otevřít **řídicím odkazů prohlížeče**.</span><span class="sxs-lookup"><span data-stu-id="b17fe-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="b17fe-130">Povolení nebo zakázání **Browser Link**.</span><span class="sxs-lookup"><span data-stu-id="b17fe-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="b17fe-131">Poznámka: Browser Link je zakázané ve výchozím nastavení v sadě Visual Studio 2017 (15.3).</span><span class="sxs-lookup"><span data-stu-id="b17fe-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="b17fe-132">Povolení nebo zakázání [Automatická synchronizace šablon stylů CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="b17fe-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="b17fe-133">Některé sady Visual Studio moduly plug-in, zejména *webové rozšíření balíčku 2015* a *webové rozšíření balíčku 2017*, nabízí rozšířené funkce pro Browser Link, ale některé další funkce nefungují s ASP. Projekty .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b17fe-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="b17fe-134">Aktualizace webové aplikace v několika prohlížečích najednou</span><span class="sxs-lookup"><span data-stu-id="b17fe-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="b17fe-135">Chcete-li zvolit jeden webový prohlížeč na spuštění při spuštění projektu, použijte rozevírací nabídky v **cíl ladění** ovládací prvek panelu nástrojů:</span><span class="sxs-lookup"><span data-stu-id="b17fe-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Rozevírací nabídka F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="b17fe-137">Chcete-li otevřít více prohlížečů najednou, zvolte **nastavit prohlížeče...**  stejné rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="b17fe-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="b17fe-138">Podržte stisknutou klávesu CTRL k výběru prohlížeče, které chcete a potom klikněte na **Procházet**:</span><span class="sxs-lookup"><span data-stu-id="b17fe-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![Stačí otevřít mnoha prohlížečů](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="b17fe-140">Tady je snímek obrazovky sady Visual Studio s zobrazení indexu otevřít a otevřít prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="b17fe-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![Synchronizovat s ukázkou prohlížečů](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="b17fe-142">Najeďte myší ovládacím prvkem panel nástrojů odkazy v prohlížeči zobrazit prohlížečů, které jsou připojené k projektu:</span><span class="sxs-lookup"><span data-stu-id="b17fe-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Popis tlačítka při najetí myší](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="b17fe-144">Změnit zobrazení indexu a všech propojených prohlížečů se aktualizují po kliknutí na tlačítko Aktualizovat odkazy v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="b17fe-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="b17fe-146">Browser Link funguje taky v prohlížečích, které můžete spustit z mimo sadu Visual Studio a přejděte na adresu URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="b17fe-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="b17fe-147">Řídicím panelu odkazů prohlížeče</span><span class="sxs-lookup"><span data-stu-id="b17fe-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="b17fe-148">Browser Link rozevírací nabídka spravovat připojení otevřené prohlížeče otevřete řídicím panelu odkazů prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="b17fe-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="b17fe-150">Pokud je připojený žádný prohlížeč, můžete začít relaci – ladění tak, že vyberete *zobrazit v prohlížeči* odkaz:</span><span class="sxs-lookup"><span data-stu-id="b17fe-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="b17fe-152">V opačném případě propojených prohlížečů jsou uvedeny cestu ke stránce, která zobrazuje každou prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="b17fe-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="b17fe-154">Pokud chcete můžete klikněte na název uvedené prohlížeče k aktualizaci tohoto jediného prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b17fe-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="b17fe-155">Povolení nebo zakázání Browser Link</span><span class="sxs-lookup"><span data-stu-id="b17fe-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="b17fe-156">Po jeho zakázání proto potřeba znovu povolit Browser Link, je nutné aktualizovat prohlížeče je znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="b17fe-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="b17fe-157">Povolit nebo zakázat automatickou synchronizaci šablon stylů CSS</span><span class="sxs-lookup"><span data-stu-id="b17fe-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="b17fe-158">Pokud je povolena automatická synchronizace šablon stylů CSS, propojených prohlížečů se automaticky aktualizují při provádět změny na soubory šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="b17fe-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="b17fe-159">Jak to funguje</span><span class="sxs-lookup"><span data-stu-id="b17fe-159">How it works</span></span>

<span data-ttu-id="b17fe-160">Browser Link používá SignalR k vytvoření komunikačního kanálu mezi Visual Studio a prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="b17fe-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="b17fe-161">Když je povolené Browser Link, Visual Studio funguje jako více klientů (prohlížečů) můžete připojit k serveru funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="b17fe-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="b17fe-162">Browser Link také zaregistruje komponentu middleware v kanálu požadavku ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b17fe-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="b17fe-163">Tato součást Vloží speciální `<script>` odkazy do každého požadavku stránky ze serveru.</span><span class="sxs-lookup"><span data-stu-id="b17fe-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="b17fe-164">Uvidíte odkazy na skript tak, že vyberete **zobrazit zdroj** v prohlížeči a posouvání na konec objektu `<body>` označení obsahu:</span><span class="sxs-lookup"><span data-stu-id="b17fe-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="b17fe-165">Zdrojové soubory se nemění.</span><span class="sxs-lookup"><span data-stu-id="b17fe-165">Your source files aren't modified.</span></span> <span data-ttu-id="b17fe-166">Komponenta middlewaru dynamicky vkládá skriptových odkazů.</span><span class="sxs-lookup"><span data-stu-id="b17fe-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="b17fe-167">Protože kódu prohlížeče na všech JavaScript, funguje ve všech prohlížečích, které podporuje funkce SignalR bez nutnosti modul plug-in prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b17fe-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
