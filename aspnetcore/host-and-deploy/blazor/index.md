---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 26c8fcf56ab8ca68aeca93560785fc6c1144ab86
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963689"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hostování a nasazení ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace jsou publikované pro nasazení v konfiguraci vydání.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V navigačním panelu vyberte **sestavení** > **publikovat {aplikace}** .
1. Vyberte *cíl publikování*. Chcete-li publikovat místně, vyberte **Složka**.
1. Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění. Vyberte tlačítko **Publikovat**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

```console
dotnet publish -c Release
```

---

Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt. V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.

Aplikace Blazor WebAssembly je publikovaná ve složce */bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* . Aplikace Blazor serveru je publikovaná do složky */Publish/bin/Release/{Target Framework}* .

Prostředky ve složce jsou nasazeny na webový server. Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesta aplikace* je kořenová cesta URL aplikace. Vezměte v úvahu následující hlavní aplikace a aplikace Blazor:

* Je volána `MyApp`hlavní aplikace:
  * Aplikace se fyzicky nachází v *\\d: MyApp*.
  * Žádosti jsou přijímány `https://www.contoso.com/{MYAPP RESOURCE}`na adrese.
* Volaná `CoolApp` aplikace Blazor je dílčí `MyApp`aplikace:
  * Dílčí aplikace je fyzicky umístěná v *d:\\MyApp\\CoolApp*.
  * Žádosti jsou přijímány `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`na adrese.

Bez zadání další konfigurace pro `CoolApp`nemůže podaplikace v tomto scénáři znát, kde se nachází na serveru. Aplikace například nemůže sestavovat správné relativní adresy URL k prostředkům bez vědomí, že se nachází v relativní cestě `/CoolApp/`URL.

Chcete-li zadat konfiguraci `https://www.contoso.com/CoolApp/`pro základní cestu aplikace Blazor `<base>` , je `href` atribut značky nastaven na relativní kořenovou cestu v souboru *wwwroot/index.html* :

```html
<base href="/CoolApp/">
```

Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace. Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace. Základní cesta aplikace se také používá k zachycení hypertextového odkazu, kde `href` cíl odkazu je v rámci základní cesty k umístění&mdash;identifikátoru URI aplikace, Blazor směrovač zpracovává interní navigaci.

V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace. V těchto případech je základní cestou k relativní adrese URL aplikace lomítko (`<base href="/" />`), což je výchozí konfigurace aplikace Blazor. V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru k aplikaci.

Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku `<head>` v rámci prvků značek souboru *wwwroot/index.html* . Nastavte hodnotu `/{RELATIVE URL PATH}/`atributuna (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace. `href`

Pro aplikaci, která má nekořenovou cestu relativní adresy URL (například `<base href="/CoolApp/">`), aplikace *při místním spuštění*nenalezne své prostředky. Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu. Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace `--pathbase` s možností:

```console
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pro aplikaci s relativní cestou `/CoolApp/` URL (`<base href="/CoolApp/">`) je tento příkaz:

```console
dotnet run --pathbase=/CoolApp
```

Aplikace odpoví místně na adrese `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Nasazení

Pokyny k nasazení najdete v následujících tématech:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
