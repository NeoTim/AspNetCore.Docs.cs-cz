---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5c37c3d9f424f4c4b814e1955880623fd95179f2
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550378"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor"></a>Hostování a nasazení ASP.NET Core Blazor

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

Blazor aplikace WebAssembly je publikovaná do složky */DIST/PUBLISH/{Assembly Framework} Name}* . Aplikace Blazor serveru je publikovaná ve složce */bin/Release/{Target Framework}/Publish* .

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
