---
title: Spuštění aplikace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/startup
ms.openlocfilehash: b10ddf52ea7d22ea98c295da61c09da8c87fc7a7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633744"
---
# <a name="app-startup-in-aspnet-core"></a>Spuštění aplikace v ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra)a [Steve Smith](https://ardalis.com)

`Startup`Třída konfiguruje služby a kanál žádostí aplikace.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Spouštěcí třída

Aplikace ASP.NET Core používají `Startup` třídu, která je pojmenována `Startup` podle konvence. Třída `Startup`:

* Volitelně zahrnuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> metodu konfigurace *služeb*aplikace. Služba je opakovaně použitelná součást, která poskytuje funkce aplikace. Služby jsou v rámci aplikace *registrovány* `ConfigureServices` a spotřebovány prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .
* Obsahuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> metodu pro vytvoření kanálu zpracování požadavků aplikace.

`ConfigureServices` a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí vzorek je pro [ Razor stránky](xref:razor-pages/index). verze MVC je podobná.


`Startup`Třída je určena při sestavení [hostitele](xref:fundamentals/index#host) aplikace. `Startup`Třída je obvykle určena voláním metody [WebHostBuilderExtensions. UseStartup \<TStartup> ](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) v Tvůrci hostitele:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Hostitel poskytuje služby, které jsou k dispozici pro `Startup` konstruktor třídy. Aplikace přidá další služby prostřednictvím `ConfigureServices` . Hostitelská i Aplikační služba jsou k dispozici v `Configure` i v celé aplikaci.

`Startup`Při použití [obecného hostitele](xref:fundamentals/host/generic-host) () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Většina služeb není k dispozici, dokud `Configure` není volána metoda.

### <a name="multiple-startup"></a>Vícenásobné spuštění

Když aplikace definuje samostatné `Startup` třídy pro různá prostředí (například `StartupDevelopment` ), příslušná `Startup` Třída se vybere za běhu. Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí. Pokud je aplikace spuštěna ve vývojovém prostředí a obsahuje třídu i `Startup` `StartupDevelopment` třídu, `StartupDevelopment` je použita třída. Další informace najdete v tématu [použití více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) . Informace o zpracování chyb během spuštění najdete v tématu [zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

<xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>Metoda je:

* Nepovinný parametr.
* Volá se hostitelem před `Configure` metodou konfigurace služeb aplikace.
* Kde jsou [Možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Hostitel může nakonfigurovat některé služby před `Startup` voláním metod. Další informace najdete v tématu [hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují podstatnou instalaci, jsou k dispozici `Add{Service}` metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> . **Přidejte**například DbContext, **přidejte**výchozí Identity , **přidejte**EntityFrameworkStores a **přidejte** Razor stránky:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

Přidání služeb do kontejneru služby je zpřístupní v rámci aplikace a v `Configure` metodě. Služby jsou vyřešeny prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .

## <a name="the-configure-method"></a>Metoda Configure

<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>Metoda slouží k určení způsobu, jakým aplikace reaguje na požadavky HTTP. Kanál požadavků je nakonfigurován přidáním součástí [middlewaru](xref:fundamentals/middleware/index) do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance. `IApplicationBuilder` je k dispozici pro `Configure` metodu, ale není zaregistrována v kontejneru služby. Hostování vytvoří `IApplicationBuilder` a předá ho přímo do `Configure` .

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:

* [Stránka s výjimkou pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužná rutina výjimky](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Přesměrování HTTPS](xref:security/enforcing-ssl)
* [Statické soubory](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) a [ Razor stránky](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí vzorek je pro [ Razor stránky](xref:razor-pages/index). verze MVC je podobná.

Každá `Use` metoda rozšíření přidá do kanálu žádosti jednu nebo více součástí middlewaru. Například <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> nakonfiguruje [middleware](xref:fundamentals/middleware/index) na poskytování [statických souborů](xref:fundamentals/static-files).

Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu řetězce, pokud je to vhodné.

Další služby, jako například `IWebHostEnvironment` , `ILoggerFactory` nebo cokoli definované v `ConfigureServices` , lze zadat v `Configure` signatuře metody. Tyto služby jsou vloženy, pokud jsou k dispozici.

Další informace o tom, jak používat `IApplicationBuilder` a pořadí zpracování middlewaru, najdete v tématu <xref:fundamentals/middleware/index> .

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurace služeb bez spuštění

Ke konfiguraci služeb a kanálu zpracování požadavků bez použití `Startup` třídy, volání `ConfigureServices` a `Configure` praktických metod v Tvůrci hostitele. Několik volání, která se mají `ConfigureServices` navzájem připojit. Pokud `Configure` existuje více volání metody, `Configure` je použito poslední volání.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Rozšířené spuštění s filtry po spuštění

Použijte <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :

* Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání `Use{Middleware}` . `IStartupFilter` se používá ASP.NET Core k přidání výchozích hodnot na začátek kanálu, aniž by bylo nutné, aby autor aplikace explicitně zaregistroval výchozí middleware. `IStartupFilter` umožňuje jinému volání komponenty `Use{Middleware}` jménem autora aplikace.
* Pro vytvoření kanálu `Configure` metod. [IStartupFilter.Configurovat](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middlewaru tak, aby běžel před nebo po middlewaru, který přidaly knihovny.

`IStartupFilter` implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> , které přijímá a vrací `Action<IApplicationBuilder>` . <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>Definuje třídu pro konfiguraci kanálu požadavků aplikace. Další informace najdete v tématu [vytvoření kanálu middlewaru pomocí IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` z nich může do kanálu požadavků přidat jeden nebo více middlewarů. Filtry jsou vyvolány v pořadí, v jakém byly přidány do kontejneru služby. Filtry můžou přidat middleware před nebo po předání řízení k dalšímu filtru, takže se připojí k začátku nebo konci kanálu aplikace.

Následující příklad ukazuje, jak registrovat middleware pomocí `IStartupFilter` . `RequestSetOptionsMiddleware`Middleware nastaví hodnotu možností z parametru řetězce dotazu:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware`Je nakonfigurován ve `RequestSetOptionsStartupFilter` třídě:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter`Je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> .

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Pokud je k dispozici parametr řetězce dotazu pro `option` , middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.

Pořadí spouštění middlewaru je nastaveno podle pořadí `IStartupFilter` registrací:

* Více `IStartupFilter` implementací může komunikovat se stejnými objekty. Pokud je řazení důležité, `IStartupFilter` zajistěte, aby registrace služeb odpovídaly pořadí, ve kterém by měly být spuštěny middleware.
* Knihovny můžou přidat middlewaru s jednou nebo více `IStartupFilter` implementacemi, které se spouštějí před nebo po jiných middlewarech aplikace registrované `IStartupFilter` . Vyvolání `IStartupFilter` middlewaru před middlewarem přidaným knihovnou `IStartupFilter` :

  * Před přidáním knihovny do kontejneru služby umístěte registraci služby.
  * Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidat konfiguraci při spuštění z externího sestavení

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje informací

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Spouštěcí třída

Aplikace ASP.NET Core používají `Startup` třídu, která je pojmenována `Startup` podle konvence. Třída `Startup`:

* Volitelně zahrnuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> metodu konfigurace *služeb*aplikace. Služba je opakovaně použitelná součást, která poskytuje funkce aplikace. Služby jsou v rámci aplikace *registrovány* `ConfigureServices` a spotřebovány prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .
* Obsahuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> metodu pro vytvoření kanálu zpracování požadavků aplikace.

`ConfigureServices` a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

`Startup`Třída je určena při sestavení [hostitele](xref:fundamentals/index#host) aplikace. `Startup`Třída je obvykle určena voláním metody [WebHostBuilderExtensions. UseStartup \<TStartup> ](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) v Tvůrci hostitele:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

Hostitel poskytuje služby, které jsou k dispozici pro `Startup` konstruktor třídy. Aplikace přidá další služby prostřednictvím `ConfigureServices` . Hostitelská i Aplikační služba jsou pak k dispozici v `Configure` a v celé aplikaci.

Běžné použití [Injektáže závislostí](xref:fundamentals/dependency-injection) do `Startup` třídy je vložení:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> ke konfiguraci služeb podle prostředí.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> pro čtení konfigurace.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> Vytvoření protokolovacího nástroje v nástroji `Startup.ConfigureServices` .

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Většina služeb není k dispozici, dokud `Configure` není volána metoda.

### <a name="multiple-startup"></a>Vícenásobné spuštění

Když aplikace definuje samostatné `Startup` třídy pro různá prostředí (například `StartupDevelopment` ), příslušná `Startup` Třída se vybere za běhu. Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí. Pokud je aplikace spuštěna ve vývojovém prostředí a obsahuje třídu i `Startup` `StartupDevelopment` třídu, `StartupDevelopment` je použita třída. Další informace najdete v tématu [použití více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) . Informace o zpracování chyb během spuštění najdete v tématu [zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

<xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>Metoda je:

* Nepovinný parametr.
* Volá se hostitelem před `Configure` metodou konfigurace služeb aplikace.
* Kde jsou [Možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Hostitel může nakonfigurovat některé služby před `Startup` voláním metod. Další informace najdete v tématu [hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují podstatnou instalaci, jsou k dispozici `Add{Service}` metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> . **Přidejte**například DbContext, **přidejte**výchozí Identity , **přidejte**EntityFrameworkStores a **přidejte** Razor stránky:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Přidání služeb do kontejneru služby je zpřístupní v rámci aplikace a v `Configure` metodě. Služby jsou vyřešeny prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .

Další [SetCompatibilityVersion](xref:mvc/compatibility-version) informace najdete v tématu SetCompatibilityVersion `SetCompatibilityVersion` .

## <a name="the-configure-method"></a>Metoda Configure

<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>Metoda slouží k určení způsobu, jakým aplikace reaguje na požadavky HTTP. Kanál požadavků je nakonfigurován přidáním součástí [middlewaru](xref:fundamentals/middleware/index) do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance. `IApplicationBuilder` je k dispozici pro `Configure` metodu, ale není zaregistrována v kontejneru služby. Hostování vytvoří `IApplicationBuilder` a předá ho přímo do `Configure` .

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:

* [Stránka s výjimkou pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužná rutina výjimky](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Přesměrování HTTPS](xref:security/enforcing-ssl)
* [Statické soubory](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) a [ Razor stránky](xref:razor-pages/index)
* [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Každá `Use` metoda rozšíření přidá do kanálu žádosti jednu nebo více součástí middlewaru. Například <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> nakonfiguruje [middleware](xref:fundamentals/middleware/index) na poskytování [statických souborů](xref:fundamentals/static-files).

Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu řetězce, pokud je to vhodné.

Další služby, například `IHostingEnvironment` a `ILoggerFactory` , nebo cokoli definované v `ConfigureServices` , lze zadat v `Configure` signatuře metody. Tyto služby jsou vloženy, pokud jsou k dispozici.

Další informace o tom, jak používat `IApplicationBuilder` a pořadí zpracování middlewaru, najdete v tématu <xref:fundamentals/middleware/index> .

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurace služeb bez spuštění

Ke konfiguraci služeb a kanálu zpracování požadavků bez použití `Startup` třídy, volání `ConfigureServices` a `Configure` praktických metod v Tvůrci hostitele. Několik volání, která se mají `ConfigureServices` navzájem připojit. Pokud `Configure` existuje více volání metody, `Configure` je použito poslední volání.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Rozšířené spuštění s filtry po spuštění

Použijte <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :

* Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání `Use{Middleware}` . `IStartupFilter` se používá ASP.NET Core k přidání výchozích hodnot na začátek kanálu, aniž by bylo nutné, aby autor aplikace explicitně zaregistroval výchozí middleware. `IStartupFilter` umožňuje jinému volání komponenty `Use{Middleware}` jménem autora aplikace.
* Pro vytvoření kanálu `Configure` metod. [IStartupFilter.Configurovat](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middlewaru tak, aby běžel před nebo po middlewaru, který přidaly knihovny.

`IStartupFilter` implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> , které přijímá a vrací `Action<IApplicationBuilder>` . <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>Definuje třídu pro konfiguraci kanálu požadavků aplikace. Další informace najdete v tématu [vytvoření kanálu middlewaru pomocí IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` z nich může do kanálu požadavků přidat jeden nebo více middlewarů. Filtry jsou vyvolány v pořadí, v jakém byly přidány do kontejneru služby. Filtry můžou přidat middleware před nebo po předání řízení k dalšímu filtru, takže se připojí k začátku nebo konci kanálu aplikace.

Následující příklad ukazuje, jak registrovat middleware pomocí `IStartupFilter` . `RequestSetOptionsMiddleware`Middleware nastaví hodnotu možností z parametru řetězce dotazu:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware`Je nakonfigurován ve `RequestSetOptionsStartupFilter` třídě:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter`Je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> .

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Pokud je k dispozici parametr řetězce dotazu pro `option` , middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.

Pořadí spouštění middlewaru je nastaveno podle pořadí `IStartupFilter` registrací:

* Více `IStartupFilter` implementací může komunikovat se stejnými objekty. Pokud je řazení důležité, `IStartupFilter` zajistěte, aby registrace služeb odpovídaly pořadí, ve kterém by měly být spuštěny middleware.
* Knihovny můžou přidat middlewaru s jednou nebo více `IStartupFilter` implementacemi, které se spouštějí před nebo po jiných middlewarech aplikace registrované `IStartupFilter` . Vyvolání `IStartupFilter` middlewaru před middlewarem přidaným knihovnou `IStartupFilter` :

  * Před přidáním knihovny do kontejneru služby umístěte registraci služby.
  * Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidat konfiguraci při spuštění z externího sestavení

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje informací

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end