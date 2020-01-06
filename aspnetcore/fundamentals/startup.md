---
title: Spuštění aplikace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/startup
ms.openlocfilehash: 8d878d692f0488f99385b0bd6944fc6cd276c306
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355689"
---
# <a name="app-startup-in-aspnet-core"></a>Spuštění aplikace v ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex)a [Steve Smith](https://ardalis.com)

Třída `Startup` konfiguruje služby a kanál zpracování požadavků aplikace.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Spouštěcí třída

Aplikace ASP.NET Core používají třídu `Startup`, která je konvenčně pojmenována `Startup`. Třída `Startup`:

* Volitelně obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>, která slouží pro konfiguraci *služeb* aplikace. Služba je znovupoužitelná komponenta, která poskytuje funkčnost aplikace. Služby jsou *registrovány* v `ConfigureServices` a spotřebovány v rámci aplikace přes [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> pro vytvoření kanálu pro zpracování požadavků aplikace.

`ConfigureServices` a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.


Třída `Startup` je určena při sestavení [hostitele](xref:fundamentals/index#host) aplikace. Třída `Startup` je obvykle určena voláním metody [WebHostBuilderExtensions. UseStartup\<TStartup >](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) v Tvůrci hostitele:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Hostitel poskytuje služby, které jsou k dispozici konstruktoru třídy `Startup`. Aplikace přidává další služby prostřednictvím metody `ConfigureServices`. Hostitelská i Aplikační služba jsou k dispozici v `Configure` a v celé aplikaci.

Při použití [obecného hostitele](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) lze do konstruktoru `Startup` vložit pouze následující typy služeb:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Většina služeb není k dispozici, dokud není volána metoda `Configure`.

### <a name="multiple-startup"></a>Vícenásobné spuštění

Pokud aplikace definuje samostatnou třídu `Startup` pro různá prostředí (například `StartupDevelopment`), odpovídající třída `Startup` je vybrána v době běhu. Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna. Pokud aplikace běží ve vývojovém prostředí a obsahuje třídu `Startup` i třídu `StartupDevelopment`, použije se třída `StartupDevelopment` . Další informace naleznete v tématu [Používání více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) . Informace o zpracování chyb během spuštění naleznete v tématu [Zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:

* Volitelné.
* Je volána hostitelem před voláním metody `Configure` pro konfiguraci služeb aplikace.
* metoda, ve které jsou [možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Hostitel může nakonfigurovat některé služby před voláním metody `Startup`. Další informace najdete v tématu [Hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují rozsáhlou konfiguraci, existují rozšiřující metody `Add{Service}` nad <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. **Přidejte**například DbContext, **přidejte**DefaultIdentity, **přidejte**EntityFrameworkStores a **přidejte**RazorPages:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

Po přidání služeb do kontejneru jsou tyto služby k dispozici v celé aplikaci a v rámci metody `Configure`. Služby jsou řešeny prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

## <a name="the-configure-method"></a>Metoda Configure

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se používá k určení toho, jak aplikace reaguje na HTTP požadavky. Kanál požadavků se konfiguruje tak, že přidáte [middlewarové](xref:fundamentals/middleware/index) komponenty do instance <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` je dostupný metodě `Configure`, není však registrován v kontejneru služeb. Hosting vytváří `IApplicationBuilder` a předává jej přímo metodě `Configure`

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:

* [Stránku výjimek pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužné rutiny výjimek](xref:fundamentals/error-handling#exception-handler-page)
* [Zabezpečení striktního HTTP přenosu (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Přesměrování HTTPS](xref:security/enforcing-ssl)
* [Statické soubory](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) a [Razor Pages](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.

Každá rozšiřující metoda `Use` přidá jednu nebo více middlewarových komponent do kanálu zpracování požadavku. <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> například nakonfiguruje [middleware](xref:fundamentals/middleware/index) pro poskytování [statických souborů](xref:fundamentals/static-files).

Každá middlewarová komponenta v kanálu zpracování požadavků zodpovídá za vyvolání další komponenty v kanálu, případně může provést předčasné ukončení řetězce volání.

Další služby, například `IWebHostEnvironment`, `ILoggerFactory`nebo cokoli definované v `ConfigureServices`, lze zadat v signatuře metody `Configure`. Tyto služby jsou vloženy, pokud jsou k dispozici.

Další informace o tom, jak používat `IApplicationBuilder` a jaké je pořadí zpracování middlewarů, naleznete v tématu <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurace služeb bez spuštění

Ke konfiguraci služeb a kanálu zpracování požadavků bez použití třídy `Startup` využijte volání usnadňujících metod `ConfigureServices` a `Configure` tvůrce (builderu) hostitele. Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání. Pokud existuje více volání metody `Configure`, využije se poslední volání `Configure`.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Rozšíření třídy Startup pomocí filtrů po spuštění

Použít <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání `Use{Middleware}`. `IStartupFilter` používá ASP.NET Core k přidání výchozích hodnot na začátek kanálu, aniž by bylo nutné, aby autor aplikace explicitně zaregistroval výchozí middleware. `IStartupFilter` umožňuje jinému volání komponenty `Use{Middleware}` jménem autora aplikace.
* Chcete-li vytvořit kanál `Configure`ch metod. [IStartupFilter. Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middleware pro spuštění před nebo po middlewaru přidaném knihovnami.

`IStartupFilter` implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, které obdrží a vrátí `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definuje třídu pro konfiguraci kanálu zpracování požadavků vaší aplikace. Další informace naleznete v tématu [Vytvoření kanálu middlewaru s IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` může do kanálu požadavků přidat jeden nebo více middlewarů. Filtry jsou volány v pořadí, ve kterém byly přidány do kontejneru služeb. Filtry mohou přidávat middleware před nebo po předání řízení dalšímu filtru, tedy připojují se na začátek nebo konec kanálu aplikace.

Následující příklad ukazuje, jak zaregistrovat middleware pomocí `IStartupFilter`. Middleware `RequestSetOptionsMiddleware` nastaví hodnoty voleb z parametrů řetězce dotazu (query string parameters):

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware` je nakonfigurovaný ve třídě `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Pokud je k dispozici parametr řetězce dotazu pro `option`, middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.

Pořadí spuštění middlewarů je nastaveno podle pořadí registrace `IStartupFilter`:

* Několik různých implementací `IStartupFilter` může operovat se stejnými objekty. Pokud je pro Vás důležité pořadí, seřaďte jednotlivé registrace služeb `IStartupFilter` tak, aby odpovídaly pořadí, ve kterém mají být jejich middlewary spuštěny.
* Knihovny mohou přidávat middlewary s jednou nebo více implementacemi rozhraní `IStartupFilter`, které se spustí před nebo po spuštění ostatních middlewarů aplikace zaregistrovaných pomocí rozhraní `IStartupFilter`. Vyvolání middlewaru `IStartupFilter` před přidáním middlewaru `IStartupFilter`knihovny:

  * Před přidáním knihovny do kontejneru služby umístěte registraci služby.
  * Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidání konfigurace při spuštění z externího sestavení

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace. Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Spouštěcí třída

Aplikace ASP.NET Core používají třídu `Startup`, která je konvenčně pojmenována `Startup`. Třída `Startup`:

* Volitelně obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>, která slouží pro konfiguraci *služeb* aplikace. Služba je znovupoužitelná komponenta, která poskytuje funkčnost aplikace. Služby jsou *registrovány* v `ConfigureServices` a spotřebovány v rámci aplikace přes [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> pro vytvoření kanálu pro zpracování požadavků aplikace.

`ConfigureServices` a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

Třída `Startup` je určena při sestavení [hostitele](xref:fundamentals/index#host) aplikace. Třída `Startup` je obvykle určena voláním metody [WebHostBuilderExtensions. UseStartup\<TStartup >](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) v Tvůrci hostitele:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

Hostitel poskytuje služby, které jsou k dispozici konstruktoru třídy `Startup`. Aplikace přidává další služby prostřednictvím metody `ConfigureServices`. Služby hostitele i aplikace jsou pak k dispozici v metodě `Configure` a v celé aplikaci.

Ve třídě `Startup` se běžně používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pro konfiguraci služby pomocí prostředí.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> pro načtení konfigurace.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> pro vytvoření protokolovacího nástroje v `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Většina služeb není k dispozici, dokud není volána metoda `Configure`.

### <a name="multiple-startup"></a>Vícenásobné spuštění

Pokud aplikace definuje samostatnou třídu `Startup` pro různá prostředí (například `StartupDevelopment`), odpovídající třída `Startup` je vybrána v době běhu. Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna. Pokud aplikace běží ve vývojovém prostředí a obsahuje třídu `Startup` i třídu `StartupDevelopment`, použije se třída `StartupDevelopment` . Další informace naleznete v tématu [Používání více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) . Informace o zpracování chyb během spuštění naleznete v tématu [Zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:

* Volitelné.
* Je volána hostitelem před voláním metody `Configure` pro konfiguraci služeb aplikace.
* metoda, ve které jsou [možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Hostitel může nakonfigurovat některé služby před voláním metody `Startup`. Další informace najdete v tématu [Hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují rozsáhlou konfiguraci, existují rozšiřující metody `Add{Service}` nad <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. **Přidejte**například DbContext, **přidejte**DefaultIdentity, **přidejte**EntityFrameworkStores a **přidejte**RazorPages:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Po přidání služeb do kontejneru jsou tyto služby k dispozici v celé aplikaci a v rámci metody `Configure`. Služby jsou řešeny prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

Vizte [SetCompatibilityVersion](xref:mvc/compatibility-version) pro další informace o `SetCompatibilityVersion`.

## <a name="the-configure-method"></a>Metoda Configure

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se používá k určení toho, jak aplikace reaguje na HTTP požadavky. Kanál požadavků se konfiguruje tak, že přidáte [middlewarové](xref:fundamentals/middleware/index) komponenty do instance <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` je dostupný metodě `Configure`, není však registrován v kontejneru služeb. Hosting vytváří `IApplicationBuilder` a předává jej přímo metodě `Configure`

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:

* [Stránku výjimek pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužné rutiny výjimek](xref:fundamentals/error-handling#exception-handler-page)
* [Zabezpečení striktního HTTP přenosu (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Přesměrování HTTPS](xref:security/enforcing-ssl)
* [Statické soubory](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) a [Razor Pages](xref:razor-pages/index)
* [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Každá rozšiřující metoda `Use` přidá jednu nebo více middlewarových komponent do kanálu zpracování požadavku. <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> například nakonfiguruje [middleware](xref:fundamentals/middleware/index) pro poskytování [statických souborů](xref:fundamentals/static-files).

Každá middlewarová komponenta v kanálu zpracování požadavků zodpovídá za vyvolání další komponenty v kanálu, případně může provést předčasné ukončení řetězce volání.

Další služby, například `IHostingEnvironment` a `ILoggerFactory`nebo cokoli definované v `ConfigureServices`, lze zadat v signatuře metody `Configure`. Tyto služby jsou vloženy, pokud jsou k dispozici.

Další informace o tom, jak používat `IApplicationBuilder` a jaké je pořadí zpracování middlewarů, naleznete v tématu <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurace služeb bez spuštění

Ke konfiguraci služeb a kanálu zpracování požadavků bez použití třídy `Startup` využijte volání usnadňujících metod `ConfigureServices` a `Configure` tvůrce (builderu) hostitele. Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání. Pokud existuje více volání metody `Configure`, využije se poslední volání `Configure`.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Rozšíření třídy Startup pomocí filtrů po spuštění

Použít <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání `Use{Middleware}`. `IStartupFilter` používá ASP.NET Core k přidání výchozích hodnot na začátek kanálu, aniž by bylo nutné, aby autor aplikace explicitně zaregistroval výchozí middleware. `IStartupFilter` umožňuje jinému volání komponenty `Use{Middleware}` jménem autora aplikace.
* Chcete-li vytvořit kanál `Configure`ch metod. [IStartupFilter. Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middleware pro spuštění před nebo po middlewaru přidaném knihovnami.

`IStartupFilter` implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, které obdrží a vrátí `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definuje třídu pro konfiguraci kanálu zpracování požadavků vaší aplikace. Další informace naleznete v tématu [Vytvoření kanálu middlewaru s IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` může do kanálu požadavků přidat jeden nebo více middlewarů. Filtry jsou volány v pořadí, ve kterém byly přidány do kontejneru služeb. Filtry mohou přidávat middleware před nebo po předání řízení dalšímu filtru, tedy připojují se na začátek nebo konec kanálu aplikace.

Následující příklad ukazuje, jak zaregistrovat middleware pomocí `IStartupFilter`. Middleware `RequestSetOptionsMiddleware` nastaví hodnoty voleb z parametrů řetězce dotazu (query string parameters):

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware` je nakonfigurovaný ve třídě `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Pokud je k dispozici parametr řetězce dotazu pro `option`, middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.

Pořadí spuštění middlewarů je nastaveno podle pořadí registrace `IStartupFilter`:

* Několik různých implementací `IStartupFilter` může operovat se stejnými objekty. Pokud je pro Vás důležité pořadí, seřaďte jednotlivé registrace služeb `IStartupFilter` tak, aby odpovídaly pořadí, ve kterém mají být jejich middlewary spuštěny.
* Knihovny mohou přidávat middlewary s jednou nebo více implementacemi rozhraní `IStartupFilter`, které se spustí před nebo po spuštění ostatních middlewarů aplikace zaregistrovaných pomocí rozhraní `IStartupFilter`. Vyvolání middlewaru `IStartupFilter` před přidáním middlewaru `IStartupFilter`knihovny:

  * Před přidáním knihovny do kontejneru služby umístěte registraci služby.
  * Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidání konfigurace při spuštění z externího sestavení

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace. Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end