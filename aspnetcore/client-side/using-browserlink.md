---
title: Odkaz na prohlížeč v ASP.NET Core
author: ncarandini
description: Vysvětluje, jak je odkaz v prohlížeči funkcí sady Visual Studio, která propojuje vývojové prostředí s jedním nebo více webovými prohlížeči.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: b21b698d49e72b559cd9cd3753c48a38c99db24d
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962787"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="28e07-103">Odkaz na prohlížeč v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28e07-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="28e07-104">[Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="28e07-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="28e07-105">Odkaz na prohlížeč je funkce sady Visual Studio, která vytváří komunikační kanál mezi vývojovým prostředím a jedním nebo více webovými prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="28e07-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="28e07-106">Odkaz na prohlížeč můžete použít k aktualizaci webové aplikace v několika prohlížečích najednou, což je užitečné pro testování v různých prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="28e07-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="28e07-107">Nastavení odkazu na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="28e07-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="28e07-108">Při převodu projektu ASP.NET Core 2,0 na ASP.NET Core 2,1 a přechodu na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app)nainstalujte balíček [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pro funkce BrowserLink.</span><span class="sxs-lookup"><span data-stu-id="28e07-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="28e07-109">Šablony projektu ASP.NET Core 2,1 ve výchozím nastavení používají `Microsoft.AspNetCore.App` Metapackage.</span><span class="sxs-lookup"><span data-stu-id="28e07-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="28e07-110">Šablony projektů **webové aplikace**ASP.NET Core 2,0 **, prázdné**a **webové rozhraní API** používají soubor [Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage), který obsahuje odkaz na balíček pro [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="28e07-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="28e07-111">Proto použití `Microsoft.AspNetCore.All` Metapackage nevyžaduje žádné další kroky k tomu, aby bylo možné použít odkaz na prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="28e07-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="28e07-112">Šablona projektu **webové aplikace** ASP.NET Core 1. x obsahuje odkaz na balíček pro balíček [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="28e07-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="28e07-113">**Prázdné** projekty šablon nebo šablony **webového rozhraní API** vyžadují, abyste přidali odkaz na balíček, který `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="28e07-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="28e07-114">Vzhledem k tomu, že se jedná o funkci sady Visual Studio, nejjednodušší způsob, jak přidat balíček do **prázdného** nebo do projektu šablony **webového rozhraní API** , je otevřít **konzolu Správce balíčků** (**Zobrazit** > jiné **konzole správce balíčků**> **Windows** ) a spustit následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="28e07-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="28e07-115">Případně můžete použít **Správce balíčků NuGet**.</span><span class="sxs-lookup"><span data-stu-id="28e07-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="28e07-116">Klikněte pravým tlačítkem myši na název projektu v **Průzkumník řešení** a vyberte možnost **Spravovat balíčky NuGet**:</span><span class="sxs-lookup"><span data-stu-id="28e07-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![Otevřít Správce balíčků NuGet](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="28e07-118">Najděte a nainstalujte balíček:</span><span class="sxs-lookup"><span data-stu-id="28e07-118">Find and install the package:</span></span>

![Přidat balíček pomocí Správce balíčků NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="28e07-120">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="28e07-120">Configuration</span></span>

<span data-ttu-id="28e07-121">V metodě `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="28e07-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="28e07-122">Kód je obvykle uvnitř `if` blok, který umožňuje pouze odkaz na prohlížeč ve vývojovém prostředí, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="28e07-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="28e07-123">Další informace najdete v tématu [použití více prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="28e07-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="28e07-124">Jak používat Browser Link</span><span class="sxs-lookup"><span data-stu-id="28e07-124">How to use Browser Link</span></span>

<span data-ttu-id="28e07-125">Pokud máte otevřený projekt ASP.NET Core, Visual Studio zobrazí ovládací prvek panelu nástrojů pro odkaz na prohlížeč vedle ovládacího prvku panel nástrojů **cíl ladění** :</span><span class="sxs-lookup"><span data-stu-id="28e07-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Rozevírací nabídka připojit k prohlížeči](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="28e07-127">Z ovládacího prvku panel nástrojů odkaz na prohlížeč můžete:</span><span class="sxs-lookup"><span data-stu-id="28e07-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="28e07-128">Aktualizujte webovou aplikaci v několika prohlížečích najednou.</span><span class="sxs-lookup"><span data-stu-id="28e07-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="28e07-129">Otevřete **řídicí panel odkaz na prohlížeč**.</span><span class="sxs-lookup"><span data-stu-id="28e07-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="28e07-130">Povolí nebo zakáže **odkaz na prohlížeč**.</span><span class="sxs-lookup"><span data-stu-id="28e07-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="28e07-131">Poznámka: v aplikaci Visual Studio 2017 (15,3) je ve výchozím nastavení zakázáno připojení prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="28e07-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="28e07-132">Povolí nebo zakáže [automatickou synchronizaci šablon stylů CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="28e07-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="28e07-133">Některé moduly plug-in sady Visual Studio, zejména *Web Extension pack 2015* a *web Extension Pack 2017*, nabízejí rozšířenou funkci pro odkaz na prohlížeč, ale některé z dalších funkcí nefungují s ASP.NET Core projekty.</span><span class="sxs-lookup"><span data-stu-id="28e07-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="28e07-134">Aktualizace webové aplikace v několika prohlížečích najednou</span><span class="sxs-lookup"><span data-stu-id="28e07-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="28e07-135">Chcete-li zvolit jeden webový prohlížeč, který má být spuštěn při spuštění projektu, použijte rozevírací nabídku v ovládacím prvku panel nástrojů **cíl ladění** :</span><span class="sxs-lookup"><span data-stu-id="28e07-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Rozevírací nabídka F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="28e07-137">Chcete-li otevřít více prohlížečů najednou, vyberte možnost **Procházet se...** ze stejného rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="28e07-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="28e07-138">Podržte stisknutou klávesu CTRL a vyberte požadované prohlížeče a potom klikněte na **Procházet**:</span><span class="sxs-lookup"><span data-stu-id="28e07-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![Otevřít spoustu prohlížečů najednou](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="28e07-140">Tady je snímek obrazovky se sadou Visual Studio a otevřeným zobrazením indexu a dvěma otevřenými prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="28e07-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![Příklad synchronizace se dvěma prohlížeči](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="28e07-142">Najeďte myší na ovládací prvek panelu nástrojů propojení prohlížeče, aby se zobrazily prohlížeče, které jsou připojené k projektu:</span><span class="sxs-lookup"><span data-stu-id="28e07-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Hrot přechodu myši](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="28e07-144">Změňte zobrazení indexu a po kliknutí na tlačítko pro obnovení propojení prohlížeče se aktualizují všechny připojené prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="28e07-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![prohlížeče – synchronizace změn](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="28e07-146">Odkaz na prohlížeč funguje taky s prohlížeči, které spouštíte z vnějšku sady Visual Studio, a přejdete na adresu URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="28e07-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="28e07-147">Řídicí panel propojení prohlížeče</span><span class="sxs-lookup"><span data-stu-id="28e07-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="28e07-148">Pokud chcete spravovat připojení s otevřenými prohlížeči, otevřete řídicí panel odkaz v prohlížeči z rozevírací nabídky odkaz na prohlížeč:</span><span class="sxs-lookup"><span data-stu-id="28e07-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![otevřít – browserslink – řídicí panel](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="28e07-150">Pokud není připojený žádný prohlížeč, můžete spustit relaci bez ladění tak, že vyberete odkaz *Zobrazit v prohlížeči* :</span><span class="sxs-lookup"><span data-stu-id="28e07-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink – řídicí panel – No – připojení](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="28e07-152">V opačném případě jsou připojené prohlížeče zobrazeny s cestou ke stránce, kterou zobrazují jednotlivé prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="28e07-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-řídicí panel – dvě připojení](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="28e07-154">Pokud chcete, můžete kliknout na uvedený název prohlížeče a aktualizovat ho v jednom prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="28e07-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="28e07-155">Povolit nebo zakázat odkaz na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="28e07-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="28e07-156">Když znovu povolíte odkaz na prohlížeč po jeho zakázání, je nutné aktualizovat prohlížeče, aby se znovu připojily.</span><span class="sxs-lookup"><span data-stu-id="28e07-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="28e07-157">Povolí nebo zakáže automatickou synchronizaci šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="28e07-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="28e07-158">Pokud je povolena automatická synchronizace šablon stylů CSS, připojené prohlížeče se automaticky aktualizují, když provedete změny v souborech CSS.</span><span class="sxs-lookup"><span data-stu-id="28e07-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="28e07-159">Jak to funguje</span><span class="sxs-lookup"><span data-stu-id="28e07-159">How it works</span></span>

<span data-ttu-id="28e07-160">Odkaz na prohlížeč používá SignalR k vytvoření komunikačního kanálu mezi Visual Studio a prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="28e07-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="28e07-161">Pokud je povolen odkaz na prohlížeč, Visual Studio funguje jako server SignalR, ke kterému se může připojit více klientů (prohlížečů).</span><span class="sxs-lookup"><span data-stu-id="28e07-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="28e07-162">Odkaz na prohlížeč také zaregistruje součást middleware v kanálu žádosti ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28e07-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="28e07-163">Tato součást vloží speciální `<script>` odkazy na všechny žádosti stránky ze serveru.</span><span class="sxs-lookup"><span data-stu-id="28e07-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="28e07-164">Odkazy na skript můžete zobrazit tak, že v prohlížeči vyberete **Zobrazit zdroj** a posunete se na konec obsahu značky `<body>`:</span><span class="sxs-lookup"><span data-stu-id="28e07-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="28e07-165">Vaše zdrojové soubory se nemění.</span><span class="sxs-lookup"><span data-stu-id="28e07-165">Your source files aren't modified.</span></span> <span data-ttu-id="28e07-166">Komponenta middlewaru vloží odkaz na skript dynamicky.</span><span class="sxs-lookup"><span data-stu-id="28e07-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="28e07-167">Vzhledem k tomu, že kód na straně prohlížeče je celý JavaScript, funguje na všech prohlížečích, které SignalR podporuje bez nutnosti modulu plug-in prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="28e07-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
