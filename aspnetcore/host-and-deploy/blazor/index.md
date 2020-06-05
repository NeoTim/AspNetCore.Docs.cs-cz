---
title: ASP.NET Core hostitele a nasazeníBlazor
author: guardrex
description: Objevte, jak hostovat a nasazovat Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 482b066d347c2c572b0ffb78ccab1ac391195823
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452236"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>ASP.NET Core hostitele a nasazeníBlazor

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace jsou publikované pro nasazení v konfiguraci vydání.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V navigačním panelu vyberte **sestavení**  >  **publikovat {aplikace}** .
1. Vyberte *cíl publikování*. Chcete-li publikovat místně, vyberte **Složka**.
1. Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění. Vyberte tlačítko **Publikovat**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Vyberte **sestavení**  >  **publikovat do složky**.
1. Potvrďte, že složka obdrží publikované prostředky, a vyberte **publikovat**.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

```dotnetcli
dotnet publish -c Release
```

---

Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt. V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.

Umístění pro publikování:

* BlazorWebAssembly
  * Samostatná: aplikace se publikuje do složky */bin/Release/{Target Framework}/Publish/wwwroot* . Chcete-li nasadit aplikaci jako statickou lokalitu, zkopírujte obsah složky *wwwroot* na hostitele statických webů.
  * Hosted: klientská Blazor aplikace WebAssembly je publikovaná do složky */bin/Release/{Target Framework}/Publish/wwwroot* aplikace v serverové aplikaci společně s dalšími statickými webovými prostředky serverové aplikace. Nasaďte obsah složky pro *publikování* na hostitele.
* BlazorServer: aplikace je publikovaná do složky */bin/Release/{Target Framework}/Publish* . Nasaďte obsah složky pro *publikování* na hostitele.

Prostředky ve složce jsou nasazeny na webový server. Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesta aplikace* je kořenová cesta URL aplikace. Vezměte v úvahu následující ASP.NET Core aplikace a Blazor dílčí aplikace:

* Aplikace ASP.NET Core má název `MyApp` :
  * Aplikace se fyzicky nachází v *d:/MyApp*.
  * Žádosti jsou přijímány na adrese `https://www.contoso.com/{MYAPP RESOURCE}` .
* BlazorAplikace s názvem `CoolApp` je dílčí aplikace `MyApp` :
  * Dílčí aplikace je fyzicky umístěná v *d:/MyApp/CoolApp*.
  * Žádosti jsou přijímány na adrese `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

Bez zadání další konfigurace pro nemůže `CoolApp` podaplikace v tomto scénáři znát, kde se nachází na serveru. Aplikace například nemůže sestavovat správné relativní adresy URL k prostředkům bez vědomí, že se nachází v relativní cestě URL `/CoolApp/` .

Chcete-li zadat konfiguraci pro Blazor základní cestu aplikace `https://www.contoso.com/CoolApp/` , `<base>` `href` je atribut značky nastaven na relativní kořenovou cestu v souboru *Pages/_Host. cshtml* ( Blazor Server) nebo souboru *wwwroot/index.html* ( Blazor WebAssembly):

```html
<base href="/CoolApp/">
```

BlazorServerové aplikace navíc nastavují základní cestu na straně serveru voláním <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> v kanálu žádosti aplikace `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace. Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace. Základní cesta aplikace se používá také k zachycení vybraných hypertextových odkazů, kde `href` cíl odkazu je v rámci prostoru identifikátoru URI základní cesty aplikace. BlazorSměrovač zpracovává interní navigaci.

V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace. V těchto případech je základní cestou k relativní adrese URL aplikace lomítko ( `<base href="/" />` ), což je výchozí konfigurace Blazor aplikace. V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru aplikace.

Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku v rámci `<head>` prvků značek souboru *pages/_Host. cshtml* ( Blazor Server) nebo souboru *wwwroot/index.html* ( Blazor WebAssembly). Nastavte `href` hodnotu atributu na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.

V případě Blazor aplikace WebAssembly s nekořenovou cestou URL, která není kořenovým adresářem (například `<base href="/CoolApp/">` ), nemůže aplikace najít své prostředky *při místním spuštění*. Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu. Nezahrnovat koncové lomítko. Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace s `--pathbase` možností:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pro Blazor aplikaci typu WebAssembly s relativní cestou URL `/CoolApp/` ( `<base href="/CoolApp/">` ) je tento příkaz:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

BlazorAplikace WebAssembly reaguje místně na `http://localhost:port/CoolApp` .

## <a name="deployment"></a>Nasazení

Pokyny k nasazení najdete v následujících tématech:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
