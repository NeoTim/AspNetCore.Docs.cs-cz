---
title: ASP.NET Core hostitele a nasazeníBlazor
author: guardrex
description: Objevte, jak hostovat a Blazor nasazovat aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 9d57b81cd813d02a65b6d3a39c7f1a1aa8a069c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775164"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hostování a nasazení ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace jsou publikované pro nasazení v konfiguraci vydání.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V navigačním panelu vyberte **sestavení** > **publikovat {aplikace}** .
1. Vyberte *cíl publikování*. Chcete-li publikovat místně, vyberte **Složka**.
1. Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění. Vyberte tlačítko **Publikovat**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Vyberte **sestavení** > **publikovat do složky**.
1. Potvrďte, že složka obdrží publikované prostředky, a vyberte **publikovat**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

```dotnetcli
dotnet publish -c Release
```

---

Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt. V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.

Umístění pro publikování:

* BlazorWebAssembly
  * Samostatná &ndash; aplikace se publikuje do složky */bin/Release/{Target Framework}/Publish/wwwroot* . Chcete-li nasadit aplikaci jako statickou lokalitu, zkopírujte obsah složky *wwwroot* na hostitele statických webů.
  * Hostování &ndash; klientské Blazor aplikace WebAssembly je Publikováno do složky */bin/Release/{Target Framework}/Publish/wwwroot* aplikace v serverové aplikaci společně s dalšími statickými webovými prostředky serverové aplikace. Nasaďte obsah složky pro *publikování* na hostitele.
* BlazorServer &ndash; , do které se aplikace publikuje, do složky */bin/Release/{Target Framework}/Publish* . Nasaďte obsah složky pro *publikování* na hostitele.

Prostředky ve složce jsou nasazeny na webový server. Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesta aplikace* je kořenová cesta URL aplikace. Vezměte v úvahu následující ASP.NET Core aplikace Blazor a dílčí aplikace:

* Aplikace ASP.NET Core má název `MyApp`:
  * Aplikace se fyzicky nachází v *d:/MyApp*.
  * Žádosti jsou přijímány `https://www.contoso.com/{MYAPP RESOURCE}`na adrese.
* Blazor Aplikace s názvem `CoolApp` je dílčí aplikace `MyApp`:
  * Dílčí aplikace je fyzicky umístěná v *d:/MyApp/CoolApp*.
  * Žádosti jsou přijímány `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`na adrese.

Bez zadání další konfigurace pro `CoolApp`nemůže podaplikace v tomto scénáři znát, kde se nachází na serveru. Aplikace například nemůže sestavovat správné relativní adresy URL k prostředkům bez vědomí, že se nachází v relativní cestě `/CoolApp/`URL.

Chcete-li zadat konfiguraci Blazor `https://www.contoso.com/CoolApp/`pro základní cestu aplikace `<base>` , je `href` atribut značky nastaven na relativní kořenovou cestu v souboru *Pages/_Host. cshtml* (Blazor Server) nebo souboru *wwwroot/index.html* (Blazor WebAssembly):

```html
<base href="/CoolApp/">
```

BlazorServerové aplikace navíc nastavují základní cestu na straně serveru voláním <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> v kanálu žádosti aplikace: `Startup.Configure`

```csharp
app.UsePathBase("/CoolApp");
```

Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace. Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace. Základní cesta aplikace se používá také k zachycení vybraných hypertextových odkazů, `href` kde cíl odkazu je v rámci prostoru identifikátoru URI základní cesty aplikace. Blazor Směrovač zpracovává interní navigaci.

V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace. V těchto případech je základní cestou k relativní adrese URL aplikace lomítko (`<base href="/" />`), což je výchozí konfigurace Blazor aplikace. V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru aplikace.

Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku v rámci `<head>` prvků značek souboru *Pages/_Host. cshtml* (Blazor Server) nebo souboru *wwwroot/index.html* (Blazor WebAssembly). Nastavte hodnotu `href` atributu na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.

Blazor V případě aplikace WebAssembly s nekořenovou cestou URL, která není kořenovým adresářem (například `<base href="/CoolApp/">`), nemůže aplikace najít své prostředky *při místním spuštění*. Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu. Nezahrnovat koncové lomítko. Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace s `--pathbase` možností:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pro aplikaci Blazor typu WebAssembly s relativní cestou URL `/CoolApp/` (`<base href="/CoolApp/">`) je tento příkaz:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor Aplikace WebAssembly reaguje místně na `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Nasazení

Pokyny k nasazení najdete v následujících tématech:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
