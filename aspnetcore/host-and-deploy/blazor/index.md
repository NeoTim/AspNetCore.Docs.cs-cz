---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434262"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hostování a nasazení ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace jsou publikované pro nasazení v konfiguraci vydání.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V navigačním panelu vyberte **Build** > **publikovat aplikaci {Application}** .
1. Vyberte *cíl publikování*. Chcete-li publikovat místně, vyberte **Složka**.
1. Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění. Vyberte tlačítko **Publikovat**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

```dotnetcli
dotnet publish -c Release
```

---

Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt. V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.

Umístění pro publikování:

* Blazor WebAssembly
  * Samostatná &ndash; aplikace je publikovaná do složky */bin/Release/{Target Framework}/Publish/wwwroot* . Chcete-li nasadit aplikaci jako statickou lokalitu, zkopírujte obsah složky *wwwroot* na hostitele statických webů.
  * Hostovaná &ndash; aplikace klientské Blazor WebAssembly je publikovaná do složky */bin/Release/{Target Framework}/Publish/wwwroot* aplikace v serverové aplikaci společně s dalšími statickými webovými prostředky serverové aplikace. Nasaďte obsah složky pro *publikování* na hostitele.
* Blazor Server &ndash; aplikace se publikuje do složky */bin/Release/{Target Framework}/Publish* . Nasaďte obsah složky pro *publikování* na hostitele.

Prostředky ve složce jsou nasazeny na webový server. Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesta aplikace* je kořenová cesta URL aplikace. Vezměte v úvahu následující ASP.NET Core aplikace a Blazor dílčí aplikaci:

* Aplikace ASP.NET Core má název `MyApp`:
  * Aplikace se fyzicky nachází v *d:/MyApp*.
  * Žádosti jsou přijímány na `https://www.contoso.com/{MYAPP RESOURCE}`.
* Blazor aplikace s názvem `CoolApp` je dílčí aplikací `MyApp`:
  * Dílčí aplikace je fyzicky umístěná v *d:/MyApp/CoolApp*.
  * Žádosti jsou přijímány na `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Bez zadání další konfigurace pro `CoolApp`nemá dílčí aplikace v tomto scénáři žádné znalosti o tom, kde se nachází na serveru. Aplikace například nemůže vytvořit správné relativní adresy URL ke svým prostředkům bez vědomí, že se nachází na relativní cestě URL `/CoolApp/`.

Chcete-li zadat konfiguraci pro základní cestu aplikace Blazor `https://www.contoso.com/CoolApp/`, je atribut `href` značky `<base>` nastaven na relativní kořenovou cestu v souboru *Pages/_Host. cshtml* (Blazor Server) nebo souboru *wwwroot/index.html* (Blazor WebAssembly):

```html
<base href="/CoolApp/">
```

Blazor serverové aplikace navíc nastaví základní cestu na straně serveru voláním <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> v kanálu žádosti o aplikaci `Startup.Configure`:

```csharp
app.UsePathBase("/CoolApp");
```

Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace. Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace. Základní cesta aplikace se používá také k zachycení vybraných hypertextových odkazů, kde `href` cíl odkazu je v rámci umístění identifikátoru URI základní cesty aplikace. Směrovač Blazor zpracovává interní navigaci.

V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace. V těchto případech je základní cestou k relativní adrese URL aplikace lomítko (`<base href="/" />`), což je výchozí konfigurace Blazor aplikace. V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru aplikace.

Chcete-li nastavit základní cestu aplikace, aktualizujte značku `<base>` v rámci `<head>` prvků značek souboru *Page/_Host. cshtml* (Blazor Server) nebo souboru *wwwroot/index.html* (Blazor WebAssembly). Nastavte hodnotu atributu `href` na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.

Pro Blazor aplikaci WebAssembly s nekořenovou cestou URL jiného typu než root (například `<base href="/CoolApp/">`) se aplikace nepovede najít své prostředky *při místním spuštění*. Chcete-li vyřešit tento problém během místního vývoje a testování, můžete dodat *základní argument Path* , který odpovídá hodnotě `href` `<base>` značce za běhu. Nezahrnovat koncové lomítko. Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte příkaz `dotnet run` z adresáře aplikace s možností `--pathbase`:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pro Blazor aplikaci WebAssembly s relativní cestou URL `/CoolApp/` (`<base href="/CoolApp/">`) je tento příkaz:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Aplikace Blazor WebAssembly reaguje místně na `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Nasazení

Pokyny k nasazení najdete v následujících tématech:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
