---
title: Spuštění aplikace v ASP.NET Core
author: tdykstra
description: Zjistěte, jak třídu pro spuštění v ASP.NET Core konfiguruje služby a kanál žádosti o aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/17/2019
uid: fundamentals/startup
ms.openlocfilehash: 7e1741d2bed15f36a967713a2f9bd0d93801c8d0
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874941"
---
# <a name="app-startup-in-aspnet-core"></a>Spuštění aplikace v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex), a [Steve Smith](https://ardalis.com)

Třída `Startup` konfiguruje služby a kanál zpracování požadavků aplikace.

## <a name="the-startup-class"></a>Třída Startup

Aplikace ASP.NET Core používají třídu `Startup`, která je konvenčně pojmenována `Startup`. Třída `Startup`:

* Volitelně obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>, která slouží pro konfiguraci *služeb* aplikace. Služba je znovupoužitelná komponenta, která poskytuje funkčnost aplikace. Služby jsou nakonfigurovány &mdash; neboli takzvaně *zaregistrovány* &mdash; v metodě `ConfigureServices` a využívány napříč aplikací skrze [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> pro vytvoření kanálu pro zpracování požadavků aplikace.

`ConfigureServices` a `Configure` jsou volány modulem runtime ASP.NET Core, při spuštění aplikace:

[!code-csharp[](startup/sample_snapshot/Startup1.cs?highlight=4,10)]

Třída `Startup` je v aplikaci specifikována při vytváření [hostitele](xref:fundamentals/index#host). Hostitel aplikace je vytvořen při volání metody `Build` na tvůrci (builderu) hostitele ve třídě `Program`. Třída `Startup` je obvykle určena voláním metody [WebHostBuilderExtensions.UseStartup\<TStartup >](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) na tvůrci (builderu) hostitele:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=10)]

Hostitel poskytuje služby, které jsou k dispozici konstruktoru třídy `Startup`. Aplikace přidává další služby prostřednictvím metody `ConfigureServices`. Služby hostitele i aplikace jsou pak k dispozici v metodě `Configure` a v celé aplikaci.

Ve třídě `Startup` se běžně používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pro konfiguraci služby pomocí prostředí.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> pro načtení konfigurace.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> pro vytvoření protokolovacího nástroje v `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Alternativou ke vložení `IHostingEnvironment` je použití přístupu založeného na konvencích. Pokud aplikace definuje samostatnou třídu `Startup` pro různá prostředí (například `StartupDevelopment`), odpovídající třída `Startup` je vybrána v době běhu. Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna. Pokud aplikace běží ve vývojovém prostředí a obsahuje třídu `Startup` i třídu `StartupDevelopment`, použije se třída `StartupDevelopment` . Další informace naleznete v tématu [Používání více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Další informace o hostiteli naleznete v tématu [Hostitel](xref:fundamentals/index#host). Informace o zpracování chyb během spuštění naleznete v tématu [Zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Metoda ConfigureServices

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:

* Volitelná.
* Je volána hostitelem před voláním metody `Configure` pro konfiguraci služeb aplikace.
* Metoda, ve které jsou [možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.

Typickým postupem je volání všech metod `Add{Service}` a následně volání všech metod `services.Configure{Service}`. Viz například [konfigurace Identity služby](xref:security/authentication/identity#pw).

Hostitel může nakonfigurovat některé služby před voláním metody `Startup`. Další informace najdete v tématu [Hostitel](xref:fundamentals/index#host).

Pro funkce, které vyžadují rozsáhlou konfiguraci, existují rozšiřující metody `Add{Service}` nad <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Typická aplikace ASP.NET Core registruje služby pro Entity Framework, Identity a MVC:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Po přidání služeb do kontejneru jsou tyto služby k dispozici v celé aplikaci a v rámci metody `Configure`. Služby jsou řešeny prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

Vizte [SetCompatibilityVersion](xref:mvc/compatibility-version) pro další informace o `SetCompatibilityVersion`.

## <a name="the-configure-method"></a>Metoda Configure

Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se používá k určení toho, jak aplikace reaguje na HTTP požadavky. Kanál požadavků se konfiguruje tak, že přidáte [middlewarové](xref:fundamentals/middleware/index) komponenty do instance <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` je dostupný metodě `Configure`, není však registrován v kontejneru služeb. Hosting vytváří `IApplicationBuilder` a předává jej přímo metodě `Configure`.

[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:

* [Stránku výjimek pro vývojáře](xref:fundamentals/error-handling#developer-exception-page)
* [Obslužné rutiny výjimek](xref:fundamentals/error-handling#exception-handler-page)
* [Zabezpečení striktního HTTP přenosu (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Přesměrování HTTPS](xref:security/enforcing-ssl)
* [Statické soubory](xref:fundamentals/static-files)
* [General Data Protection Regulation (GDPR)](xref:security/gdpr)
* ASP.NET Core [MVC](xref:mvc/overview) a [stránky Razor](xref:razor-pages/index)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Každá rozšiřující metoda `Use` přidá jednu nebo více middlewarových komponent do kanálu zpracování požadavku. Například rozšiřující metoda `UseMvc` přidává [Směrovací middleware](xref:fundamentals/routing) do kanálu zpracování požadavků a nakonfiguruje [MVC](xref:mvc/overview) jako výchozí obslužnou rutinu.

Každá middlewarová komponenta v kanálu zpracování požadavků zodpovídá za vyvolání další komponenty v kanálu, případně může provést předčasné ukončení řetězce volání. Pokud nedojde k předčasnému ukončení řetězce volání během zpracování požadavku, může libovolný middleware požadavek zpracovat ještě podruhé, než je odeslán klientovi.

Dodatečné služby, jako jsou například `IHostingEnvironment` a `ILoggerFactory`, je také možné určit v signatuře metody `Configure`. Jsou-li specifikovány, dodatečné služby se vloží za předpokladu jejich dostupnosti.

Další informace o tom, jak používat `IApplicationBuilder` a jaké je pořadí zpracování middlewarů, naleznete v tématu <xref:fundamentals/middleware/index>.

## <a name="convenience-methods"></a>Usnadňující metody

Ke konfiguraci služeb a kanálu zpracování požadavků bez použití třídy `Startup` využijte volání usnadňujících metod `ConfigureServices` a `Configure` tvůrce (builderu) hostitele. Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání. Pokud existuje více volání metody `Configure`, využije se poslední volání `Configure`.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Rozšíření třídy Startup pomocí filtrů po spuštění

Využijte <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> pro konfiguraci middlewaru na začátku a na konci [konfigurační](#the-configure-method) middlewarového kanálu vaší aplikace. `IStartupFilter` je užitečný k zajištění toho, aby byl daný middleware spuštěn před nebo po spuštění middlewarů přidaných knihovnami na začátku nebo konci kanálu zpracování požadavků aplikace.

`IStartupFilter` implementuje jedinou metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, která přijímá a vrací `Action<IApplicationBuilder>`. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definuje třídu pro konfiguraci kanálu zpracování požadavků vaší aplikace. Další informace naleznete v tématu [Vytvoření kanálu middlewaru s IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Každý `IStartupFilter` implementuje jeden nebo více middlewarů v kanálu zpracování požadavků. Filtry jsou volány v pořadí, ve kterém byly přidány do kontejneru služeb. Filtry mohou přidávat middleware před nebo po předání řízení dalšímu filtru, tedy připojují se na začátek nebo konec kanálu aplikace.

Následující příklad ukazuje, jak se zaregistrovat middleware s `IStartupFilter`.

Middleware `RequestSetOptionsMiddleware` nastaví hodnoty voleb z parametrů řetězce dotazu (query string parameters):

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

`RequestSetOptionsMiddleware` je nakonfigurovaný ve třídě `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

`IStartupFilter` je zaregistrovaný v kontejneru služeb  v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> a rozšiřuje `Startup` mimo třídu `Startup`:

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Pokud je určena hodnota parametru řetězce dotazu `option`, middleware zpracuje danou hodnotu předtím, než MVC middleware vykreslí odpověď:

![Okno prohlížeče zobrazující vykreslenou stránku Index. Hodnota Option je vykreslena jako 'From Middleware' na základě parametru řetězce dotazu 'option', jehož hodnota je nastavena na 'From Middleware'.](startup/_static/index.png)

Pořadí spuštění middlewarů je nastaveno podle pořadí registrace `IStartupFilter`:

* Několik různých implementací `IStartupFilter` může operovat se stejnými objekty. Pokud je pro Vás důležité pořadí, seřaďte jednotlivé registrace služeb `IStartupFilter` tak, aby odpovídaly pořadí, ve kterém mají být jejich middlewary spuštěny.
* Knihovny mohou přidávat middlewary s jednou nebo více implementacemi rozhraní `IStartupFilter`, které se spustí před nebo po spuštění ostatních middlewarů aplikace zaregistrovaných pomocí rozhraní `IStartupFilter`. Pokud chcete vyvolat middleware rozhraní `IStartupFilter` před  middlewarem přidaným pomocí rozhraní `IStartupFilter` knihovny, umístěte registraci služby před přidání knihovny do kontejneru služeb. Pokud ji chcete vyvolat později, umístěte registraci služby za přidání knihovny.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Přidání konfigurace při spuštění z externího sestavení

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* [Hostitel](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
