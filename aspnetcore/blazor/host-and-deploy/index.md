---
title: ASP.NET Core hostitele a nasazeníBlazor
author: guardrex
description: Objevte, jak hostovat a nasazovat Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 8a5172db22a17138f7462d140fd97316586e5282
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014201"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a>ASP.NET Core hostitele a nasazeníBlazor

Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publikování aplikace

Aplikace jsou publikované pro nasazení v konfiguraci vydání.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. V navigačním panelu vyberte **sestavení**  >  **publikovat {aplikace}** .
1. Vyberte *cíl publikování*. Chcete-li publikovat místně, vyberte **Složka**.
1. Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění. Vyberte **`Publish`** tlačítko.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Vyberte **sestavení**  >  **publikovat do složky**.
1. Potvrďte, že složka obdrží publikované prostředky a vyberte **`Publish`** .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

[`dotnet publish`](/dotnet/core/tools/dotnet-publish)K publikování aplikace s konfigurací vydané verze použijte příkaz:

```dotnetcli
dotnet publish -c Release
```

---

Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt. V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.

Umístění pro publikování:

* Blazor WebAssembly
  * Samostatná: aplikace se publikuje do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky. Chcete-li nasadit aplikaci jako statickou lokalitu, zkopírujte obsah `wwwroot` složky na hostitele statických webů.
  * Hosted: klientská Blazor WebAssembly aplikace je publikovaná do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace. Nasaďte obsah `publish` složky na hostitele.
* Blazor Server: Aplikace je publikovaná do `/bin/Release/{TARGET FRAMEWORK}/publish` složky. Nasaďte obsah `publish` složky na hostitele.

Prostředky ve složce jsou nasazeny na webový server. Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.

## <a name="app-base-path"></a>Základní cesta aplikace

*Základní cesta aplikace* je kořenová cesta URL aplikace. Vezměte v úvahu následující ASP.NET Core aplikace a Blazor dílčí aplikace:

* Aplikace ASP.NET Core má název `MyApp` :
  * Aplikace se fyzicky nachází na adrese `d:/MyApp` .
  * Žádosti jsou přijímány na adrese `https://www.contoso.com/{MYAPP RESOURCE}` .
* BlazorAplikace s názvem `CoolApp` je dílčí aplikace `MyApp` :
  * Dílčí aplikace se fyzicky nachází v `d:/MyApp/CoolApp` .
  * Žádosti jsou přijímány na adrese `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

Bez zadání další konfigurace pro nemůže `CoolApp` podaplikace v tomto scénáři znát, kde se nachází na serveru. Aplikace například nemůže sestavovat správné relativní adresy URL k prostředkům bez vědomí, že se nachází v relativní cestě URL `/CoolApp/` .

Chcete-li zadat konfiguraci pro Blazor základní cestu aplikace `https://www.contoso.com/CoolApp/` , `<base>` `href` je atribut značky nastaven na relativní kořenovou cestu v `Pages/_Host.cshtml` souboru ( Blazor Server ) nebo `wwwroot/index.html` souboru ( Blazor WebAssembly ):

```html
<base href="/CoolApp/">
```

Blazor Serveraplikace navíc nastaví základní cestu na straně serveru voláním <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> v kanálu žádosti aplikace `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace. Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace. Základní cesta aplikace se používá také k zachycení vybraných hypertextových odkazů, kde `href` cíl odkazu je v rámci prostoru identifikátoru URI základní cesty aplikace. BlazorSměrovač zpracovává interní navigaci.

V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace. V těchto případech je základní cestou k relativní adrese URL aplikace lomítko ( `<base href="/" />` ), což je výchozí konfigurace Blazor aplikace. V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru aplikace.

Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku v rámci `<head>` prvků tagu `Pages/_Host.cshtml` souboru ( Blazor Server ) nebo `wwwroot/index.html` souboru ( Blazor WebAssembly ). Nastavte `href` hodnotu atributu na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.

Pro Blazor WebAssembly aplikaci, která má nekořenovou cestu relativní adresy URL (například `<base href="/CoolApp/">` ), aplikace *při místním spuštění*nenalezne své prostředky. Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu. Nezahrnovat koncové lomítko. Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace s `--pathbase` možností:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pro Blazor WebAssembly aplikaci s relativní cestou URL `/CoolApp/` ( `<base href="/CoolApp/">` ) je tento příkaz:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor WebAssemblyAplikace odpoví místně na adrese `http://localhost:port/CoolApp` .

**Blazor Server`MapFallbackToPage`Konfigurace**

Předat následující cestu do <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> `Startup.Configure` :

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

Zástupný symbol `{RELATIVE PATH}` je nekořenová cesta na serveru. Například `CoolApp` je segment zástupného symbolu, pokud není kořenová adresa URL aplikace `https://{HOST}:{PORT}/CoolApp/` ):

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

**Hostování více Blazor WebAssembly aplikací**

Další informace o hostování více Blazor WebAssembly aplikací v hostovaném Blazor řešení najdete v tématu <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> .

## <a name="deployment"></a>Nasazení

Pokyny k nasazení najdete v následujících tématech:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
