---
title: Hostování mimo procesy pomocí služby IIS a ASP.NET Core
author: rick-anderson
description: Přečtěte si o mimoprocesových hostování se službou IIS a modulem ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 048a18349de4d784ae4abb33bd86a2d9c08c609d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755274"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>Hostování mimo procesy pomocí služby IIS a ASP.NET Core 

Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul ASP.NET Core zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě. To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:

![ASP.NET Core modul ve scénáři hostování mimo proces](index/_static/ancm-outofprocess.png)

1. Požadavky přicházející z webu do ovladače HTTP.sys režimu jádra.
1. Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu. Nakonfigurovaný port je obvykle 80 (HTTP) nebo 443 (HTTPS).
1. Modul předá požadavky do Kestrel na náhodném portu pro aplikaci. Náhodný port není 80 nebo 443.

<!-- make this a bullet list -->
Modul ASP.NET Core Určuje port prostřednictvím proměnné prostředí při spuštění. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>Rozšíření nakonfiguruje server tak, aby naslouchal `http://localhost:{PORT}` . Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání HTTPS. Požadavky jsou předávány přes protokol HTTP i v případě, že jsou přijímány službou IIS prostřednictvím protokolu HTTPS.

Jakmile Kestrel požadavek z modulu přijme, požadavek se přepošle do kanálu middleware ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace. Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel. Odpověď aplikace se předává zpátky do služby IIS, která je předává zpátky klientovi HTTP, který žádost inicioval.

Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module> .

Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="enable-the-iisintegration-components"></a>Povolit komponenty IISIntegration

Při sestavování hostitele v `CreateHostBuilder` ( `Program.cs` ) zavolejte <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> na Povolit integraci služby IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Další informace o `CreateDefaultBuilder` naleznete v tématu <xref:fundamentals/host/generic-host#default-builder-settings> .


**Model hostování mimo proces**

Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Možnost                         | Výchozí | Nastavení |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Pokud `true` [Služba IIS Integration middleware](#enable-the-iisintegration-components) nastaví ověření `HttpContext.User` [ověřováním systému Windows](xref:security/authentication/windowsauth). `false`V případě, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` . Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` . Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách. |
| `ForwardClientCertificate`     | `true`  | Pokud `true` je k `MS-ASPNETCORE-CLIENTCERT` dispozici hlavička žádosti, `HttpContext.Connection.ClientCertificate` je naplněna hodnota. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Scénáře proxy serveru a nástroje pro vyrovnávání zatížení

[Middleware pro integraci služby IIS](#enable-the-iisintegration-components) a modul ASP.NET Core jsou nakonfigurované pro přeposílání:

* Schéma (HTTP/HTTPS).
* Vzdálená IP adresa, ze které pochází požadavek.

Middleware pro [integraci služby IIS](#enable-the-iisintegration-components) konfiguruje middleware předávaných hlaviček.

Pro aplikace hostované za dalšími proxy servery a nástroji pro vyrovnávání zatížení může být vyžadována další konfigurace. Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` soubor projektu ( `.csproj` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Hostování v rámci procesu je nastaveno na `InProcess` , což je výchozí hodnota.

Hodnota rozlišuje `<AspNetCoreHostingModel>` malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.

[Kestrel](xref:fundamentals/servers/kestrel) Místo http serveru IIS () se používá server Kestrel `IISHttpServer` .

Pro procesy mimo proces [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> :

* Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
* Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

### <a name="process-name"></a>Název procesu

`Process.GetCurrentProcess().ProcessName`sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (mimo proces).

Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní. Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .

Modul ASP.NET Core může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.
* Předejte tokeny ověřování systému Windows.
