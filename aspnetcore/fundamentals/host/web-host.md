---
title: ASP.NET základní webový hostitel
author: rick-anderson
description: Další informace o webovém hostiteli v ASP.NET Core, který je zodpovědný za spuštění aplikace a správu životnosti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: e02d6efcb3aec1329469b8654e66ba845870421a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666710"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="4700f-103">ASP.NET základní webový hostitel</span><span class="sxs-lookup"><span data-stu-id="4700f-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="4700f-104">ASP.NET aplikace Core nakonfigurují a spouštějí *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="4700f-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="4700f-105">Hostitel je zodpovědný za spuštění aplikace a správu životnosti.</span><span class="sxs-lookup"><span data-stu-id="4700f-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4700f-106">Hostitel minimálně konfiguruje server a kanál zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="4700f-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="4700f-107">Hostitel může také nastavit protokolování, vkládání závislostí a konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="4700f-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4700f-108">Tento článek popisuje webový hostitel, který zůstává k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="4700f-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="4700f-109">[Obecný hostitel](xref:fundamentals/host/generic-host) se doporučuje pro všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="4700f-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4700f-110">Tento článek popisuje webhosting, který je určen pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="4700f-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="4700f-111">Pro jiné druhy aplikací použijte [obecný hostitel](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="4700f-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="4700f-112">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="4700f-112">Set up a host</span></span>

<span data-ttu-id="4700f-113">Vytvořte hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="4700f-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="4700f-114">To se obvykle provádí v vstupním bodě `Main` aplikace, metoda.</span><span class="sxs-lookup"><span data-stu-id="4700f-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="4700f-115">V šablonách projektu `Main` se nachází v *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4700f-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="4700f-116">Typická aplikace volá [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ke spuštění nastavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="4700f-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="4700f-117">Kód, který `CreateDefaultBuilder` volá je `CreateWebHostBuilder`v metodě s názvem `Main` , `Run` která jej odděluje od kódu v tomto volání objektu tvůrce.</span><span class="sxs-lookup"><span data-stu-id="4700f-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="4700f-118">Toto oddělení je vyžadováno, pokud používáte [nástroje Core framework .](/ef/core/miscellaneous/cli/)</span><span class="sxs-lookup"><span data-stu-id="4700f-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="4700f-119">Nástroje očekávají, že `CreateWebHostBuilder` najdou metodu, kterou mohou volat v době návrhu ke konfiguraci hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="4700f-120">Alternativou je `IDesignTimeDbContextFactory`implementace .</span><span class="sxs-lookup"><span data-stu-id="4700f-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="4700f-121">Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="4700f-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="4700f-122">`CreateDefaultBuilder` provede následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="4700f-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="4700f-123">Konfiguruje [kestrelserver](xref:fundamentals/servers/kestrel) jako webový server pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="4700f-124">Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .</span><span class="sxs-lookup"><span data-stu-id="4700f-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4700f-125">Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na cestu vrácenou [directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="4700f-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="4700f-126">Načte [konfiguraci hostitele](#host-configuration-values) z:</span><span class="sxs-lookup"><span data-stu-id="4700f-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="4700f-127">Proměnné prostředí s `ASPNETCORE_` předponou `ASPNETCORE_ENVIRONMENT`(například ).</span><span class="sxs-lookup"><span data-stu-id="4700f-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="4700f-128">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4700f-128">Command-line arguments.</span></span>
* <span data-ttu-id="4700f-129">Načte konfiguraci aplikace v následujícím pořadí od:</span><span class="sxs-lookup"><span data-stu-id="4700f-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="4700f-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4700f-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="4700f-131">*nastavení aplikace. {Prostředí}.json*.</span><span class="sxs-lookup"><span data-stu-id="4700f-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4700f-132">[Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí pomocí sestavení položky.</span><span class="sxs-lookup"><span data-stu-id="4700f-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="4700f-133">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4700f-133">Environment variables.</span></span>
  * <span data-ttu-id="4700f-134">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4700f-134">Command-line arguments.</span></span>
* <span data-ttu-id="4700f-135">Konfiguruje [protokolování](xref:fundamentals/logging/index) pro výstup konzoly a ladění.</span><span class="sxs-lookup"><span data-stu-id="4700f-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="4700f-136">Protokolování zahrnuje pravidla [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) zadaná v části Konfigurace protokolování *souboru appsettings.json* nebo *appsettings.{ Prostředí}.json.*</span><span class="sxs-lookup"><span data-stu-id="4700f-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="4700f-137">Při spuštění za službou IIS `CreateDefaultBuilder` pomocí [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module)umožňuje integraci [služby IIS](xref:host-and-deploy/iis/index), která konfiguruje základní adresu a port aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="4700f-138">Integrace služby IIS také konfiguruje aplikaci tak, aby [zaznamenávala chyby při spuštění](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="4700f-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="4700f-139">Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="4700f-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="4700f-140">Nastaví [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` v případě, že prostředí aplikace je vývoj.</span><span class="sxs-lookup"><span data-stu-id="4700f-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="4700f-141">Další informace naleznete v [tématu Ověření oboru](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="4700f-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="4700f-142">Konfigurace definovaná `CreateDefaultBuilder` hodnotou může být přepsána a rozšířena [pomocí ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a dalších metod a rozšiřujících metod [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="4700f-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="4700f-143">Několik příkladů následuje:</span><span class="sxs-lookup"><span data-stu-id="4700f-143">A few examples follow:</span></span>

* <span data-ttu-id="4700f-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se používá `IConfiguration` k určení další pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4700f-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="4700f-145">Následující `ConfigureAppConfiguration` volání přidá delegáta, aby zahrnul konfiguraci aplikace do souboru *appsettings.xml.*</span><span class="sxs-lookup"><span data-stu-id="4700f-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="4700f-146">`ConfigureAppConfiguration`může být volána vícekrát.</span><span class="sxs-lookup"><span data-stu-id="4700f-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="4700f-147">Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí).</span><span class="sxs-lookup"><span data-stu-id="4700f-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="4700f-148">Viz část Hodnoty [konfigurace hostitele.](#host-configuration-values)</span><span class="sxs-lookup"><span data-stu-id="4700f-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="4700f-149">Následující `ConfigureLogging` volání přidá delegáta ke konfiguraci minimální úrovně protokolování[(SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="4700f-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="4700f-150">Toto nastavení přepíše nastavení v *nastavení aplikace. Development.json* `LogLevel.Debug`( ) a *nastavení aplikací. Production.json* `LogLevel.Error`( ) `CreateDefaultBuilder`nakonfigurovaný .</span><span class="sxs-lookup"><span data-stu-id="4700f-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4700f-151">`ConfigureLogging`může být volána vícekrát.</span><span class="sxs-lookup"><span data-stu-id="4700f-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="4700f-152">Následující volání `ConfigureKestrel` přepíše výchozí [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtů vytvořených při `CreateDefaultBuilder`konfiguraci Kestrel pomocí :</span><span class="sxs-lookup"><span data-stu-id="4700f-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="4700f-153">Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepíše výchozí [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtů vytvořených při `CreateDefaultBuilder`kestrel byl nakonfigurován :</span><span class="sxs-lookup"><span data-stu-id="4700f-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="4700f-154">[Kořenový adresář obsahu](xref:fundamentals/index#content-root) určuje, kde hostitel hledá soubory obsahu, například soubory zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="4700f-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="4700f-155">Při spuštění aplikace z kořenové složky projektu se jako kořenový obsah použije kořenová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="4700f-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="4700f-156">Toto je výchozí nastavení používané v [sadě Visual Studio](https://visualstudio.microsoft.com) a nové šablony [dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="4700f-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="4700f-157">Další informace o konfiguraci <xref:fundamentals/configuration/index>aplikace najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="4700f-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="4700f-158">Jako alternativu k `CreateDefaultBuilder` použití statické metody je vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) podporovaným přístupem s ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="4700f-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="4700f-159">Při nastavování hostitele lze poskytnout metody [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) a [ConfigureServices.](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices)</span><span class="sxs-lookup"><span data-stu-id="4700f-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="4700f-160">Pokud `Startup` je zadána třída, `Configure` musí definovat metodu.</span><span class="sxs-lookup"><span data-stu-id="4700f-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="4700f-161">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4700f-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="4700f-162">Více volání `ConfigureServices` k sobě navzájem.</span><span class="sxs-lookup"><span data-stu-id="4700f-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="4700f-163">Více volání `Configure` `UseStartup` nebo `WebHostBuilder` na nahradit předchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="4700f-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="4700f-164">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="4700f-164">Host configuration values</span></span>

<span data-ttu-id="4700f-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) spoléhá na následující přístupy k nastavení hodnot konfigurace hostitele:</span><span class="sxs-lookup"><span data-stu-id="4700f-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="4700f-166">Konfigurace tvůrce hostitelů, která zahrnuje `ASPNETCORE_{configurationKey}`proměnné prostředí s formátem .</span><span class="sxs-lookup"><span data-stu-id="4700f-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="4700f-167">Například, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="4700f-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="4700f-168">Rozšíření, jako je [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz [přepsání konfigurace](#override-configuration) části).</span><span class="sxs-lookup"><span data-stu-id="4700f-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="4700f-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč.</span><span class="sxs-lookup"><span data-stu-id="4700f-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="4700f-170">Při nastavování `UseSetting`hodnoty s , hodnota je nastavena jako řetězec bez ohledu na typ.</span><span class="sxs-lookup"><span data-stu-id="4700f-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="4700f-171">Hostitel používá podle toho, která možnost nastaví hodnotu jako poslední.</span><span class="sxs-lookup"><span data-stu-id="4700f-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="4700f-172">Další informace naleznete v tématu [Přepsat konfiguraci](#override-configuration) v další části.</span><span class="sxs-lookup"><span data-stu-id="4700f-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="4700f-173">Aplikační klíč (název)</span><span class="sxs-lookup"><span data-stu-id="4700f-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4700f-174">Vlastnost `IWebHostEnvironment.ApplicationName` je automaticky nastavena při [usestartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) je volána během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="4700f-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="4700f-175">Hodnota je nastavena na název sestavení obsahující vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="4700f-176">Chcete-li hodnotu nastavit explicitně, použijte [webHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="4700f-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4700f-177">Vlastnost [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během výstavby hostitele.</span><span class="sxs-lookup"><span data-stu-id="4700f-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="4700f-178">Hodnota je nastavena na název sestavení obsahující vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="4700f-179">Chcete-li hodnotu nastavit explicitně, použijte [webHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="4700f-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="4700f-180">**Klíč**: applicationName</span><span class="sxs-lookup"><span data-stu-id="4700f-180">**Key**: applicationName</span></span>  
<span data-ttu-id="4700f-181">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="4700f-181">**Type**: *string*</span></span>  
<span data-ttu-id="4700f-182">**Výchozí**: Název sestavení obsahujícího vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="4700f-183">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4700f-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4700f-184">**Proměnná prostředí**:`ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4700f-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="4700f-185">Zachytit chyby při spuštění</span><span class="sxs-lookup"><span data-stu-id="4700f-185">Capture Startup Errors</span></span>

<span data-ttu-id="4700f-186">Toto nastavení řídí zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="4700f-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="4700f-187">**Klíč:** captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4700f-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="4700f-188">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="4700f-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4700f-189">**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .</span><span class="sxs-lookup"><span data-stu-id="4700f-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4700f-190">**Nastavit pomocí**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4700f-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="4700f-191">**Proměnná prostředí**:`ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4700f-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4700f-192">Když `false`, chyby při spuštění za následek ukončení hostitele.</span><span class="sxs-lookup"><span data-stu-id="4700f-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4700f-193">Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="4700f-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="4700f-194">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="4700f-194">Content root</span></span>

<span data-ttu-id="4700f-195">Toto nastavení určuje, kde ASP.NET Core začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="4700f-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="4700f-196">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="4700f-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="4700f-197">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="4700f-197">**Type**: *string*</span></span>  
<span data-ttu-id="4700f-198">**Výchozí**: Výchozí je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="4700f-199">**Nastavit pomocí**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="4700f-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="4700f-200">**Proměnná prostředí**:`ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4700f-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="4700f-201">Kořen obsahu se také používá jako základní cesta pro [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="4700f-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="4700f-202">Pokud kořenová cesta obsahu neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="4700f-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="4700f-203">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="4700f-203">For more information, see:</span></span>

* [<span data-ttu-id="4700f-204">Základy: Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="4700f-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4700f-205">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="4700f-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="4700f-206">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="4700f-206">Detailed Errors</span></span>

<span data-ttu-id="4700f-207">Určuje, zda by měly být zachyceny podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="4700f-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="4700f-208">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="4700f-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="4700f-209">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="4700f-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4700f-210">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="4700f-210">**Default**: false</span></span>  
<span data-ttu-id="4700f-211">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4700f-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4700f-212">**Proměnná prostředí**:`ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4700f-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="4700f-213">Pokud je povoleno (nebo pokud `Development`je <a href="#environment">nastaveno</a> prostředí ), aplikace zachytí podrobné výjimky.</span><span class="sxs-lookup"><span data-stu-id="4700f-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="4700f-214">Prostředí</span><span class="sxs-lookup"><span data-stu-id="4700f-214">Environment</span></span>

<span data-ttu-id="4700f-215">Nastaví prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-215">Sets the app's environment.</span></span>

<span data-ttu-id="4700f-216">**Klíč**: životní prostředí</span><span class="sxs-lookup"><span data-stu-id="4700f-216">**Key**: environment</span></span>  
<span data-ttu-id="4700f-217">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="4700f-217">**Type**: *string*</span></span>  
<span data-ttu-id="4700f-218">**Výchozí**: Výroba</span><span class="sxs-lookup"><span data-stu-id="4700f-218">**Default**: Production</span></span>  
<span data-ttu-id="4700f-219">**Nastavit pomocí**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="4700f-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="4700f-220">**Proměnná prostředí**:`ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4700f-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="4700f-221">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4700f-221">The environment can be set to any value.</span></span> <span data-ttu-id="4700f-222">Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a .</span><span class="sxs-lookup"><span data-stu-id="4700f-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4700f-223">Hodnoty nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="4700f-223">Values aren't case sensitive.</span></span> <span data-ttu-id="4700f-224">Ve výchozím nastavení *prostředí* je `ASPNETCORE_ENVIRONMENT` číst z proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4700f-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="4700f-225">Při použití [sady Visual Studio](https://visualstudio.microsoft.com)mohou být v souboru *launchSettings.json* nastaveny proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="4700f-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="4700f-226">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4700f-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="4700f-227">Hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="4700f-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="4700f-228">Nastaví hostitelská sestavení aplikace pro spuštění.</span><span class="sxs-lookup"><span data-stu-id="4700f-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="4700f-229">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4700f-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="4700f-230">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="4700f-230">**Type**: *string*</span></span>  
<span data-ttu-id="4700f-231">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="4700f-231">**Default**: Empty string</span></span>  
<span data-ttu-id="4700f-232">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4700f-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4700f-233">**Proměnná prostředí**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4700f-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4700f-234">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění.</span><span class="sxs-lookup"><span data-stu-id="4700f-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="4700f-235">Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4700f-236">Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="4700f-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="4700f-237">HTTPS Port</span><span class="sxs-lookup"><span data-stu-id="4700f-237">HTTPS Port</span></span>

<span data-ttu-id="4700f-238">Nastavte port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4700f-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="4700f-239">Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="4700f-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4700f-240">**Klíč**: https_port **Typ**: *řetězec*
**Výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="4700f-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="4700f-241">**Nastavit**pomocí `UseSetting` 
: **Proměnná prostředí**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4700f-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="4700f-242">Hostování sestavení vyloučení po spuštění</span><span class="sxs-lookup"><span data-stu-id="4700f-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="4700f-243">Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.</span><span class="sxs-lookup"><span data-stu-id="4700f-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4700f-244">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4700f-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="4700f-245">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="4700f-245">**Type**: *string*</span></span>  
<span data-ttu-id="4700f-246">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="4700f-246">**Default**: Empty string</span></span>  
<span data-ttu-id="4700f-247">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4700f-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4700f-248">**Proměnná prostředí**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4700f-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="4700f-249">Preferovat adresy URL hostování</span><span class="sxs-lookup"><span data-stu-id="4700f-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="4700f-250">Označuje, zda má hostitel naslouchat adresám `WebHostBuilder` URL nakonfigurovaným s namísto adres nakonfigurovaných s implementací. `IServer`</span><span class="sxs-lookup"><span data-stu-id="4700f-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4700f-251">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4700f-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="4700f-252">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="4700f-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4700f-253">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="4700f-253">**Default**: true</span></span>  
<span data-ttu-id="4700f-254">**Nastavit pomocí**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4700f-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="4700f-255">**Proměnná prostředí**:`ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4700f-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="4700f-256">Zabránit spuštění hostingu</span><span class="sxs-lookup"><span data-stu-id="4700f-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="4700f-257">Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4700f-258">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4700f-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4700f-259">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4700f-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="4700f-260">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="4700f-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4700f-261">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="4700f-261">**Default**: false</span></span>  
<span data-ttu-id="4700f-262">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4700f-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4700f-263">**Proměnná prostředí**:`ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4700f-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="4700f-264">Adresy URL serveru</span><span class="sxs-lookup"><span data-stu-id="4700f-264">Server URLs</span></span>

<span data-ttu-id="4700f-265">Označuje adresy IP nebo hostitelské adresy s porty a protokoly, které by měl server naslouchat požadavkům.</span><span class="sxs-lookup"><span data-stu-id="4700f-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="4700f-266">**Klíč**: urls</span><span class="sxs-lookup"><span data-stu-id="4700f-266">**Key**: urls</span></span>  
<span data-ttu-id="4700f-267">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="4700f-267">**Type**: *string*</span></span>  
<span data-ttu-id="4700f-268">**Výchozí :**http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="4700f-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="4700f-269">**Nastavit pomocí**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="4700f-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="4700f-270">**Proměnná prostředí**:`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="4700f-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="4700f-271">Nastaveno na středník-oddělené (;) seznam předpon URL, na které by měl server reagovat.</span><span class="sxs-lookup"><span data-stu-id="4700f-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="4700f-272">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4700f-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4700f-273">Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ).</span><span class="sxs-lookup"><span data-stu-id="4700f-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4700f-274">Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="4700f-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4700f-275">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="4700f-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="4700f-276">Kestrel má vlastní konfigurační rozhraní API koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="4700f-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4700f-277">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4700f-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="4700f-278">Časový čas vypnutí</span><span class="sxs-lookup"><span data-stu-id="4700f-278">Shutdown Timeout</span></span>

<span data-ttu-id="4700f-279">Určuje dobu čekání na vypnutí webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="4700f-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="4700f-280">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="4700f-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="4700f-281">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="4700f-281">**Type**: *int*</span></span>  
<span data-ttu-id="4700f-282">**Výchozí**: 5</span><span class="sxs-lookup"><span data-stu-id="4700f-282">**Default**: 5</span></span>  
<span data-ttu-id="4700f-283">**Nastavit pomocí**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="4700f-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="4700f-284">**Proměnná prostředí**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4700f-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4700f-285">Přestože klíč přijímá *int* s `UseSetting` (například `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension metoda trvá [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="4700f-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="4700f-286">Během časového období, hosting:</span><span class="sxs-lookup"><span data-stu-id="4700f-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="4700f-287">Aktivuje [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="4700f-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4700f-288">Pokusí se zastavit hostované služby, protokolování všechny chyby pro služby, které se nepodaří zastavit.</span><span class="sxs-lookup"><span data-stu-id="4700f-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="4700f-289">Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4700f-290">Služby se zastaví, i když ještě nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="4700f-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4700f-291">Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.</span><span class="sxs-lookup"><span data-stu-id="4700f-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="4700f-292">Sestavení při spuštění</span><span class="sxs-lookup"><span data-stu-id="4700f-292">Startup Assembly</span></span>

<span data-ttu-id="4700f-293">Určuje sestavení k hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="4700f-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4700f-294">**Klíč**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="4700f-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="4700f-295">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="4700f-295">**Type**: *string*</span></span>  
<span data-ttu-id="4700f-296">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="4700f-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4700f-297">**Nastavit pomocí**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="4700f-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="4700f-298">**Proměnná prostředí**:`ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4700f-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4700f-299">Na sestavení podle`string`názvu (`TStartup`) nebo typu ( ) lze odkazovat.</span><span class="sxs-lookup"><span data-stu-id="4700f-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="4700f-300">Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.</span><span class="sxs-lookup"><span data-stu-id="4700f-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="4700f-301">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="4700f-301">Web root</span></span>

<span data-ttu-id="4700f-302">Nastaví relativní cestu ke statickým datovým zdrojům aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="4700f-303">**Klíč**: webroot</span><span class="sxs-lookup"><span data-stu-id="4700f-303">**Key**: webroot</span></span>  
<span data-ttu-id="4700f-304">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="4700f-304">**Type**: *string*</span></span>  
<span data-ttu-id="4700f-305">**Výchozí**: Výchozí `wwwroot`hodnota je .</span><span class="sxs-lookup"><span data-stu-id="4700f-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4700f-306">Cesta k *kořenovému adresáři obsahu}/www root* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="4700f-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="4700f-307">Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.</span><span class="sxs-lookup"><span data-stu-id="4700f-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="4700f-308">**Nastavit pomocí**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="4700f-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="4700f-309">**Proměnná prostředí**:`ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4700f-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="4700f-310">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="4700f-310">For more information, see:</span></span>

* [<span data-ttu-id="4700f-311">Základy: Kořen webu</span><span class="sxs-lookup"><span data-stu-id="4700f-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4700f-312">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="4700f-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="4700f-313">Přepsat konfiguraci</span><span class="sxs-lookup"><span data-stu-id="4700f-313">Override configuration</span></span>

<span data-ttu-id="4700f-314">Ke konfiguraci webového hostitele použijte [konfiguraci](xref:fundamentals/configuration/index) konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4700f-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="4700f-315">V následujícím příkladu je konfigurace hostitele volitelně zadána v souboru *hostsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="4700f-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="4700f-316">Jakákoli konfigurace načtená ze souboru *hostsettings.json* může být přepsána argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="4700f-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="4700f-317">Vytvořená konfigurace `config`(in) se používá ke konfiguraci hostitele pomocí [useConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4700f-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="4700f-318">`IWebHostBuilder`konfigurace je přidána do konfigurace aplikace, ale opak&mdash; `ConfigureAppConfiguration` není pravdivý `IWebHostBuilder` nemá vliv na konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="4700f-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="4700f-319">Přepsání konfigurace poskytované `UseUrls` pomocí konfigurace *hostsettings.json* first, příkazový řádek argument config druhý:</span><span class="sxs-lookup"><span data-stu-id="4700f-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="4700f-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4700f-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="4700f-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) pouze kopíruje `IConfiguration` klíče z poskytnutých do konfigurace tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="4700f-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="4700f-322">Proto nastavení `reloadOnChange: true` pro soubory nastavení JSON, INI a XML nemá žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="4700f-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="4700f-323">Chcete-li určit spuštění hostitele na konkrétní adrese URL, požadovaná hodnota může být předána z příkazového řádku při provádění [spuštění dotnet](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="4700f-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="4700f-324">Argument příkazového řádku přepíše hodnotu `urls` ze souboru *hostsettings.json* a server naslouchá na portu 8080:</span><span class="sxs-lookup"><span data-stu-id="4700f-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="4700f-325">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="4700f-325">Manage the host</span></span>

<span data-ttu-id="4700f-326">**Spustit**</span><span class="sxs-lookup"><span data-stu-id="4700f-326">**Run**</span></span>

<span data-ttu-id="4700f-327">Metoda `Run` spustí webovou aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne:</span><span class="sxs-lookup"><span data-stu-id="4700f-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="4700f-328">**Zahájení**</span><span class="sxs-lookup"><span data-stu-id="4700f-328">**Start**</span></span>

<span data-ttu-id="4700f-329">Spusťte hostitele neblokujícím způsobem `Start` voláním jeho metody:</span><span class="sxs-lookup"><span data-stu-id="4700f-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="4700f-330">Pokud je `Start` metodě předán seznam adres URL, naslouchá na zadaných adresách URL:</span><span class="sxs-lookup"><span data-stu-id="4700f-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="4700f-331">Aplikace může inicializovat a spustit nového hostitele pomocí `CreateDefaultBuilder` předem nakonfigurovaných výchozích hodnot pomocí statické metody pohodlí.</span><span class="sxs-lookup"><span data-stu-id="4700f-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="4700f-332">Tyto metody spustit server bez výstupu konzoly a [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) čekat na přestávku (Ctrl-C/SIGINT nebo SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="4700f-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="4700f-333">**Start (aplikace RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="4700f-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="4700f-334">Začněte `RequestDelegate`s :</span><span class="sxs-lookup"><span data-stu-id="4700f-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4700f-335">Požádejte v prohlížeči `http://localhost:5000` o odpověď "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="4700f-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="4700f-336">`WaitForShutdown`bloky, dokud není vydána přestávka (Ctrl-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="4700f-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4700f-337">Aplikace zobrazí `Console.WriteLine` zprávu a čeká na ukončení stisknutí klávesy.</span><span class="sxs-lookup"><span data-stu-id="4700f-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4700f-338">**Start (adresa url řetězce, aplikace RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="4700f-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="4700f-339">Začněte s `RequestDelegate`adresou URL a :</span><span class="sxs-lookup"><span data-stu-id="4700f-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4700f-340">Vytvoří stejný výsledek jako **Start(RequestDelegate app)**, s `http://localhost:8080`výjimkou aplikace reaguje na .</span><span class="sxs-lookup"><span data-stu-id="4700f-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="4700f-341">**Start (akce\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="4700f-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="4700f-342">Použijte instanci `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) k použití middlewaru směrování:</span><span class="sxs-lookup"><span data-stu-id="4700f-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4700f-343">V příkladu použijte následující požadavky prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="4700f-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="4700f-344">Žádost</span><span class="sxs-lookup"><span data-stu-id="4700f-344">Request</span></span>                                    | <span data-ttu-id="4700f-345">Odpověď</span><span class="sxs-lookup"><span data-stu-id="4700f-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="4700f-346">Ahoj, Martine!</span><span class="sxs-lookup"><span data-stu-id="4700f-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="4700f-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="4700f-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="4700f-348">Vyvolá výjimku s řetězcem "jejda!"</span><span class="sxs-lookup"><span data-stu-id="4700f-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="4700f-349">Vyvolá výjimku s řetězcem "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="4700f-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="4700f-350">Sante, Kevine!</span><span class="sxs-lookup"><span data-stu-id="4700f-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="4700f-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="4700f-351">Hello World!</span></span>                             |

<span data-ttu-id="4700f-352">`WaitForShutdown`bloky, dokud není vydána přestávka (Ctrl-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="4700f-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4700f-353">Aplikace zobrazí `Console.WriteLine` zprávu a čeká na ukončení stisknutí klávesy.</span><span class="sxs-lookup"><span data-stu-id="4700f-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4700f-354">**Start (adresa URL\<řetězce, akce IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="4700f-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="4700f-355">Použijte adresu URL a `IRouteBuilder`instanci :</span><span class="sxs-lookup"><span data-stu-id="4700f-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4700f-356">Vytvoří stejný výsledek jako **Start(Action\<IRouteBuilder> routeBuilder)**, `http://localhost:8080`s výjimkou aplikace reaguje na .</span><span class="sxs-lookup"><span data-stu-id="4700f-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="4700f-357">**StartWith(Akce\<IApplicationBuilder> aplikace)**</span><span class="sxs-lookup"><span data-stu-id="4700f-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="4700f-358">Poskytněte delegátovi konfiguraci : `IApplicationBuilder`</span><span class="sxs-lookup"><span data-stu-id="4700f-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4700f-359">Požádejte v prohlížeči `http://localhost:5000` o odpověď "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="4700f-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="4700f-360">`WaitForShutdown`bloky, dokud není vydána přestávka (Ctrl-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="4700f-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4700f-361">Aplikace zobrazí `Console.WriteLine` zprávu a čeká na ukončení stisknutí klávesy.</span><span class="sxs-lookup"><span data-stu-id="4700f-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4700f-362">**StartWith (adresa url\<řetězce, akce IApplicationBuilder> aplikace)**</span><span class="sxs-lookup"><span data-stu-id="4700f-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="4700f-363">Zadejte adresu URL a `IApplicationBuilder`delegáta pro konfiguraci :</span><span class="sxs-lookup"><span data-stu-id="4700f-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4700f-364">Vytvoří stejný výsledek jako **StartWith(Akce\<IApplicationBuilder> aplikace)**, `http://localhost:8080`s výjimkou aplikace reaguje na .</span><span class="sxs-lookup"><span data-stu-id="4700f-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="4700f-365">Rozhraní IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4700f-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="4700f-366">Rozhraní `IWebHostEnvironment` poskytuje informace o webhostingovém prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="4700f-367">Použijte [vstřikování konstruktoru](xref:fundamentals/dependency-injection) `IWebHostEnvironment` k získání, abyste mohli používat jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4700f-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="4700f-368">[Přístup založený na konvencích](xref:fundamentals/environments#environment-based-startup-class-and-methods) lze použít ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="4700f-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="4700f-369">Alternativně vstříkněte `IWebHostEnvironment` do konstruktoru `Startup` pro použití v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4700f-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="4700f-370">`IsDevelopment` Kromě rozšiřující metody `IWebHostEnvironment` nabízí `IsStaging`, `IsProduction`a `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="4700f-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="4700f-371">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4700f-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4700f-372">Služba `IWebHostEnvironment` může být také vložena `Configure` přímo do metody pro nastavení kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="4700f-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="4700f-373">`IWebHostEnvironment`lze vstříknout `Invoke` do metody při vytváření vlastního [middleware](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="4700f-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="4700f-374">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="4700f-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="4700f-375">[Rozhraní IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o webhostingovém prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="4700f-376">Použijte [vstřikování konstruktoru](xref:fundamentals/dependency-injection) `IHostingEnvironment` k získání, abyste mohli používat jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4700f-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="4700f-377">[Přístup založený na konvencích](xref:fundamentals/environments#environment-based-startup-class-and-methods) lze použít ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="4700f-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="4700f-378">Alternativně vstříkněte `IHostingEnvironment` do konstruktoru `Startup` pro použití v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4700f-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="4700f-379">`IsDevelopment` Kromě rozšiřující metody `IHostingEnvironment` nabízí `IsStaging`, `IsProduction`a `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="4700f-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="4700f-380">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4700f-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4700f-381">Služba `IHostingEnvironment` může být také vložena `Configure` přímo do metody pro nastavení kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="4700f-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="4700f-382">`IHostingEnvironment`lze vstříknout `Invoke` do metody při vytváření vlastního [middleware](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="4700f-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="4700f-383">Rozhraní IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4700f-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="4700f-384">`IHostApplicationLifetime`umožňuje činnosti po spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4700f-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="4700f-385">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4700f-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4700f-386">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="4700f-386">Cancellation Token</span></span>    | <span data-ttu-id="4700f-387">Aktivuje se při&#8230;</span><span class="sxs-lookup"><span data-stu-id="4700f-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="4700f-388">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="4700f-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="4700f-389">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4700f-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4700f-390">Všechny požadavky by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="4700f-390">All requests should be processed.</span></span> <span data-ttu-id="4700f-391">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="4700f-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="4700f-392">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4700f-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4700f-393">Požadavky mohou být stále zpracování.</span><span class="sxs-lookup"><span data-stu-id="4700f-393">Requests may still be processing.</span></span> <span data-ttu-id="4700f-394">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="4700f-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="4700f-395">`StopApplication`požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="4700f-396">Následující třída `StopApplication` používá k řádnému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="4700f-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="4700f-397">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4700f-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="4700f-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje aktivity po spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4700f-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="4700f-399">Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4700f-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4700f-400">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="4700f-400">Cancellation Token</span></span>    | <span data-ttu-id="4700f-401">Aktivuje se při&#8230;</span><span class="sxs-lookup"><span data-stu-id="4700f-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="4700f-402">Aplikace spuštěna</span><span class="sxs-lookup"><span data-stu-id="4700f-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="4700f-403">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="4700f-403">The host has fully started.</span></span> |
| [<span data-ttu-id="4700f-404">Aplikace byla zastavena.</span><span class="sxs-lookup"><span data-stu-id="4700f-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="4700f-405">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4700f-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4700f-406">Všechny požadavky by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="4700f-406">All requests should be processed.</span></span> <span data-ttu-id="4700f-407">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="4700f-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="4700f-408">AplikaceZastavování</span><span class="sxs-lookup"><span data-stu-id="4700f-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="4700f-409">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="4700f-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4700f-410">Požadavky mohou být stále zpracování.</span><span class="sxs-lookup"><span data-stu-id="4700f-410">Requests may still be processing.</span></span> <span data-ttu-id="4700f-411">Vypnutí blokuje, dokud tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="4700f-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="4700f-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="4700f-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="4700f-413">Následující třída `StopApplication` používá k řádnému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="4700f-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="4700f-414">Ověření oboru</span><span class="sxs-lookup"><span data-stu-id="4700f-414">Scope validation</span></span>

<span data-ttu-id="4700f-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` pokud je prostředí aplikace vývoj.</span><span class="sxs-lookup"><span data-stu-id="4700f-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="4700f-416">Je-li `ValidateScopes` `true`nastavena hodnota , provádí výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="4700f-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="4700f-417">Služby s oborem nejsou přímo ani nepřímo vyřešeny od kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="4700f-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="4700f-418">Oborové služby nejsou přímo nebo nepřímo vloženy do singletons.</span><span class="sxs-lookup"><span data-stu-id="4700f-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="4700f-419">Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider.](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider)</span><span class="sxs-lookup"><span data-stu-id="4700f-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="4700f-420">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace nebo serveru, když poskytovatel začne s aplikací a je uvolněn, když se aplikace vypne.</span><span class="sxs-lookup"><span data-stu-id="4700f-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="4700f-421">Služby s vymezeným oborem jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="4700f-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="4700f-422">Pokud je služba s vymezeným oborem vytvořena v kořenovém kontejneru, životnost služby je efektivně povýšena na singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="4700f-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="4700f-423">Ověřování oborů služby zachytí tyto situace, když `BuildServiceProvider` je volána.</span><span class="sxs-lookup"><span data-stu-id="4700f-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="4700f-424">Chcete-li vždy ověřovat obory, včetně produkčního prostředí, nakonfigurujte [serviceprovideroptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) s [useDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrce hostitele:</span><span class="sxs-lookup"><span data-stu-id="4700f-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="4700f-425">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4700f-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
