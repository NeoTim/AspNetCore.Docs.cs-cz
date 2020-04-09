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
# <a name="browser-link-in-aspnet-core"></a>Odkaz na prohlížeč v ASP.NET jádru

Od [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), a [Tom Dykstra](https://github.com/tdykstra)

Odkaz prohlížeče je funkce sady Visual Studio. Vytvoří komunikační kanál mezi vývojovým prostředím a jedním nebo více webovými prohlížeči. Pomocí odkazu prohlížeče můžete aktualizovat webovou aplikaci v několika prohlížečích najednou, což je užitečné pro testování mezi prohlížeči.

## <a name="browser-link-setup"></a>Nastavení odkazu na prohlížeč

::: moniker range=">= aspnetcore-3.0"

Přidejte do projektu balíček [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) Pro ASP.NET Core Razor Pages nebo MVC projekty, také povolit runtime <xref:mvc/views/view-compilation>kompilaci Razor (*.cshtml*) soubory, jak je popsáno v . Změny syntaxe holicího strojku se použijí pouze v případě, že byla povolena kompilace modulu runtime.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Při převodu projektu ASP.NET Core 2.0 na ASP.NET jádrem 2.1 a při přechodu na [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app)nainstalujte balíček [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pro funkci Odkaz prohlížeče. Šablony projektu ASP.NET Core 2.1 `Microsoft.AspNetCore.App` používají metabalíček ve výchozím nastavení.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Šablony projektu ASP.NET Core 2.0 **,** **Empty**a **Web API** používají [metabalíček Microsoft.AspNetCore.All](xref:fundamentals/metapackage), který obsahuje odkaz na balíček pro [microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Proto použití `Microsoft.AspNetCore.All` metabalíčku nevyžaduje žádné další akce, aby odkaz prohlížeče k dispozici pro použití.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Šablona projektu ASP.NET Core 1.x **Web Application** obsahuje odkaz na balíček pro balíček [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) Jiné typy projektů vyžadují přidání odkazu `Microsoft.VisualStudio.Web.BrowserLink`na balíček do aplikace .

::: moniker-end

### <a name="configuration"></a>Konfigurace

Volání `UseBrowserLink` `Startup.Configure` metody:

```csharp
app.UseBrowserLink();
```

Volání `UseBrowserLink` je obvykle umístěn `if` uvnitř bloku, který umožňuje pouze odkaz prohlížeče ve vývojovém prostředí. Příklad:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Jak používat odkaz na prohlížeč

Pokud máte otevřený ASP.NET základní projekt, Visual Studio zobrazí ovládací prvek panelu nástrojů Odkaz prohlížeče vedle ovládacího prvku panelu nástrojů **Ladění cíle:**

![Rozevírací nabídka Odkaz prohlížeče](using-browserlink/_static/browserLink-dropdown-menu.png)

V ovládacím panelu nástrojů Odkaz prohlížeče můžete:

* Aktualizujte webovou aplikaci v několika prohlížečích najednou.
* Otevřete **řídicí panel odkazu prohlížeče**.
* Povolte nebo zakažte **odkaz na prohlížeč**. Poznámka: Odkaz prohlížeče je ve výchozím nastavení v sadě Visual Studio zakázán.
* Povolte nebo zakažte [automatické synchronizaci CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Aktualizace webové aplikace v několika prohlížečích najednou

Chcete-li vybrat jeden webový prohlížeč, který chcete spustit při spuštění projektu, použijte rozevírací nabídku v ovládacím panelu nástrojů **Ladění cíle:**

![Rozevírací nabídka F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Chcete-li otevřít více prohlížečů najednou, zvolte **Procházet pomocí...** ze stejného rozevíracího balíčku. Podržením klávesy <kbd>Ctrl</kbd> vyberte požadované prohlížeče a klikněte na **Procházet**:

![Otevření mnoha prohlížečů najednou](using-browserlink/_static/open-many-browsers-at-once.png)

Následující snímek obrazovky ukazuje Visual Studio s otevřeným zobrazením Rejstříku a dvěma otevřenými prohlížeči:

![Příklad synchronizace se dvěma prohlížeči](using-browserlink/_static/sync-with-two-browsers-example.png)

Chcete-li zobrazit prohlížeče připojené k projektu, najeďte na panel nástrojů Odkaz prohlížeče:

![Špička pro vznášení](using-browserlink/_static/hoover-tip.png)

Změňte zobrazení rejstříku a všechny připojené prohlížeče se aktualizují, když kliknete na tlačítko Aktualizovat odkaz prohlížeče:

![prohlížeče-synchronizace se změnami](using-browserlink/_static/browsers-sync-to-changes.png)

Odkaz na prohlížeč funguje také s prohlížeči, které spustíte z externího visual studia a přejdete na adresu URL aplikace.

### <a name="the-browser-link-dashboard"></a>Řídicí panel odkazu prohlížeče

Chcete-li spravovat připojení s otevřenými prohlížeči, otevřete okno **Řídicí panel odkazu prohlížeče** v rozevírací nabídce Odkaz prohlížeče:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Pokud není připojen žádný prohlížeč, můžete zahájit relaci bez ladění výběrem odkazu **Zobrazení v prohlížeči:**

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

V opačném případě se připojené prohlížeče zobrazí s cestou ke stránce, kterou každý prohlížeč zobrazuje:

![odkaz na prohlížeč-řídicí panel-dvě připojení](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Můžete také kliknout na název jednotlivého prohlížeče a aktualizovat pouze tento prohlížeč.

### <a name="enable-or-disable-browser-link"></a>Povolení nebo zakázání odkazu prohlížeče

Po opětovném povolení odkazu prohlížeče po jeho zakázání je nutné aktualizovat prohlížeče, abyste je znovu připojili.

### <a name="enable-or-disable-css-auto-sync"></a>Povolení nebo zakázání automatické synchronizace CSS

Je-li povolena automatická synchronizace CSS, připojené prohlížeče se automaticky aktualizují, když provedete jakoukoli změnu souborů CSS.

## <a name="how-it-works"></a>Jak to funguje

Odkaz prohlížeče [SignalR](xref:signalr/introduction) používá k vytvoření komunikačního kanálu mezi Visual Studio a prohlížečem. Je-li povoleno propojení prohlížeče, SignalR visual studio funguje jako server, který více klientů (prohlížečů) se může připojit k. Browser Link také registruje middleware komponenty v kanálu ASP.NET požadavků Core. Tato součást vloží `<script>` speciální odkazy do každého požadavku na stránku ze serveru. Odkazy na skripty můžete zobrazit tak, že v prohlížeči vyberete `<body>` zdroj **zobrazení** a posunete se na konec obsahu značky:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Zdrojové soubory se nezmění. Middleware komponenta vloží odkazy skriptu dynamicky.

Vzhledem k tomu, že kód na straně prohlížeče SignalR je veškerý JavaScript, funguje ve všech prohlížečích, které podporují bez nutnosti modulu plug-in prohlížeče.
