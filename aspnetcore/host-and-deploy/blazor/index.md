---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 271135a0ebe67d31fd8e2bcf672e723814727147
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391343"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hostování a nasazení ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace jsou publikované pro nasazení v konfiguraci vydání.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V navigačním panelu vyberte **Build** > **publikovat aplikaci {Application}** .
1. Vyberte *cíl publikování*. Chcete-li publikovat místně, vyberte **Složka**.
1. Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění. Vyberte tlačítko **publikovat** .

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :

```dotnetcli
dotnet publish -c Release
```

---

Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt. V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.

Aplikace Blazor WebAssembly je publikovaná ve složce */bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* . Aplikace Blazor serveru je publikovaná do složky */Publish/bin/Release/{Target Framework}* .

Prostředky ve složce jsou nasazeny na webový server. Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesta aplikace* je kořenová cesta URL aplikace. Vezměte v úvahu následující hlavní aplikace a aplikace Blazor:

* Hlavní aplikace se nazývá `MyApp`:
  * Aplikace se fyzicky nachází v *d: \\MyApp*.
  * Žádosti jsou přijímány na `https://www.contoso.com/{MYAPP RESOURCE}`.
* Blazor aplikace s názvem `CoolApp` je dílčí aplikace `MyApp`:
  * Dílčí aplikace je fyzicky umístěná v *d: \\MyApp @ no__t-2CoolApp*.
  * Žádosti jsou přijímány na `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Bez zadání další konfigurace pro `CoolApp` nemá dílčí aplikace v tomto scénáři žádné znalosti o tom, kde se nachází na serveru. Aplikace například nemůže sestavovat správné relativní adresy URL ke svým prostředkům bez vědomí, že se nachází na relativní cestě URL `/CoolApp/`.

Chcete-li zadat konfiguraci pro základní cestu aplikace Blazor `https://www.contoso.com/CoolApp/`, je atribut `href` značky `<base>` nastaven na relativní kořenovou cestu v souboru *wwwroot/index.html* :

```html
<base href="/CoolApp/">
```

Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace. Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace. Základní cesta aplikace se také používá k zachycení hypertextového odkazu, kde je `href` cíl odkazu v rámci základní cesty identifikátoru URI aplikace @ no__t-1The Blazor router zpracovává interní navigaci.

V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace. V těchto případech je základní cestou k relativní adrese URL aplikace lomítko (`<base href="/" />`), což je výchozí konfigurace aplikace Blazor. V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru k aplikaci.

Chcete-li nastavit základní cestu aplikace, aktualizujte značku `<base>` v rámci prvků značky `<head>` souboru *wwwroot/index.html* . Nastavte hodnotu atributu `href` na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.

Pro aplikaci, která má nekořenovou cestu relativní adresy URL (například `<base href="/CoolApp/">`), aplikace *při místním spuštění*nenalezne své prostředky. Chcete-li vyřešit tento problém během místního vývoje a testování, můžete dodat *základní argument Path* , který odpovídá hodnotě `href` značky `<base>` za běhu. Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte příkaz `dotnet run` z adresáře aplikace s možností `--pathbase`:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

V případě aplikace s relativní cestou URL `/CoolApp/` (`<base href="/CoolApp/">`) je tento příkaz:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Aplikace odpoví místně na `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Nasazení

Pokyny k nasazení najdete v následujících tématech:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
