---
title: Hostování a nasazení ASP.NET coreBlazor
author: guardrex
description: Zjistěte, jak Blazor hostovat a nasazovat aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434262"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hostování a nasazení ASP.NET Core Blazor

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace se publikují pro nasazení v konfiguraci vydání.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Na navigačním panelu vyberte **Publikovat** > **{APPLICATION}.**
1. Vyberte *cíl publikování*. Chcete-li publikovat místně, vyberte **složku**.
1. Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění. Vyberte tlačítko **Publikovat**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pomocí příkazu [dotnet publish](/dotnet/core/tools/dotnet-publish) publikujte aplikaci s konfigurací vydání:

```dotnetcli
dotnet publish -c Release
```

---

Publikování aplikace aktivuje [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a [vytvoří](/dotnet/core/tools/dotnet-build) projekt před vytvořením prostředků pro nasazení. V rámci procesu sestavení jsou odebrány nepoužívané metody a sestavení, aby se zmenšila velikost stahování aplikací a doba načítání.

Umístění publikování:

* BlazorWebová sestava
  * Samostatný &ndash; Aplikace je publikována do složky */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.* Chcete-li aplikaci nasadit jako statický web, zkopírujte obsah složky *wwwroot* do statického hostitele webu.
  * Hosted: &ndash; Blazor Klientská webová sestava aplikace je publikována do složky */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace. Nasaďte obsah složky *publikování* do hostitele.
* BlazorServer &ndash; Aplikace je publikována do složky */bin/Release/{TARGET FRAMEWORK}/publish.* Nasaďte obsah složky *publikování* do hostitele.

Datové zdroje ve složce jsou nasazeny na webový server. Nasazení může být ruční nebo automatizovaný proces v závislosti na vývojových nástrojích, které jsou používány.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesta aplikace* je kořenová cesta URL aplikace. Zvažte následující ASP.NET Blazor základní aplikace a podaplikace:

* Aplikace ASP.NET Core `MyApp`se jmenuje :
  * Aplikace fyzicky sídlí na *d:/MyApp*.
  * Žádosti jsou přijímány na adrese `https://www.contoso.com/{MYAPP RESOURCE}`.
* Aplikace Blazor s `CoolApp` názvem je podaplikace `MyApp`:
  * Sub-app fyzicky sídlí na *d:/MyApp/CoolApp*.
  * Žádosti jsou přijímány na adrese `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Bez zadání další `CoolApp`konfigurace pro , dílčí aplikace v tomto scénáři nemá žádné znalosti o umístění na serveru. Aplikace například nemůže vytvořit správné relativní adresy URL pro své prostředky, aniž by `/CoolApp/`věděla, že se nachází na cestě k relativní adrese URL .

Chcete-li poskytnout Blazor konfiguraci pro `https://www.contoso.com/CoolApp/`základní `<base>` cestu aplikace `href` , atribut značky je nastaven na relativní kořenovouBlazor cestu v souboru *Pages/_Host.cshtml* ( Server) nebo *wwwroot/index.html* souboru (Blazor WebAssembly):

```html
<base href="/CoolApp/">
```

BlazorServerové aplikace navíc nastavují základní <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> cestu na straně serveru `Startup.Configure`voláním v kanálu požadavků aplikace :

```csharp
app.UsePathBase("/CoolApp");
```

Poskytnutím relativní cesty URL může komponenta, která není v kořenovém adresáři, vytvářet adresy URL vzhledem ke kořenové cestě aplikace. Součásti na různých úrovních struktury adresáře můžete vytvářet odkazy na jiné prostředky v umístěních v celé aplikaci. Základní cesta aplikace se také používá k `href` zachycení vybraných hypertextových odkazů, kde cíl propojení je v prostoru URI základní cesty aplikace. Směrovač Blazor zpracovává vnitřní navigaci.

V mnoha scénářích hostování je relativní cesta URL k aplikaci kořenem aplikace. V těchto případech je relativní základní cesta url aplikace`<base href="/" />`lomítko ( ), Blazor což je výchozí konfigurace aplikace. V jiných scénářích hostování, jako jsou stránky GitHub a dílčí aplikace IIS, musí být základní cesta aplikace nastavena na relativní cestu url aplikace na serveru.

Chcete-li nastavit základní cestu aplikace, `<base>` aktualizujte značku v elementech `<head>` tagu souboru *Pages/_Host.cshtml* (Server)Blazor nebo *wwwroot/index.html* souboru (Blazor WebAssembly). Nastavte `href` hodnotu `/{RELATIVE URL PATH}/` atributu na (je vyžadováno koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.

U Blazor aplikace WebAssembly s nekořenovou relativní adresou `<base href="/CoolApp/">`URL (například ) se aplikaci nedaří najít své prostředky *při místním spuštění*. Chcete-li tento problém překonat během místního vývoje a testování, můžete `<base>` zadat základní argument *cesty,* který odpovídá `href` hodnotě značky za běhu. Nezahrnejte koncové lomítko. Chcete-li předat argument základní cesty při místním `dotnet run` spuštění aplikace, spusťte `--pathbase` příkaz z adresáře aplikace s možností:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pro Blazor aplikaci WebAssembly s relativní `/CoolApp/` `<base href="/CoolApp/">`adresou URL ( ) je příkaz:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Aplikace Blazor WebAssembly odpovídá místně `http://localhost:port/CoolApp`na adrese .

## <a name="deployment"></a>Nasazení

Pokyny k nasazení najdete v následujících tématech:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
