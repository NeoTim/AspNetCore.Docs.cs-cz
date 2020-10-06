---
title: Hostování v rámci procesu pomocí služby IIS a ASP.NET Core
author: rick-anderson
description: Seznamte se s hostováním v procesu pomocí služby IIS a modulu ASP.NET Core.
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: ecf873e6ad2044aae87a5e7dc07316eae0e10912
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755241"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>Hostování v rámci procesu pomocí služby IIS a ASP.NET Core 

Hostování v rámci procesu spouští aplikaci ASP.NET Core ve stejném procesu jako její pracovní proces služby IIS. Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače.

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou v procesu:

![ASP.NET Core modul ve scénáři hostování v rámci procesu](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>Povolit hostování v procesu

Vzhledem k tomu, že ASP.NET Core 3,0, je vnitroprocesové hostování ve výchozím nastavení povoleno pro všechny aplikace nasazené ve službě IIS.

Chcete-li explicitně nakonfigurovat aplikaci pro hostování v rámci procesu, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `InProcess` soubor projektu ( `.csproj` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Obecná architektura

Obecný tok žádosti je následující:

1. Požadavek přijde z webu do ovladače HTTP.sys režimu jádra.
1. Ovladač směruje nativní požadavek na IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).
1. Modul ASP.NET Core obdrží nativní požadavek a předá ho k serveru HTTP služby IIS ( `IISHttpServer` ). HTTP server IIS je vnitroprocesové implementace v rámci procesového serveru pro službu IIS, která převádí požadavek z nativního na spravovanou.

Poté, co server HTTP služby IIS zpracuje požadavek:

1. Požadavek se odešle do kanálu middleware ASP.NET Core.
1. Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.
1. Odpověď aplikace se předává zpět službě IIS prostřednictvím serveru IIS HTTP.
1. Služba IIS odešle odpověď klientovi, který žádost inicioval.

`CreateDefaultBuilder` přidá <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instanci voláním <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> metody pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS ( `w3wp.exe` nebo `iisexpress.exe` ). Testy výkonu označují, že hostování aplikace .NET Core v rámci procesu přináší výrazně vyšší propustnost žádostí v porovnání s hostováním aplikace mimo proces a požadavky na proxy server do [Kestrel](xref:fundamentals/servers/kestrel).

Aplikace publikované jako spustitelný soubor s jedním souborem nejde načíst pomocí modelu hostování v rámci procesu.

## <a name="application-configuration"></a>Konfigurace aplikací

Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do nástroje konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Následující příklad zakáže AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Možnost | Výchozí | Nastavení |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | Pokud `true` Server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth). Pokud `false` Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, pokud je explicitně vyžádala `AuthenticationScheme` . Aby mohla služba fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows `AutomaticAuthentication` . Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Nastaví zobrazovaný název, který se zobrazí uživatelům na přihlašovacích stránkách. |
| `AllowSynchronousIO` | `false` | Zda je povolena synchronní I/O pro `HttpContext.Request` a `HttpContext.Response` . |
| `MaxRequestBodySize` | `30000000` | Získá nebo nastaví velikost textu maximálního požadavku pro `HttpRequest` . Všimněte si, že samotná služba IIS má limit, `maxAllowedContentLength` který bude zpracován před `MaxRequestBodySize` nastavením v `IISServerOptions` . Změna `MaxRequestBodySize` neovlivní `maxAllowedContentLength` . Chcete-li zvýšit `maxAllowedContentLength` , přidejte položku do pole `web.config` pro nastavení `maxAllowedContentLength` na vyšší hodnotu. Další podrobnosti najdete v tématu [Konfigurace](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>Rozdíly mezi procesy v procesu a hostování mimo procesy

Následující vlastnosti platí při hostování v procesu:

* `IISHttpServer`Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS (). Pro vnitroprocesové [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> :

  * Zaregistrujte `IISHttpServer` .
  * Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
  * Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

* [ `requestTimeout` Atribut](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.

* Sdílení fondu aplikací mezi aplikacemi není podporováno. Použijte jeden fond aplikací na aplikaci.

* Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací. Například aplikace publikované pro 32 (x86) musí mít ve svých fondech aplikací IIS povolený 32 bitů. Další informace najdete v části [Vytvoření webu služby IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .

* Zjistila se odpojení klienta. [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)Token zrušení se zruší, když se klient odpojí.

* Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> není volána interně pro inicializaci uživatele. Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována. Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> na přidat ověřovací služby:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* [Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.
