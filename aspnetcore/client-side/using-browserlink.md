---
title: Odkaz na prohlížeč v ASP.NET Core
author: ncarandini
description: Vysvětluje, jak je odkaz v prohlížeči funkcí sady Visual Studio, která propojuje vývojové prostředí s jedním nebo více webovými prohlížeči.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 95ddf379d7cab336356cbfd3853311cb0911552a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401711"
---
# <a name="browser-link-in-aspnet-core"></a>Odkaz na prohlížeč v ASP.NET Core

[Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)a [Dykstra](https://github.com/tdykstra)

Odkaz na prohlížeč je funkce sady Visual Studio. Vytvoří komunikační kanál mezi vývojovým prostředím a jedním nebo více webovými prohlížeči. Odkaz na prohlížeč můžete použít k aktualizaci webové aplikace v několika prohlížečích najednou, což je užitečné pro testování v různých prohlížečích.

## <a name="browser-link-setup"></a>Nastavení odkazu na prohlížeč

::: moniker range=">= aspnetcore-3.0"

Přidejte do projektu balíček [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) . Pro ASP.NET Core Razor stránky nebo projekty MVC také povolte kompilaci za běhu Razor souborů (*. cshtml*), jak je popsáno v <xref:mvc/views/view-compilation> . Razorzměny syntaxe se aplikují jenom v případě, že je povolená kompilace za běhu.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Při převodu projektu ASP.NET Core 2,0 na ASP.NET Core 2,1 a přechodu na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app)nainstalujte balíček [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pro funkce připojení prohlížeče. Šablony projektu ASP.NET Core 2,1 `Microsoft.AspNetCore.App` ve výchozím nastavení používají Metapackage.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Šablony projektů **webové aplikace**ASP.NET Core 2,0 **, prázdné**a **webové rozhraní API** používají soubor [Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage), který obsahuje odkaz na balíček pro [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Proto použití `Microsoft.AspNetCore.All` Metapackage nevyžaduje žádné další kroky k tomu, aby byl odkaz na prohlížeč dostupný pro použití.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Šablona projektu **webové aplikace** ASP.NET Core 1. x obsahuje odkaz na balíček pro balíček [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) . Jiné typy projektů vyžadují, abyste přidali odkaz na balíček `Microsoft.VisualStudio.Web.BrowserLink` .

::: moniker-end

### <a name="configuration"></a>Konfigurace

Zavolejte `UseBrowserLink` v `Startup.Configure` metodě:

```csharp
app.UseBrowserLink();
```

`UseBrowserLink`Volání je obvykle umístěno uvnitř `if` bloku, který povoluje pouze odkaz na prohlížeč ve vývojovém prostředí. Například:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Jak používat Browser Link

Pokud máte otevřený projekt ASP.NET Core, Visual Studio zobrazí ovládací prvek panelu nástrojů pro odkaz na prohlížeč vedle ovládacího prvku panel nástrojů **cíl ladění** :

![Rozevírací nabídka připojit k prohlížeči](using-browserlink/_static/browserLink-dropdown-menu.png)

Z ovládacího prvku panel nástrojů odkaz na prohlížeč můžete:

* Aktualizujte webovou aplikaci v několika prohlížečích najednou.
* Otevřete **řídicí panel odkaz na prohlížeč**.
* Povolí nebo zakáže **odkaz na prohlížeč**. Poznámka: ve výchozím nastavení v aplikaci Visual Studio je odkaz na prohlížeč zakázán.
* Povolí nebo zakáže [automatickou synchronizaci šablon stylů CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Aktualizace webové aplikace v několika prohlížečích najednou

Chcete-li zvolit jeden webový prohlížeč, který má být spuštěn při spuštění projektu, použijte rozevírací nabídku v ovládacím prvku panel nástrojů **cíl ladění** :

![Rozevírací nabídka F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Chcete-li otevřít více prohlížečů najednou, vyberte možnost **Procházet se...** ze stejného rozevíracího seznamu. Podržte stisknutou klávesu <kbd>CTRL</kbd> a vyberte požadované prohlížeče a potom klikněte na **Procházet**:

![Otevřít spoustu prohlížečů najednou](using-browserlink/_static/open-many-browsers-at-once.png)

Následující snímek obrazovky ukazuje Visual Studio s otevřeným zobrazením index a dvěma otevřenými prohlížeči:

![Příklad synchronizace se dvěma prohlížeči](using-browserlink/_static/sync-with-two-browsers-example.png)

Najeďte myší na ovládací prvek panelu nástrojů propojení prohlížeče, aby se zobrazily prohlížeče, které jsou připojené k projektu:

![Hrot přechodu myši](using-browserlink/_static/hoover-tip.png)

Změňte zobrazení indexu a po kliknutí na tlačítko pro obnovení propojení prohlížeče se aktualizují všechny připojené prohlížeče:

![prohlížeče – synchronizace změn](using-browserlink/_static/browsers-sync-to-changes.png)

Odkaz na prohlížeč funguje taky s prohlížeči, které spouštíte z vnějšku sady Visual Studio, a přejdete na adresu URL aplikace.

### <a name="the-browser-link-dashboard"></a>Řídicí panel propojení prohlížeče

Otevřete okno **řídicí panel pro propojení prohlížeče** z rozevírací nabídky odkaz na prohlížeč ke správě připojení s otevřenými prohlížeči:

![otevřít – browserslink – řídicí panel](using-browserlink/_static/open-browserlink-dashboard.png)

Pokud není připojený žádný prohlížeč, můžete spustit relaci bez ladění tak, že vyberete odkaz **Zobrazit v prohlížeči** :

![browserlink – řídicí panel – No – připojení](using-browserlink/_static/browserlink-dashboard-no-connections.png)

V opačném případě jsou připojené prohlížeče zobrazeny s cestou ke stránce, kterou zobrazují jednotlivé prohlížeče:

![browserlink-řídicí panel – dvě připojení](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Můžete také kliknout na název jednotlivého prohlížeče a aktualizovat pouze tento prohlížeč.

### <a name="enable-or-disable-browser-link"></a>Povolit nebo zakázat odkaz na prohlížeč

Když znovu povolíte odkaz na prohlížeč po jeho zakázání, je nutné aktualizovat prohlížeče, aby se znovu připojily.

### <a name="enable-or-disable-css-auto-sync"></a>Povolí nebo zakáže automatickou synchronizaci šablon stylů CSS.

Pokud je povolena automatická synchronizace šablon stylů CSS, připojené prohlížeče se automaticky aktualizují, když provedete změny v souborech CSS.

## <a name="how-it-works"></a>Jak to funguje

Odkaz na prohlížeč používá [SignalR](xref:signalr/introduction) k vytvoření komunikačního kanálu mezi Visual Studio a prohlížečem. Pokud je povolen odkaz na prohlížeč, Visual Studio funguje jako SignalR Server, ke kterému se může připojit více klientů (prohlížečů). Odkaz na prohlížeč také zaregistruje součást middleware v kanálu žádosti ASP.NET Core. Tato součást vloží speciální `<script>` odkazy na všechny žádosti stránky ze serveru. Odkazy na skript můžete zobrazit tak, že v prohlížeči vyberete **Zobrazit zdroj** a posunete se na konec `<body>` obsahu značky:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Vaše zdrojové soubory se nemění. Komponenta middlewaru vloží odkaz na skript dynamicky.

Vzhledem k tomu, že kód na straně prohlížeče je JavaScript, funguje na všech prohlížečích, které SignalR podporují bez nutnosti použití modulu plug-in prohlížeče.
