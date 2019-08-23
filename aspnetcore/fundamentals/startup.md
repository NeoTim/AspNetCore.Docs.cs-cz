---
title: Spuštění aplikace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
ms.author: riande
ms.custom: mvc
ms.date: 8/7/2019
uid: fundamentals/startup
ms.openlocfilehash: 8866ee9210a91754d8050d0b91ff52c3d3fe0836
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975441"
---
# <a name="app-startup-in-aspnet-core"></a>Spuštění aplikace v ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex)a [Steve Smith](https://ardalis.com)

Třída `Startup` konfiguruje služby a kanál zpracování požadavků aplikace.

## <a name="the-startup-class"></a>Třída Startup

Aplikace ASP.NET Core používají třídu `Startup`, která je konvenčně pojmenována `Startup`. Třída `Startup`:

* Volitelně obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>, která slouží pro konfiguraci *služeb* aplikace. Služba je znovupoužitelná komponenta, která poskytuje funkčnost aplikace. Služby jsou nakonfigurovány &mdash; neboli takzvaně *zaregistrovány* &mdash; v metodě `ConfigureServices` a využívány napříč aplikací skrze [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> pro vytvoření kanálu pro zpracování požadavků aplikace.

`ConfigureServices`a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

Třída je určena při sestavení hostitele aplikace. [](xref:fundamentals/index#host) `Startup` Třída je obvykle určena [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) voláním metody v Tvůrci hostitele: `Startup`

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Hostitel poskytuje služby, které jsou k dispozici konstruktoru třídy `Startup`. Aplikace přidává další služby prostřednictvím metody `ConfigureServices`. Hostitelská i Aplikační služba jsou k dispozici `Configure` v i v celé aplikaci.

Při použití `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>lze do konstruktoru vložit pouze následující typy služeb:

* `IWebHostEnvironment`
* `IHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Většina služeb není k dispozici, `Configure` dokud není volána metoda.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Hostitel poskytuje služby, které jsou k dispozici konstruktoru třídy `Startup`. Aplikace přidává další služby prostřednictvím metody `ConfigureServices`. Služby hostitele i aplikace jsou pak k dispozici v metodě `Configure` a v celé aplikaci.

Ve třídě `Startup` se běžně používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pro konfiguraci služby pomocí prostředí.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> pro načtení konfigurace.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> pro vytvoření protokolovacího nástroje v `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

::: moniker-end
Alternativou ke vložení `IWebHostEnvironment` je použití přístupu založeného na konvencích.
::: moniker range=">= aspnetcore-3.0"

::: moniker-end

::: moniker range="< aspnetcore-3.0"
Alternativou ke vložení `IHostingEnvironment` je použití přístupu založeného na konvencích.
::: moniker-end

Pokud aplikace definuje samostatnou třídu `Startup` pro různá prostředí (například `StartupDevelopment`), odpovídající třída `Startup` je vybrána v době běhu. Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna. Pokud aplikace běží ve vývojovém prostředí a obsahuje třídu `Startup` i třídu `StartupDevelopment`, použije se třída `StartupDevelopment` . Další informace naleznete v tématu [Používání více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) . Informace o zpracování chyb během spuštění naleznete v tématu [Zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:

* Volitelná.
* Je volána hostitelem před voláním metody `Configure` pro konfiguraci služeb aplikace.
* metoda, ve které jsou [možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Hostitel může nakonfigurovat některé služby před voláním metody `Startup`. Další informace najdete v tématu [Hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují rozsáhlou konfiguraci, existují rozšiřující metody `Add{Service}` nad <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. **Přidejte**například DbContext, **přidejte**DefaultIdentity, **přidejte**EntityFrameworkStores a **přidejte**RazorPages:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

Po přidání služeb do kontejneru jsou tyto služby k dispozici v celé aplikaci a v rámci metody `Configure`. Služby jsou řešeny prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

::: moniker range="< aspnetcore-3.0"

Vizte [SetCompatibilityVersion](xref:mvc/compatibility-version) pro další informace o `SetCompatibilityVersion`.

::: moniker-end

## <a name="the-configure-method"></a>Metoda Configure

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se používá k určení toho, jak aplikace reaguje na HTTP požadavky. Kanál požadavků se konfiguruje tak, že přidáte [middlewarové](xref:fundamentals/middleware/index) komponenty do instance <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` je dostupný metodě `Configure`, není však registrován v kontejneru služeb. Hosting vytváří `IApplicationBuilder` a předává jej přímo metodě `Configure`.

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:

* [Stránku výjimek pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužné rutiny výjimek](xref:fundamentals/error-handling#exception-handler-page)
* [Zabezpečení striktního HTTP přenosu (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
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

Každá rozšiřující metoda `Use` přidá jednu nebo více middlewarových komponent do kanálu zpracování požadavku. Například nakonfiguruje middleware na poskytování [statických souborů](xref:fundamentals/static-files). [](xref:fundamentals/middleware/index) <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>

Každá middlewarová komponenta v kanálu zpracování požadavků zodpovídá za vyvolání další komponenty v kanálu, případně může provést předčasné ukončení řetězce volání.

::: moniker range=">= aspnetcore-3.0"

Další `IWebHostEnvironment`služby, jako například, `ILoggerFactory`nebo cokoli definované v `ConfigureServices`, lze zadat v `Configure` signatuře metody. Tyto služby jsou vloženy, pokud jsou k dispozici.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Další služby, `IHostingEnvironment` například a `ILoggerFactory`, nebo cokoli definované v `ConfigureServices`, lze zadat v `Configure` signatuře metody. Tyto služby jsou vloženy, pokud jsou k dispozici.

::: moniker-end

Další informace o tom, jak používat `IApplicationBuilder` a jaké je pořadí zpracování middlewarů, naleznete v tématu <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurace služeb bez spuštění

Ke konfiguraci služeb a kanálu zpracování požadavků bez použití třídy `Startup` využijte volání usnadňujících metod `ConfigureServices` a `Configure` tvůrce (builderu) hostitele. Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání. Pokud existuje více volání metody `Configure`, využije se poslední volání `Configure`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a>Rozšíření třídy Startup pomocí filtrů po spuštění

Využijte <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> pro konfiguraci middlewaru na začátku a na konci [konfigurační](#the-configure-method) middlewarového kanálu vaší aplikace. `IStartupFilter`slouží k vytvoření kanálu `Configure` metod. [IStartupFilter. Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middleware pro spuštění před nebo po middlewaru přidaném knihovnami.

`IStartupFilter`implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, které přijímá a `Action<IApplicationBuilder>`vrací. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definuje třídu pro konfiguraci kanálu zpracování požadavků vaší aplikace. Další informace naleznete v tématu [Vytvoření kanálu middlewaru s IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` z nich může do kanálu požadavků přidat jeden nebo více middlewarů. Filtry jsou volány v pořadí, ve kterém byly přidány do kontejneru služeb. Filtry mohou přidávat middleware před nebo po předání řízení dalšímu filtru, tedy připojují se na začátek nebo konec kanálu aplikace.

Následující příklad ukazuje, jak registrovat middleware pomocí `IStartupFilter`. Middleware `RequestSetOptionsMiddleware` nastaví hodnoty voleb z parametrů řetězce dotazu (query string parameters):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

`RequestSetOptionsMiddleware` je nakonfigurovaný ve třídě `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware` je nakonfigurovaný ve třídě `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

Je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>. `IStartupFilter`

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

Pokud je k dispozici parametr `option` řetězce dotazu pro, middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.

Pořadí spuštění middlewarů je nastaveno podle pořadí registrace `IStartupFilter`:

* Několik různých implementací `IStartupFilter` může operovat se stejnými objekty. Pokud je pro Vás důležité pořadí, seřaďte jednotlivé registrace služeb `IStartupFilter` tak, aby odpovídaly pořadí, ve kterém mají být jejich middlewary spuštěny.
* Knihovny mohou přidávat middlewary s jednou nebo více implementacemi `IStartupFilter`, které se spuští před nebo po spuštění ostatních middlewarů aplikace zaregistrovaných pomocí `IStartupFilter`. Vyvolání `IStartupFilter` middlewaru před middlewarem přidaným `IStartupFilter`knihovnou:

  * Před přidáním knihovny do kontejneru služby umístěte registraci služby.
  * Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidání konfigurace při spuštění z externího sestavení

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
