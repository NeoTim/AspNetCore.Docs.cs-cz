---
title: Spuštění aplikace v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak třída Startup v ASP.NET Core konfiguruje služby a kanál požadavků aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/startup
ms.openlocfilehash: e3249df4b7388beeff13fe4b4e0ff481c35725c5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667655"
---
# <a name="app-startup-in-aspnet-core"></a>Spuštění aplikace v ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), a Steve [Smith](https://ardalis.com)

Třída `Startup` konfiguruje služby a kanál požadavků aplikace.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Spouštěcí třída

ASP.NET aplikace Core `Startup` používají třídu, která je pojmenována `Startup` podle konvence. Třída `Startup`:

* Volitelně obsahuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> metodu konfigurace *služeb*aplikace . Služba je opakovaně použitelná součást, která poskytuje funkce aplikace. Služby se `ConfigureServices` *zapisují* a spotřebovávají <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>v celé aplikaci prostřednictvím [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo .
* Obsahuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> metodu k vytvoření kanálu zpracování požadavků aplikace.

`ConfigureServices`a `Configure` jsou volány ASP.NET core runtime při spuštění aplikace:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí ukázka je pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.


Třída `Startup` je určena při spuštění [hostitele](xref:fundamentals/index#host) aplikace. Třída `Startup` je obvykle určena voláním [Metody WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) v tvůrce hostitele:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Hostitel poskytuje služby, které `Startup` jsou k dispozici pro konstruktor třídy. Aplikace přidává další `ConfigureServices`služby přes . Služby hostitele i aplikace `Configure` jsou k dispozici v aplikaci a v celé aplikaci.

Při použití `Startup` [obecného hostitele](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Většina služeb nejsou `Configure` k dispozici, dokud je volána metoda.

### <a name="multiple-startup"></a>Vícenásobné spuštění

Když aplikace definuje `Startup` samostatné třídy pro různá `StartupDevelopment`prostředí `Startup` (například), je za běhu vybrána příslušná třída. Třída, jejíž přípona názvu odpovídá aktuálnímu prostředí, je upřednostněna. Pokud je aplikace spuštěna ve vývojovém `Startup` prostředí `StartupDevelopment` a zahrnuje `StartupDevelopment` třídu i třídu, použije se třída. Další informace naleznete v tématu [Použití více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli naleznete v tématu [Hostitel.](xref:fundamentals/index#host) Informace o zpracování chyb při spuštění naleznete v tématu [Zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:

* Nepovinný parametr.
* Volána hostitelem `Configure` před metodou konfigurace služeb aplikace.
* Kde jsou [možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Hostitel může nakonfigurovat `Startup` některé služby před voláním metod. Další informace naleznete [v tématu Hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují `Add{Service}` podstatné nastavení, existují metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Například **Přidat**DbContext, **Přidat**DefaultIdentity, **Přidat**EntityFrameworkStores a **Přidat**RazorPages:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

Přidání služeb do kontejneru služby je zpřístupní v rámci aplikace a v metodě. `Configure` Služby jsou [vyřešeny](xref:fundamentals/dependency-injection) prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>vkládání závislostí nebo z .

## <a name="the-configure-method"></a>Metoda Configure

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se používá k určení, jak aplikace reaguje na požadavky HTTP. Kanál požadavků je nakonfigurován přidáním [middleware](xref:fundamentals/middleware/index) komponent do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance. `IApplicationBuilder`je k `Configure` dispozici pro metodu, ale není registrována v kontejneru služby. Hosting vytvoří `IApplicationBuilder` a předá `Configure`přímo do .

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou:

* [Stránka výjimky pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužná rutina výjimk](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Přesměrování https](xref:security/enforcing-ssl)
* [Statické soubory](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) a [Razor Stránky](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí ukázka je pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.

Každá `Use` metoda rozšíření přidá jednu nebo více middleware komponent do kanálu požadavku. Například <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> konfiguruje [middleware](xref:fundamentals/middleware/index) tak, aby sloužil [statickým souborům](xref:fundamentals/static-files).

Každá součást middlewaru v kanálu požadavků je zodpovědná za vyvolání další součásti v potrubí nebo zkratování řetězu, pokud je to vhodné.

V podpisu `IWebHostEnvironment`metody `ILoggerFactory`lze zadat `ConfigureServices`další služby, `Configure` například , , nebo cokoli definovaného v písmenu a). Tyto služby jsou injekčně, pokud jsou k dispozici.

Další informace o použití `IApplicationBuilder` a pořadí zpracování middleware <xref:fundamentals/middleware/index>naleznete v tématu .

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurace služeb bez spuštění

Chcete-li konfigurovat služby a `Startup` kanál zpracování `ConfigureServices` `Configure` požadavků bez použití třídy, metody volání a pohodlí na tvůrce hostitele. Více volání `ConfigureServices` k sobě navzájem. Pokud `Configure` existuje více volání `Configure` metody, použije se poslední volání.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Rozšíření spuštění pomocí spouštěcích filtrů

Použití <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání . `Use{Middleware}` `IStartupFilter`používá ASP.NET Core přidat výchozí hodnoty na začátek kanálu, aniž by bylo třeba, aby autor aplikace explicitně zaregistrovat výchozí middleware. `IStartupFilter`umožňuje volání jiné `Use{Middleware}` součásti jménem autora aplikace.
* Chcete-li vytvořit `Configure` kanál metod. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middleware spustit před nebo po middleware přidané knihovny.

`IStartupFilter`implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, který obdrží `Action<IApplicationBuilder>`a vrátí . Definuje <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> třídu pro konfiguraci kanálu požadavků aplikace. Další informace naleznete [v tématu Vytvoření kanálu middlewaru pomocí aplikace IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` může přidat jeden nebo více middlewares v kanálu požadavku. Filtry jsou vyvolány v pořadí, ve které byly přidány do kontejneru služby. Filtry mohou přidat middleware před nebo po předání ovládacího prvku do dalšího filtru, a proto se připojí k začátku nebo na konci kanálu aplikace.

Následující příklad ukazuje, jak zaregistrovat `IStartupFilter`middleware s . Middleware `RequestSetOptionsMiddleware` nastaví hodnotu voleb z parametru řetězce dotazu:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

Je `RequestSetOptionsMiddleware` konfigurován ve `RequestSetOptionsStartupFilter` třídě:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Je `IStartupFilter` registrován v kontejneru <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>služby v .

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Pokud je k `option` dispozici parametr řetězce dotazu pro, middleware zpracuje přiřazení hodnoty před ASP.NET middleware Core vykreslí odpověď.

Pořadí provádění middlewaru je `IStartupFilter` nastaveno pořadím registrací:

* Více `IStartupFilter` implementací může pracovat se stejnými objekty. Pokud je důležité objednávat, objednejte registrace jejich `IStartupFilter` služeb tak, aby odpovídaly pořadí, ve které by měly být spuštěny jejich middlewares.
* Knihovny mohou přidávat middleware `IStartupFilter` s jednou nebo více implementacemi, které `IStartupFilter`běží před nebo po jiné aplikace middleware registrované u . Chcete-li `IStartupFilter` vyvolat middleware před middleware přidané `IStartupFilter`knihovny :

  * Umístěte registraci služby před přidáním knihovny do kontejneru služby.
  * Chcete-li vyvolat později, umístěte registraci služby po přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidání konfigurace při spuštění z externího sestavení

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidávání vylepšení do aplikace při spuštění z externího `Startup` sestavení mimo třídu aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Spouštěcí třída

ASP.NET aplikace Core `Startup` používají třídu, která je pojmenována `Startup` podle konvence. Třída `Startup`:

* Volitelně obsahuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> metodu konfigurace *služeb*aplikace . Služba je opakovaně použitelná součást, která poskytuje funkce aplikace. Služby se `ConfigureServices` *zapisují* a spotřebovávají <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>v celé aplikaci prostřednictvím [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo .
* Obsahuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> metodu k vytvoření kanálu zpracování požadavků aplikace.

`ConfigureServices`a `Configure` jsou volány ASP.NET core runtime při spuštění aplikace:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

Třída `Startup` je určena při spuštění [hostitele](xref:fundamentals/index#host) aplikace. Třída `Startup` je obvykle určena voláním [Metody WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) v tvůrce hostitele:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

Hostitel poskytuje služby, které `Startup` jsou k dispozici pro konstruktor třídy. Aplikace přidává další `ConfigureServices`služby přes . Služby hostitele i aplikace jsou `Configure` pak dostupné v aplikaci a v celé aplikaci.

Běžné použití [vkládání závislostí](xref:fundamentals/dependency-injection) `Startup` do třídy je inject:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>pro konfiguraci služeb podle prostředí.
* <xref:Microsoft.Extensions.Configuration.IConfiguration>pro čtení konfigurace.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory>chcete-li vytvořit `Startup.ConfigureServices`protokolovací nástroj v .

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Většina služeb nejsou `Configure` k dispozici, dokud je volána metoda.

### <a name="multiple-startup"></a>Vícenásobné spuštění

Když aplikace definuje `Startup` samostatné třídy pro různá `StartupDevelopment`prostředí `Startup` (například), je za běhu vybrána příslušná třída. Třída, jejíž přípona názvu odpovídá aktuálnímu prostředí, je upřednostněna. Pokud je aplikace spuštěna ve vývojovém `Startup` prostředí `StartupDevelopment` a zahrnuje `StartupDevelopment` třídu i třídu, použije se třída. Další informace naleznete v tématu [Použití více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli naleznete v tématu [Hostitel.](xref:fundamentals/index#host) Informace o zpracování chyb při spuštění naleznete v tématu [Zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:

* Nepovinný parametr.
* Volána hostitelem `Configure` před metodou konfigurace služeb aplikace.
* Kde jsou [možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Hostitel může nakonfigurovat `Startup` některé služby před voláním metod. Další informace naleznete [v tématu Hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují `Add{Service}` podstatné nastavení, existují metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Například **Přidat**DbContext, **Přidat**DefaultIdentity, **Přidat**EntityFrameworkStores a **Přidat**RazorPages:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Přidání služeb do kontejneru služby je zpřístupní v rámci aplikace a v metodě. `Configure` Služby jsou [vyřešeny](xref:fundamentals/dependency-injection) prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>vkládání závislostí nebo z .

Další informace o tématu `SetCompatibilityVersion` [SetCompatibilityVersion](xref:mvc/compatibility-version) naleznete v tématu .

## <a name="the-configure-method"></a>Metoda Configure

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se používá k určení, jak aplikace reaguje na požadavky HTTP. Kanál požadavků je nakonfigurován přidáním [middleware](xref:fundamentals/middleware/index) komponent do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance. `IApplicationBuilder`je k `Configure` dispozici pro metodu, ale není registrována v kontejneru služby. Hosting vytvoří `IApplicationBuilder` a předá `Configure`přímo do .

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou:

* [Stránka výjimky pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužná rutina výjimk](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Přesměrování https](xref:security/enforcing-ssl)
* [Statické soubory](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) a [Razor Stránky](xref:razor-pages/index)
* [Obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Každá `Use` metoda rozšíření přidá jednu nebo více middleware komponent do kanálu požadavku. Například <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> konfiguruje [middleware](xref:fundamentals/middleware/index) tak, aby sloužil [statickým souborům](xref:fundamentals/static-files).

Každá součást middlewaru v kanálu požadavků je zodpovědná za vyvolání další součásti v potrubí nebo zkratování řetězu, pokud je to vhodné.

Další služby, `IHostingEnvironment` `ILoggerFactory`například a `ConfigureServices`, nebo cokoli `Configure` definovaného v písmenu a , lze zadat v podpisu metody. Tyto služby jsou injekčně, pokud jsou k dispozici.

Další informace o použití `IApplicationBuilder` a pořadí zpracování middleware <xref:fundamentals/middleware/index>naleznete v tématu .

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurace služeb bez spuštění

Chcete-li konfigurovat služby a `Startup` kanál zpracování `ConfigureServices` `Configure` požadavků bez použití třídy, metody volání a pohodlí na tvůrce hostitele. Více volání `ConfigureServices` k sobě navzájem. Pokud `Configure` existuje více volání `Configure` metody, použije se poslední volání.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Rozšíření spuštění pomocí spouštěcích filtrů

Použití <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání . `Use{Middleware}` `IStartupFilter`používá ASP.NET Core přidat výchozí hodnoty na začátek kanálu, aniž by bylo třeba, aby autor aplikace explicitně zaregistrovat výchozí middleware. `IStartupFilter`umožňuje volání jiné `Use{Middleware}` součásti jménem autora aplikace.
* Chcete-li vytvořit `Configure` kanál metod. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middleware spustit před nebo po middleware přidané knihovny.

`IStartupFilter`implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, který obdrží `Action<IApplicationBuilder>`a vrátí . Definuje <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> třídu pro konfiguraci kanálu požadavků aplikace. Další informace naleznete [v tématu Vytvoření kanálu middlewaru pomocí aplikace IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` může přidat jeden nebo více middlewares v kanálu požadavku. Filtry jsou vyvolány v pořadí, ve které byly přidány do kontejneru služby. Filtry mohou přidat middleware před nebo po předání ovládacího prvku do dalšího filtru, a proto se připojí k začátku nebo na konci kanálu aplikace.

Následující příklad ukazuje, jak zaregistrovat `IStartupFilter`middleware s . Middleware `RequestSetOptionsMiddleware` nastaví hodnotu voleb z parametru řetězce dotazu:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

Je `RequestSetOptionsMiddleware` konfigurován ve `RequestSetOptionsStartupFilter` třídě:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Je `IStartupFilter` registrován v kontejneru <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>služby v .

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Pokud je k `option` dispozici parametr řetězce dotazu pro, middleware zpracuje přiřazení hodnoty před ASP.NET middleware Core vykreslí odpověď.

Pořadí provádění middlewaru je `IStartupFilter` nastaveno pořadím registrací:

* Více `IStartupFilter` implementací může pracovat se stejnými objekty. Pokud je důležité objednávat, objednejte registrace jejich `IStartupFilter` služeb tak, aby odpovídaly pořadí, ve které by měly být spuštěny jejich middlewares.
* Knihovny mohou přidávat middleware `IStartupFilter` s jednou nebo více implementacemi, které `IStartupFilter`běží před nebo po jiné aplikace middleware registrované u . Chcete-li `IStartupFilter` vyvolat middleware před middleware přidané `IStartupFilter`knihovny :

  * Umístěte registraci služby před přidáním knihovny do kontejneru služby.
  * Chcete-li vyvolat později, umístěte registraci služby po přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidání konfigurace při spuštění z externího sestavení

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidávání vylepšení do aplikace při spuštění z externího `Startup` sestavení mimo třídu aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end