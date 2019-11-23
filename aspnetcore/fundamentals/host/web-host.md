---
title: ASP.NET Core webového hostitele
author: rick-anderson
description: Přečtěte si o webovém hostiteli v ASP.NET Core, který zodpovídá za správu spouštění a životního cyklu aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: bc18b5490d232758b796d33a62cd8d1a7dd7289f
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007106"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="17661-103">ASP.NET Core webového hostitele</span><span class="sxs-lookup"><span data-stu-id="17661-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="17661-104">ASP.NET Core aplikace nakonfigurují a spouštějí *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="17661-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="17661-105">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="17661-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="17661-106">Minimálně hostitel nakonfiguruje server a kanál zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="17661-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="17661-107">Hostitel může také nastavit protokolování, vkládání závislostí a konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="17661-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17661-108">Tento článek se zabývá webovým hostitelem, který zůstává dostupný jenom pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="17661-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="17661-109">[Obecný hostitel](xref:fundamentals/host/generic-host) je doporučený pro všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="17661-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17661-110">Tento článek se zabývá webovým hostitelem, který je určený pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="17661-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="17661-111">Pro jiné druhy aplikací použijte [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="17661-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="17661-112">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="17661-112">Set up a host</span></span>

<span data-ttu-id="17661-113">Vytvořte hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="17661-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="17661-114">To se obvykle provádí v vstupním bodě aplikace, `Main` metoda.</span><span class="sxs-lookup"><span data-stu-id="17661-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="17661-115">V šablonách projektu `Main` se nachází v *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="17661-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="17661-116">Typická aplikace volá [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) , aby zahájila nastavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="17661-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="17661-117">Kód, který volá `CreateDefaultBuilder` je v metodě s názvem `CreateWebHostBuilder`, která ho odděluje od kódu v `Main`, který volá `Run` objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="17661-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="17661-118">Toto oddělení se vyžaduje při použití [Entity Framework Corech nástrojů](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="17661-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="17661-119">Nástroje očekávají, že `CreateWebHostBuilder` metodu, kterou mohou volat v době návrhu ke konfiguraci hostitele bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="17661-120">Alternativou je implementace `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="17661-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="17661-121">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="17661-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="17661-122">`CreateDefaultBuilder` provádí následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="17661-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="17661-123">Nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="17661-124">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="17661-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="17661-125">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou [adresářem. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="17661-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="17661-126">Načte [konfiguraci hostitele](#host-configuration-values) z:</span><span class="sxs-lookup"><span data-stu-id="17661-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="17661-127">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="17661-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="17661-128">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="17661-128">Command-line arguments.</span></span>
* <span data-ttu-id="17661-129">Načte konfiguraci aplikace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="17661-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="17661-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="17661-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="17661-131">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="17661-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="17661-132">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="17661-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="17661-133">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="17661-133">Environment variables.</span></span>
  * <span data-ttu-id="17661-134">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="17661-134">Command-line arguments.</span></span>
* <span data-ttu-id="17661-135">Konfiguruje [protokolování](xref:fundamentals/logging/index) pro konzolu a výstup ladění.</span><span class="sxs-lookup"><span data-stu-id="17661-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="17661-136">Protokolování zahrnuje pravidla [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) zadaná v sekci Konfigurace protokolování souboru *appSettings. JSON* nebo *appSettings. { Environment}. JSON* soubor.</span><span class="sxs-lookup"><span data-stu-id="17661-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="17661-137">Při spuštění za službou IIS s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module)`CreateDefaultBuilder` povolí [integraci služby IIS](xref:host-and-deploy/iis/index), která nakonfiguruje základní adresu a port aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="17661-138">Integrace služby IIS také nakonfiguruje aplikaci pro [zachycení chyb při spuštění](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="17661-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="17661-139">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="17661-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="17661-140">Nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) , aby `true`, jestli je prostředí aplikace vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="17661-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="17661-141">Další informace najdete v tématu [ověřování oboru](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="17661-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="17661-142">Konfiguraci definovanou pomocí `CreateDefaultBuilder` lze přepsat a rozšířit pomocí [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a další metody a metody rozšíření [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="17661-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="17661-143">Následuje několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="17661-143">A few examples follow:</span></span>

* <span data-ttu-id="17661-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se používá k určení dalších `IConfiguration` pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="17661-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="17661-145">Následující `ConfigureAppConfiguration` volání přidá delegáta pro zahrnutí konfigurace aplikace do souboru *appSettings. XML* .</span><span class="sxs-lookup"><span data-stu-id="17661-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="17661-146">`ConfigureAppConfiguration` může být volána víckrát.</span><span class="sxs-lookup"><span data-stu-id="17661-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="17661-147">Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí).</span><span class="sxs-lookup"><span data-stu-id="17661-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="17661-148">Viz část [hodnoty konfigurace hostitele](#host-configuration-values) .</span><span class="sxs-lookup"><span data-stu-id="17661-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="17661-149">Následující `ConfigureLogging` volání přidá delegáta pro konfiguraci minimální úrovně protokolování ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="17661-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="17661-150">Toto nastavení přepisuje nastavení v *appSettings. Development. JSON* (`LogLevel.Debug`) a *appSettings. Produkční. JSON* (`LogLevel.Error`) nakonfigurovaný pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="17661-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="17661-151">`ConfigureLogging` může být volána víckrát.</span><span class="sxs-lookup"><span data-stu-id="17661-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="17661-152">Následující volání `ConfigureKestrel` přepisuje výchozí [omezení. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtů zavedených při konfiguraci Kestrel pomocí `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="17661-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="17661-153">Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepisuje výchozí [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) z 30 000 000 bajtů zavedených při konfiguraci Kestrel pomocí `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="17661-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="17661-154">[Kořen obsahu](xref:fundamentals/index#content-root) určuje, kde hostitel vyhledává soubory obsahu, například soubory zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="17661-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="17661-155">Při spuštění aplikace z kořenové složky projektu se jako kořen obsahu použije kořenová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="17661-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="17661-156">Toto je výchozí použití v [aplikaci Visual Studio](https://visualstudio.microsoft.com) a [dotnet New Templates](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="17661-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="17661-157">Další informace o konfiguraci aplikace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="17661-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="17661-158">Jako alternativu k použití statické metody `CreateDefaultBuilder` je vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) podporovaným přístupem s ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="17661-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="17661-159">Při nastavování hostitele je možné zadat metody [Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) .</span><span class="sxs-lookup"><span data-stu-id="17661-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="17661-160">Pokud je zadána třída `Startup`, musí definovat metodu `Configure`.</span><span class="sxs-lookup"><span data-stu-id="17661-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="17661-161">Další informace najdete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="17661-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="17661-162">Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání.</span><span class="sxs-lookup"><span data-stu-id="17661-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="17661-163">Několik volání `Configure` nebo `UseStartup` na `WebHostBuilder` nahrazuje předchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="17661-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="17661-164">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="17661-164">Host configuration values</span></span>

<span data-ttu-id="17661-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) spoléhá na následující přístupy k nastavení hodnot konfigurace hostitele:</span><span class="sxs-lookup"><span data-stu-id="17661-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="17661-166">Konfigurace tvůrce hostitele, což zahrnuje proměnné prostředí ve formátu `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="17661-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="17661-167">Například, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="17661-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="17661-168">Rozšíření jako [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz oddíl pro [přepsání konfigurace](#override-configuration) ).</span><span class="sxs-lookup"><span data-stu-id="17661-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="17661-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč.</span><span class="sxs-lookup"><span data-stu-id="17661-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="17661-170">Při nastavování hodnoty pomocí `UseSetting`je hodnota nastavena jako řetězec bez ohledu na typ.</span><span class="sxs-lookup"><span data-stu-id="17661-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="17661-171">Hostitel používá jakoukoli možnost, která hodnotu nastaví jako poslední.</span><span class="sxs-lookup"><span data-stu-id="17661-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="17661-172">Další informace najdete v tématu [přepsání konfigurace](#override-configuration) v následující části.</span><span class="sxs-lookup"><span data-stu-id="17661-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="17661-173">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="17661-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17661-174">Vlastnost `IWebHostEnvironment.ApplicationName` je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="17661-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="17661-175">Hodnota je nastavená na název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="17661-176">Chcete-li nastavit hodnotu explicitně, použijte [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="17661-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17661-177">Vlastnost [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="17661-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="17661-178">Hodnota je nastavená na název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="17661-179">Chcete-li nastavit hodnotu explicitně, použijte [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="17661-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="17661-180">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="17661-180">**Key**: applicationName</span></span>  
<span data-ttu-id="17661-181">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="17661-181">**Type**: *string*</span></span>  
<span data-ttu-id="17661-182">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="17661-183">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="17661-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="17661-184">**Proměnná prostředí**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="17661-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="17661-185">Zachytit chyby při spuštění</span><span class="sxs-lookup"><span data-stu-id="17661-185">Capture Startup Errors</span></span>

<span data-ttu-id="17661-186">Toto nastavení řídí zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="17661-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="17661-187">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="17661-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="17661-188">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="17661-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="17661-189">**Výchozí**: výchozí nastavení `false`, pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true`.</span><span class="sxs-lookup"><span data-stu-id="17661-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="17661-190">**Nastavit pomocí**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="17661-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="17661-191">**Proměnná prostředí**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="17661-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="17661-192">Při `false`dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="17661-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="17661-193">Při `true`zachytí hostitel při spuštění výjimky a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="17661-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="17661-194">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="17661-194">Content root</span></span>

<span data-ttu-id="17661-195">Toto nastavení určuje, kde ASP.NET Core začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="17661-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="17661-196">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="17661-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="17661-197">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="17661-197">**Type**: *string*</span></span>  
<span data-ttu-id="17661-198">**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="17661-199">**Nastavit pomocí**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="17661-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="17661-200">**Proměnná prostředí**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="17661-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="17661-201">Kořen obsahu se používá také jako základní cesta pro [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="17661-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="17661-202">Pokud kořenová cesta obsahu neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="17661-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="17661-203">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="17661-203">For more information, see:</span></span>

* [<span data-ttu-id="17661-204">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="17661-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="17661-205">Webový kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="17661-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="17661-206">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="17661-206">Detailed Errors</span></span>

<span data-ttu-id="17661-207">Určuje, zda mají být zachyceny podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="17661-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="17661-208">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="17661-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="17661-209">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="17661-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="17661-210">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="17661-210">**Default**: false</span></span>  
<span data-ttu-id="17661-211">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="17661-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="17661-212">**Proměnná prostředí**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="17661-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="17661-213">Pokud je tato možnost povolená (nebo když je <a href="#environment">prostředí</a> nastavené na `Development`), aplikace zachycuje podrobné výjimky.</span><span class="sxs-lookup"><span data-stu-id="17661-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="17661-214">Prostředí</span><span class="sxs-lookup"><span data-stu-id="17661-214">Environment</span></span>

<span data-ttu-id="17661-215">Nastaví prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-215">Sets the app's environment.</span></span>

<span data-ttu-id="17661-216">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="17661-216">**Key**: environment</span></span>  
<span data-ttu-id="17661-217">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="17661-217">**Type**: *string*</span></span>  
<span data-ttu-id="17661-218">**Výchozí**: výroba</span><span class="sxs-lookup"><span data-stu-id="17661-218">**Default**: Production</span></span>  
<span data-ttu-id="17661-219">**Nastavit pomocí**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="17661-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="17661-220">**Proměnná prostředí**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="17661-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="17661-221">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="17661-221">The environment can be set to any value.</span></span> <span data-ttu-id="17661-222">Mezi hodnoty definované rozhraní patří `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="17661-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="17661-223">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="17661-223">Values aren't case sensitive.</span></span> <span data-ttu-id="17661-224">Ve výchozím nastavení je *prostředí* čteno z proměnné prostředí `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="17661-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="17661-225">Při použití sady [Visual Studio](https://visualstudio.microsoft.com)mohou být proměnné prostředí nastaveny v souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="17661-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="17661-226">Další informace najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="17661-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="17661-227">Hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="17661-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="17661-228">Nastaví hostování spouštěcích sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="17661-229">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="17661-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="17661-230">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="17661-230">**Type**: *string*</span></span>  
<span data-ttu-id="17661-231">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="17661-231">**Default**: Empty string</span></span>  
<span data-ttu-id="17661-232">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="17661-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="17661-233">**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="17661-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="17661-234">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="17661-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="17661-235">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="17661-236">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="17661-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="17661-237">Port HTTPS</span><span class="sxs-lookup"><span data-stu-id="17661-237">HTTPS Port</span></span>

<span data-ttu-id="17661-238">Nastavte port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="17661-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="17661-239">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="17661-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="17661-240">**Klíč**: https_port **typ**: *řetězec*
**výchozí**: není nastavena výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="17661-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="17661-241">**Nastavit pomocí**: `UseSetting`
**proměnná prostředí**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="17661-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="17661-242">Hostování nespouštěcích sestavení pro vyloučení</span><span class="sxs-lookup"><span data-stu-id="17661-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="17661-243">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="17661-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="17661-244">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="17661-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="17661-245">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="17661-245">**Type**: *string*</span></span>  
<span data-ttu-id="17661-246">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="17661-246">**Default**: Empty string</span></span>  
<span data-ttu-id="17661-247">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="17661-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="17661-248">**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="17661-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="17661-249">Preferovat hostování adres URL</span><span class="sxs-lookup"><span data-stu-id="17661-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="17661-250">Určuje, zda má hostitel naslouchat na adresách URL konfigurovaných pomocí `WebHostBuilder` namísto těch nakonfigurovaných pomocí implementace `IServer`.</span><span class="sxs-lookup"><span data-stu-id="17661-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="17661-251">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="17661-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="17661-252">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="17661-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="17661-253">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="17661-253">**Default**: true</span></span>  
<span data-ttu-id="17661-254">**Nastavit pomocí**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="17661-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="17661-255">**Proměnná prostředí**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="17661-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="17661-256">Zabránit spuštění hostování</span><span class="sxs-lookup"><span data-stu-id="17661-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="17661-257">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="17661-258">Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="17661-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="17661-259">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="17661-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="17661-260">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="17661-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="17661-261">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="17661-261">**Default**: false</span></span>  
<span data-ttu-id="17661-262">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="17661-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="17661-263">**Proměnná prostředí**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="17661-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="17661-264">Adresy URL serveru</span><span class="sxs-lookup"><span data-stu-id="17661-264">Server URLs</span></span>

<span data-ttu-id="17661-265">Označuje IP adresy nebo adresy hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="17661-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="17661-266">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="17661-266">**Key**: urls</span></span>  
<span data-ttu-id="17661-267">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="17661-267">**Type**: *string*</span></span>  
<span data-ttu-id="17661-268">**Výchozí**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="17661-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="17661-269">**Nastavit pomocí**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="17661-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="17661-270">**Proměnná prostředí**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="17661-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="17661-271">Nastavte na středníkem oddělený (;) Seznam předpon adres URL, na které má server odpovědět</span><span class="sxs-lookup"><span data-stu-id="17661-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="17661-272">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="17661-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="17661-273">Pomocí příkazu "\*" označíte, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="17661-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="17661-274">Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="17661-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="17661-275">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="17661-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="17661-276">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="17661-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="17661-277">Další informace najdete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="17661-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="17661-278">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="17661-278">Shutdown Timeout</span></span>

<span data-ttu-id="17661-279">Určuje dobu, po kterou se má čekat na vypnutí webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="17661-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="17661-280">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="17661-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="17661-281">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="17661-281">**Type**: *int*</span></span>  
<span data-ttu-id="17661-282">**Výchozí**: 5</span><span class="sxs-lookup"><span data-stu-id="17661-282">**Default**: 5</span></span>  
<span data-ttu-id="17661-283">**Nastavit pomocí**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="17661-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="17661-284">**Proměnná prostředí**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="17661-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="17661-285">I když klíč přijímá *int* s `UseSetting` (například `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), metoda rozšíření [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) přebírá [časový interval](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="17661-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="17661-286">Po uplynutí časového limitu hostování:</span><span class="sxs-lookup"><span data-stu-id="17661-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="17661-287">Spustí [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="17661-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="17661-288">Pokusy o zastavení hostovaných služeb a protokolování všech chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="17661-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="17661-289">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="17661-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="17661-290">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="17661-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="17661-291">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="17661-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="17661-292">Spouštěcí sestavení</span><span class="sxs-lookup"><span data-stu-id="17661-292">Startup Assembly</span></span>

<span data-ttu-id="17661-293">Určuje sestavení pro hledání `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="17661-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="17661-294">**Klíč**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="17661-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="17661-295">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="17661-295">**Type**: *string*</span></span>  
<span data-ttu-id="17661-296">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="17661-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="17661-297">**Nastavit pomocí**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="17661-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="17661-298">**Proměnná prostředí**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="17661-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="17661-299">Lze odkazovat na sestavení podle názvu (`string`) nebo typu (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="17661-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="17661-300">Je-li voláno více metod `UseStartup`, má poslední název přednost.</span><span class="sxs-lookup"><span data-stu-id="17661-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="17661-301">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="17661-301">Web root</span></span>

<span data-ttu-id="17661-302">Nastaví relativní cestu k statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="17661-303">**Klíč**: Webroot</span><span class="sxs-lookup"><span data-stu-id="17661-303">**Key**: webroot</span></span>  
<span data-ttu-id="17661-304">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="17661-304">**Type**: *string*</span></span>  
<span data-ttu-id="17661-305">**Výchozí**: výchozí hodnota je `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="17661-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="17661-306">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="17661-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="17661-307">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="17661-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="17661-308">**Nastavit pomocí**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="17661-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="17661-309">**Proměnná prostředí**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="17661-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="17661-310">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="17661-310">For more information, see:</span></span>

* [<span data-ttu-id="17661-311">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="17661-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="17661-312">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="17661-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="17661-313">Přepsat konfiguraci</span><span class="sxs-lookup"><span data-stu-id="17661-313">Override configuration</span></span>

<span data-ttu-id="17661-314">Pro konfiguraci webového hostitele použijte [konfiguraci](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="17661-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="17661-315">V následujícím příkladu je konfigurace hostitele volitelně určena v souboru *HostSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="17661-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="17661-316">Jakékoli konfigurace načtené ze souboru *HostSettings. JSON* může přepsat argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="17661-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="17661-317">Vytvořená konfigurace (v `config`) se používá ke konfiguraci hostitele pomocí [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="17661-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="17661-318">do konfigurace aplikace se přidá `IWebHostBuilder` konfigurace, ale konverzace není pravdivá&mdash;`ConfigureAppConfiguration` nemá vliv na konfiguraci `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="17661-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="17661-319">Přepsání konfigurace poskytované `UseUrls` s první konfigurací *HostSettings. JSON* a konfigurací argumentu příkazového řádku (sekundy):</span><span class="sxs-lookup"><span data-stu-id="17661-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="17661-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="17661-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="17661-321">Metoda rozšíření [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) momentálně není schopná analyzovat konfigurační oddíl vrácený `GetSection` (například `.UseConfiguration(Configuration.GetSection("section"))`.</span><span class="sxs-lookup"><span data-stu-id="17661-321">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="17661-322">Metoda `GetSection` filtruje konfigurační klíče k požadované části, ale ponechá název oddílu na klíčích (například `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="17661-322">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="17661-323">Metoda `UseConfiguration` očekává, že klíče budou odpovídat klíčům `WebHostBuilder` (například `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="17661-323">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="17661-324">Přítomnost názvu oddílu u klíčů brání hodnotám oddílu v konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="17661-324">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="17661-325">Tento problém bude vyřešen v příští verzi.</span><span class="sxs-lookup"><span data-stu-id="17661-325">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="17661-326">Další informace a alternativní řešení naleznete v [části předání konfiguračního oddílu do WebHostBuilder. UseConfiguration používá úplné klíče](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="17661-326">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="17661-327">v konfiguraci tvůrce hostitele se `UseConfiguration` jenom kopírují klíče z poskytnuté `IConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="17661-327">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="17661-328">Proto nastavení `reloadOnChange: true` pro soubory JSON, INI a nastavení XML nemá žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="17661-328">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="17661-329">Chcete-li určit spuštění hostitele na konkrétní adrese URL, lze požadovanou hodnotu předat z příkazového řádku při [spuštění příkazu dotnet](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="17661-329">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="17661-330">Argument příkazového řádku Přepisuje `urls` hodnotu ze souboru *HostSettings. JSON* a server naslouchá na portu 8080:</span><span class="sxs-lookup"><span data-stu-id="17661-330">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="17661-331">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="17661-331">Manage the host</span></span>

<span data-ttu-id="17661-332">**Spuštění**</span><span class="sxs-lookup"><span data-stu-id="17661-332">**Run**</span></span>

<span data-ttu-id="17661-333">Metoda `Run` spustí webovou aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="17661-333">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="17661-334">**Start**</span><span class="sxs-lookup"><span data-stu-id="17661-334">**Start**</span></span>

<span data-ttu-id="17661-335">Spusťte hostitele neblokujícím způsobem voláním jeho `Start` metody:</span><span class="sxs-lookup"><span data-stu-id="17661-335">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="17661-336">Pokud je do metody `Start` předán seznam adres URL, naslouchá na zadaných adresách URL:</span><span class="sxs-lookup"><span data-stu-id="17661-336">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="17661-337">Aplikace se může inicializovat a spustit nového hostitele s použitím předem nakonfigurovaných výchozích hodnot `CreateDefaultBuilder` s využitím staticky ovladatelné metody.</span><span class="sxs-lookup"><span data-stu-id="17661-337">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="17661-338">Tyto metody spustí server bez výstupu konzoly a s [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) čekají na přerušení (CTRL-C/SIGINT nebo SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="17661-338">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="17661-339">**Spustit (aplikace RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="17661-339">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="17661-340">Začněte s `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="17661-340">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="17661-341">Vytvořte v prohlížeči žádost o `http://localhost:5000`, že se má přijmout odpověď "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="17661-341">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="17661-342">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="17661-342">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="17661-343">Aplikace zobrazí `Console.WriteLine`ovou zprávu a počká, až se ukončí stisknutí klávesy.</span><span class="sxs-lookup"><span data-stu-id="17661-343">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="17661-344">**Spustit (adresa URL řetězce, aplikace v RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="17661-344">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="17661-345">Začněte s adresou URL a `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="17661-345">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="17661-346">Vytvoří stejný výsledek jako **Start (aplikace RequestDelegate)** , s výjimkou, že aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="17661-346">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="17661-347">**Start (akce\<IRouteBuilder > routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="17661-347">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="17661-348">Použijte instanci `IRouteBuilder` ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) pro použití middleware směrování:</span><span class="sxs-lookup"><span data-stu-id="17661-348">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="17661-349">Použijte následující požadavky prohlížeče s příkladem:</span><span class="sxs-lookup"><span data-stu-id="17661-349">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="17661-350">Žádost</span><span class="sxs-lookup"><span data-stu-id="17661-350">Request</span></span>                                    | <span data-ttu-id="17661-351">Odpověď</span><span class="sxs-lookup"><span data-stu-id="17661-351">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="17661-352">Hello, Novák!</span><span class="sxs-lookup"><span data-stu-id="17661-352">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="17661-353">Buenos Dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="17661-353">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="17661-354">Vyvolá výjimku s řetězcem "Ooops!"</span><span class="sxs-lookup"><span data-stu-id="17661-354">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="17661-355">Vyvolá výjimku s řetězcem "uh Oh!".</span><span class="sxs-lookup"><span data-stu-id="17661-355">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="17661-356">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="17661-356">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="17661-357">Hello World!</span><span class="sxs-lookup"><span data-stu-id="17661-357">Hello World!</span></span>                             |

<span data-ttu-id="17661-358">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="17661-358">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="17661-359">Aplikace zobrazí `Console.WriteLine`ovou zprávu a počká, až se ukončí stisknutí klávesy.</span><span class="sxs-lookup"><span data-stu-id="17661-359">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="17661-360">**Start (adresa URL řetězce, akce\<IRouteBuilder > routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="17661-360">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="17661-361">Použijte adresu URL a instanci `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="17661-361">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="17661-362">Vytvoří stejný výsledek jako **Start (Action\<IRouteBuilder > routeBuilder)** , s výjimkou, že aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="17661-362">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="17661-363">**StartWith (akce\<IApplicationBuilder > aplikace)**</span><span class="sxs-lookup"><span data-stu-id="17661-363">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="17661-364">Zadejte delegáta pro konfiguraci `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="17661-364">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="17661-365">Vytvořte v prohlížeči žádost o `http://localhost:5000`, že se má přijmout odpověď "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="17661-365">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="17661-366">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="17661-366">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="17661-367">Aplikace zobrazí `Console.WriteLine`ovou zprávu a počká, až se ukončí stisknutí klávesy.</span><span class="sxs-lookup"><span data-stu-id="17661-367">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="17661-368">**StartWith (adresa URL řetězce, akce\<IApplicationBuilder aplikace >)**</span><span class="sxs-lookup"><span data-stu-id="17661-368">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="17661-369">Zadejte adresu URL a delegáta pro konfiguraci `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="17661-369">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="17661-370">Vytvoří stejný výsledek jako **StartWith (Action\<IApplicationBuilder > App)** , s výjimkou, že aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="17661-370">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="17661-371">Rozhraní IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="17661-371">IWebHostEnvironment interface</span></span>

<span data-ttu-id="17661-372">Rozhraní `IWebHostEnvironment` poskytuje informace o prostředí hostování webu aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-372">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="17661-373">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) k získání `IWebHostEnvironment`, aby bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="17661-373">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="17661-374">[Přístup založený na konvenci](xref:fundamentals/environments#environment-based-startup-class-and-methods) je možné použít ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="17661-374">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="17661-375">Případně můžete vložit `IWebHostEnvironment` do konstruktoru `Startup` pro použití v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="17661-375">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="17661-376">Kromě metody rozšíření `IsDevelopment` `IWebHostEnvironment` nabízí `IsStaging`, `IsProduction`a `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="17661-376">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="17661-377">Další informace najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="17661-377">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="17661-378">Službu `IWebHostEnvironment` lze také vložit přímo do metody `Configure` pro nastavení kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="17661-378">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="17661-379">`IWebHostEnvironment` lze vložit do metody `Invoke` při vytváření vlastního [middlewaru](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="17661-379">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="17661-380">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="17661-380">IHostingEnvironment interface</span></span>

<span data-ttu-id="17661-381">[Rozhraní IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o prostředí hostování webu aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-381">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="17661-382">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) k získání `IHostingEnvironment`, aby bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="17661-382">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="17661-383">[Přístup založený na konvenci](xref:fundamentals/environments#environment-based-startup-class-and-methods) je možné použít ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="17661-383">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="17661-384">Případně můžete vložit `IHostingEnvironment` do konstruktoru `Startup` pro použití v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="17661-384">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="17661-385">Kromě metody rozšíření `IsDevelopment` `IHostingEnvironment` nabízí `IsStaging`, `IsProduction`a `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="17661-385">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="17661-386">Další informace najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="17661-386">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="17661-387">Službu `IHostingEnvironment` lze také vložit přímo do metody `Configure` pro nastavení kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="17661-387">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="17661-388">`IHostingEnvironment` lze vložit do metody `Invoke` při vytváření vlastního [middlewaru](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="17661-388">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="17661-389">Rozhraní IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="17661-389">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="17661-390">`IHostApplicationLifetime` umožňuje aktivity po spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="17661-390">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="17661-391">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="17661-391">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="17661-392">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="17661-392">Cancellation Token</span></span>    | <span data-ttu-id="17661-393">Aktivováno, když&#8230;</span><span class="sxs-lookup"><span data-stu-id="17661-393">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="17661-394">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="17661-394">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="17661-395">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="17661-395">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="17661-396">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="17661-396">All requests should be processed.</span></span> <span data-ttu-id="17661-397">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="17661-397">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="17661-398">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="17661-398">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="17661-399">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="17661-399">Requests may still be processing.</span></span> <span data-ttu-id="17661-400">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="17661-400">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="17661-401">`StopApplication` žádosti o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-401">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="17661-402">Následující třída používá `StopApplication` k bezproblémovému vypnutí aplikace, když je volána metoda `Shutdown` třídy:</span><span class="sxs-lookup"><span data-stu-id="17661-402">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="17661-403">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="17661-403">IApplicationLifetime interface</span></span>

<span data-ttu-id="17661-404">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje aktivity po spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="17661-404">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="17661-405">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="17661-405">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="17661-406">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="17661-406">Cancellation Token</span></span>    | <span data-ttu-id="17661-407">Aktivováno, když&#8230;</span><span class="sxs-lookup"><span data-stu-id="17661-407">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="17661-408">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="17661-408">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="17661-409">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="17661-409">The host has fully started.</span></span> |
| [<span data-ttu-id="17661-410">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="17661-410">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="17661-411">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="17661-411">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="17661-412">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="17661-412">All requests should be processed.</span></span> <span data-ttu-id="17661-413">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="17661-413">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="17661-414">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="17661-414">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="17661-415">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="17661-415">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="17661-416">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="17661-416">Requests may still be processing.</span></span> <span data-ttu-id="17661-417">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="17661-417">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="17661-418">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) žádá o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-418">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="17661-419">Následující třída používá `StopApplication` k bezproblémovému vypnutí aplikace, když je volána metoda `Shutdown` třídy:</span><span class="sxs-lookup"><span data-stu-id="17661-419">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="17661-420">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="17661-420">Scope validation</span></span>

<span data-ttu-id="17661-421">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) , aby `true`, jestli je prostředí aplikace vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="17661-421">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="17661-422">Pokud je `ValidateScopes` nastaveno na `true`, provede výchozí poskytovatel služeb kontroly, které ověří, že:</span><span class="sxs-lookup"><span data-stu-id="17661-422">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="17661-423">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="17661-423">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="17661-424">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="17661-424">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="17661-425">Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="17661-425">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="17661-426">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-426">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="17661-427">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="17661-427">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="17661-428">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="17661-428">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="17661-429">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="17661-429">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="17661-430">Pokud chcete vždy ověřit obory, včetně v produkčním prostředí, nakonfigurujte [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) pomocí [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrci hostitele:</span><span class="sxs-lookup"><span data-stu-id="17661-430">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="17661-431">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="17661-431">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
