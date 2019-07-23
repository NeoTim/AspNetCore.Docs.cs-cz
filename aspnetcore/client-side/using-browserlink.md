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
# <a name="browser-link-in-aspnet-core"></a>Browser Link v ASP.NET Core

Podle [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), a [Petr Dykstra](https://github.com/tdykstra)

Browser Link je funkce v sadě Visual Studio, který vytvoří komunikační kanál mezi vývojové prostředí a jeden nebo více webových prohlížečů. Můžete použít odkaz prohlížeče pro aktualizaci webové aplikace v několika prohlížečích najednou, což je užitečné pro testování prohlížečů.

## <a name="browser-link-setup"></a>Nastavit propojení prohlížeče

::: moniker range=">= aspnetcore-2.1"

Při převodu projektu aplikace ASP.NET Core 2.0 k ASP.NET Core 2.1 a omezování [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), nainstalujte [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) balíček pro BrowserLink funkce. Použijte šablony projektů ASP.NET Core 2.1 `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all ve výchozím nastavení.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **webovou aplikaci**, **prázdný**, a **webového rozhraní API** projektu pomocí šablony [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) , který obsahuje odkaz na balíček pro [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Proto pomocí `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all vyžaduje, abyste měli k dispozici pro použití Browser Link žádná další akce.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **webovou aplikaci** šablona projekt obsahuje odkaz na balíček pro [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) balíčku. **Prázdný** nebo **webového rozhraní API** šablony projektů vyžadují, abyste přidat odkaz na balíček pro `Microsoft.VisualStudio.Web.BrowserLink`.

Protože tato funkce sady Visual Studio, je nejjednodušší způsob přidání balíčku do **prázdný** nebo **webového rozhraní API** šablony projektu, je otevřít **Konzola správce balíčků** (**Zobrazení** > **ostatní Windows** > **Konzola správce balíčků**) a spusťte následující příkaz:

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

Alternativně můžete použít **Správce balíčků NuGet**. Klikněte pravým tlačítkem na název projektu v **Průzkumníka řešení** a zvolte **spravovat balíčky NuGet**:

![Správce balíčků NuGet otevřít](using-browserlink/_static/open-nuget-package-manager.png)

Najít a nainstalovat balíček:

![Přidání balíčku pomocí Správce balíčků NuGet](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a>Konfigurace

V `Startup.Configure` metody:

```csharp
app.UseBrowserLink();
```

Obvykle je kód uvnitř `if` blok, který povoluje pouze odkazy v prohlížeči ve vývojovém prostředí, jak je znázorněno zde:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Další informace naleznete v tématu [Používání více prostředí](xref:fundamentals/environments).

## <a name="how-to-use-browser-link"></a>Postup použití funkce Browser Link

Pokud máte otevřít projekt ASP.NET Core, Visual Studio zobrazí vedle ovládacím prvkem panel nástrojů odkazy v prohlížeči **cíl ladění** ovládací prvek panelu nástrojů:

![Browser Link rozevírací nabídky](using-browserlink/_static/browserLink-dropdown-menu.png)

Browser Link ovládací prvek panelu nástrojů můžete:

* Aktualizace webové aplikace v několika prohlížečích najednou.
* Otevřít **řídicím odkazů prohlížeče**.
* Povolení nebo zakázání **Browser Link**. Poznámka: Browser Link je zakázané ve výchozím nastavení v sadě Visual Studio 2017 (15.3).
* Povolení nebo zakázání [Automatická synchronizace šablon stylů CSS](#enable-or-disable-css-auto-sync).

> [!NOTE]
> Některé sady Visual Studio moduly plug-in, zejména *webové rozšíření balíčku 2015* a *webové rozšíření balíčku 2017*, nabízí rozšířené funkce pro Browser Link, ale některé další funkce nefungují s ASP. Projekty .NET Core.

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Aktualizace webové aplikace v několika prohlížečích najednou

Chcete-li zvolit jeden webový prohlížeč na spuštění při spuštění projektu, použijte rozevírací nabídky v **cíl ladění** ovládací prvek panelu nástrojů:

![Rozevírací nabídka F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Chcete-li otevřít více prohlížečů najednou, zvolte **nastavit prohlížeče...**  stejné rozevíracím seznamu. Podržte stisknutou klávesu CTRL k výběru prohlížeče, které chcete a potom klikněte na **Procházet**:

![Stačí otevřít mnoha prohlížečů](using-browserlink/_static/open-many-browsers-at-once.png)

Tady je snímek obrazovky sady Visual Studio s zobrazení indexu otevřít a otevřít prohlížečů:

![Synchronizovat s ukázkou prohlížečů](using-browserlink/_static/sync-with-two-browsers-example.png)

Najeďte myší ovládacím prvkem panel nástrojů odkazy v prohlížeči zobrazit prohlížečů, které jsou připojené k projektu:

![Popis tlačítka při najetí myší](using-browserlink/_static/hoover-tip.png)

Změnit zobrazení indexu a všech propojených prohlížečů se aktualizují po kliknutí na tlačítko Aktualizovat odkazy v prohlížeči:

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Browser Link funguje taky v prohlížečích, které můžete spustit z mimo sadu Visual Studio a přejděte na adresu URL aplikace.

### <a name="the-browser-link-dashboard"></a>Řídicím panelu odkazů prohlížeče

Browser Link rozevírací nabídka spravovat připojení otevřené prohlížeče otevřete řídicím panelu odkazů prohlížeče:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Pokud je připojený žádný prohlížeč, můžete začít relaci – ladění tak, že vyberete *zobrazit v prohlížeči* odkaz:

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

V opačném případě propojených prohlížečů jsou uvedeny cestu ke stránce, která zobrazuje každou prohlížeče:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Pokud chcete můžete klikněte na název uvedené prohlížeče k aktualizaci tohoto jediného prohlížeče.

### <a name="enable-or-disable-browser-link"></a>Povolení nebo zakázání Browser Link

Po jeho zakázání proto potřeba znovu povolit Browser Link, je nutné aktualizovat prohlížeče je znovu připojit.

### <a name="enable-or-disable-css-auto-sync"></a>Povolit nebo zakázat automatickou synchronizaci šablon stylů CSS

Pokud je povolena automatická synchronizace šablon stylů CSS, propojených prohlížečů se automaticky aktualizují při provádět změny na soubory šablon stylů CSS.

## <a name="how-it-works"></a>Jak to funguje

Browser Link používá SignalR k vytvoření komunikačního kanálu mezi Visual Studio a prohlížečem. Když je povolené Browser Link, Visual Studio funguje jako více klientů (prohlížečů) můžete připojit k serveru funkce SignalR. Browser Link také zaregistruje komponentu middleware v kanálu požadavku ASP.NET Core. Tato součást Vloží speciální `<script>` odkazy do každého požadavku stránky ze serveru. Uvidíte odkazy na skript tak, že vyberete **zobrazit zdroj** v prohlížeči a posouvání na konec objektu `<body>` označení obsahu:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Zdrojové soubory se nemění. Komponenta middlewaru dynamicky vkládá skriptových odkazů.

Protože kódu prohlížeče na všech JavaScript, funguje ve všech prohlížečích, které podporuje funkce SignalR bez nutnosti modul plug-in prohlížeče.
