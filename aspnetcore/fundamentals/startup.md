---
title: Spuštění aplikace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2019
uid: fundamentals/startup
ms.openlocfilehash: 081eaa772d136477a37a3392877886327e0cda7c
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634041"
---
# <a name="app-startup-in-aspnet-core"></a>Spuštění aplikace v ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex)a [Steve Smith](https://ardalis.com)

Třída `Startup` konfiguruje služby a kanál žádostí aplikace.

## <a name="the-startup-class"></a>Spouštěcí třída

Aplikace ASP.NET Core používají `Startup` třídu, která je pojmenována `Startup` podle konvence. Třída `Startup`:

* Volitelně zahrnuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> pro konfiguraci *služeb*aplikace. Služba je opakovaně použitelná součást, která poskytuje funkce aplikace. Služby jsou *registrovány* v `ConfigureServices` a spotřebovány v rámci aplikace přes [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> k vytvoření kanálu zpracování požadavků aplikace.

`ConfigureServices` a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

Třída `Startup` je určena při sestavení [hostitele](xref:fundamentals/index#host) aplikace. Třída `Startup` je obvykle určena voláním metody [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) na tvůrci hostitele:

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Hostitel poskytuje služby, které jsou k dispozici pro konstruktor `Startup` třídy. Aplikace přidá další služby prostřednictvím `ConfigureServices`. Hostitelská i Aplikační služba jsou k dispozici v `Configure` a v celé aplikaci.

Při použití [obecného hostitele](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) lze do konstruktoru `Startup` vložit pouze následující typy služeb:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Většina služeb není k dispozici, dokud není volána metoda `Configure`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Hostitel poskytuje služby, které jsou k dispozici pro konstruktor `Startup` třídy. Aplikace přidá další služby prostřednictvím `ConfigureServices`. Hostitelská i Aplikační služba jsou pak k dispozici v `Configure` a v celé aplikaci.

Běžné použití [Injektáže závislosti](xref:fundamentals/dependency-injection) do `Startup` třídy je vložení:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> ke konfiguraci služeb podle prostředí.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> ke čtení konfigurace.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> vytvoření protokolovacího nástroje v `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Většina služeb není k dispozici, dokud není volána metoda `Configure`.

::: moniker-end

### <a name="multiple-startup"></a>Vícenásobné spuštění

Když aplikace definuje samostatné třídy `Startup` pro různá prostředí (například `StartupDevelopment`), je při běhu vybrána odpovídající třída `Startup`. Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí. Pokud je aplikace spuštěna ve vývojovém prostředí a obsahuje třídu `Startup` a třídu `StartupDevelopment`, je použita třída `StartupDevelopment`. Další informace najdete v tématu [použití více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) . Informace o zpracování chyb během spuštění najdete v tématu [zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:

* Volitelné.
* Volá se hostitelem před metodou `Configure` ke konfiguraci služeb aplikace.
* Kde jsou [Možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Hostitel může nakonfigurovat některé služby před voláním `Startup` metody. Další informace najdete v tématu [hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují zásadní instalaci, existují `Add{Service}` metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. **Přidejte**například DbContext, **přidejte**DefaultIdentity, **přidejte**EntityFrameworkStores a **přidejte**RazorPages:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

Přidání služeb do kontejneru služby je zpřístupní v rámci aplikace a v metodě `Configure`. Služby jsou vyřešeny prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

::: moniker range="< aspnetcore-3.0"

Další informace o `SetCompatibilityVersion`najdete v tématu [SetCompatibilityVersion](xref:mvc/compatibility-version) .

::: moniker-end

## <a name="the-configure-method"></a>Metoda Configure

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> slouží k určení způsobu, jakým aplikace reaguje na požadavky HTTP. Kanál požadavků je nakonfigurován přidáním součástí [middlewaru](xref:fundamentals/middleware/index) do instance <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` je k dispozici pro metodu `Configure`, ale není zaregistrována v kontejneru služby. Hostování vytvoří `IApplicationBuilder` a předá ho přímo do `Configure`.

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:

* [Stránka s výjimkou pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužná rutina výjimky](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Přesměrování HTTPS](xref:security/enforcing-ssl)
* [Statické soubory](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) a [Razor Pages](xref:razor-pages/index)

::: moniker range="< aspnetcore-3.0"

* [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

::: moniker-end

Každá metoda rozšíření `Use` přidá do kanálu žádosti jednu nebo více součástí middlewaru. <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> například nakonfiguruje [middleware](xref:fundamentals/middleware/index) pro poskytování [statických souborů](xref:fundamentals/static-files).

Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu řetězce, pokud je to vhodné.

::: moniker range=">= aspnetcore-3.0"

Další služby, například `IWebHostEnvironment`, `ILoggerFactory`nebo cokoli definované v `ConfigureServices`, lze zadat v signatuře metody `Configure`. Tyto služby jsou vloženy, pokud jsou k dispozici.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Další služby, například `IHostingEnvironment` a `ILoggerFactory`nebo cokoli definované v `ConfigureServices`, lze zadat v signatuře metody `Configure`. Tyto služby jsou vloženy, pokud jsou k dispozici.

::: moniker-end

Další informace o tom, jak používat `IApplicationBuilder` a pořadí zpracování middlewaru, najdete v tématu <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurace služeb bez spuštění

Chcete-li nakonfigurovat služby a kanál zpracování požadavků bez použití třídy `Startup`, zavolejte `ConfigureServices` a `Configure` metody usnadnění v Tvůrci hostitele. Několik volání, která se `ConfigureServices` připojit k druhému. Pokud existuje více volání metody `Configure`, je použita poslední volání `Configure`.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a>Rozšířené spuštění s filtry po spuštění

Použít <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání `Use{Middleware}`. `IStartupFilter` používá ASP.NET Core k přidání výchozích hodnot na začátek kanálu, aniž by bylo nutné, aby autor aplikace explicitně zaregistroval výchozí middleware. `IStartupFilter` umožňuje jinému volání komponenty `Use{Middleware}` jménem autora aplikace.
* Chcete-li vytvořit kanál `Configure`ch metod. [IStartupFilter. Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middleware pro spuštění před nebo po middlewaru přidaném knihovnami.

`IStartupFilter` implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, které obdrží a vrátí `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definuje třídu pro konfiguraci kanálu požadavků aplikace. Další informace najdete v tématu [vytvoření kanálu middlewaru pomocí IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` může do kanálu požadavků přidat jeden nebo více middlewarů. Filtry jsou vyvolány v pořadí, v jakém byly přidány do kontejneru služby. Filtry můžou přidat middleware před nebo po předání řízení k dalšímu filtru, takže se připojí k začátku nebo konci kanálu aplikace.

Následující příklad ukazuje, jak zaregistrovat middleware pomocí `IStartupFilter`. Middleware `RequestSetOptionsMiddleware` nastaví hodnotu možností z parametru řetězce dotazu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware` je nakonfigurována v `RequestSetOptionsStartupFilter` třídě:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware` je nakonfigurována v `RequestSetOptionsStartupFilter` třídě:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

`IStartupFilter` je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

Pokud je k dispozici parametr řetězce dotazu pro `option`, middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.

Pořadí spouštění middlewaru je nastaveno podle pořadí `IStartupFilter` registrace:

* Více implementací `IStartupFilter` může komunikovat se stejnými objekty. Pokud je řazení důležité, porovnejte své `IStartupFilter` registraci služeb, aby odpovídaly pořadí, ve kterém by měly být své middleware spuštěny.
* Knihovny můžou přidat middlewaru s jednou nebo více `IStartupFilter` implementacemi, které se spouštějí před nebo po jiných middlewarech aplikací registrovaných pomocí `IStartupFilter`. Vyvolání middlewaru `IStartupFilter` před přidáním middlewaru `IStartupFilter`knihovny:

  * Před přidáním knihovny do kontejneru služby umístěte registraci služby.
  * Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidat konfiguraci při spuštění z externího sestavení

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídy aplikace. Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
