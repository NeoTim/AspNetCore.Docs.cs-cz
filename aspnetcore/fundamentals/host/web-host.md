---
title: ASP.NET Core webového hostitele
author: rick-anderson
description: Přečtěte si o webovém hostiteli v ASP.NET Core, který zodpovídá za správu spouštění a životního cyklu aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: 977c1df67c2775870d630f3a1085d5e19cef58f5
ms.sourcegitcommit: 4115bf0e850c13d4e655beb5ab5e8ff431173cb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981901"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="5ed22-103">ASP.NET Core webového hostitele</span><span class="sxs-lookup"><span data-stu-id="5ed22-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="5ed22-104">ASP.NET Core aplikace nakonfigurují a spouštějí *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="5ed22-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="5ed22-105">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="5ed22-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5ed22-106">Minimálně hostitel nakonfiguruje server a kanál zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="5ed22-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="5ed22-107">Hostitel může také nastavit protokolování, vkládání závislostí a konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="5ed22-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5ed22-108">Tento článek se zabývá webovým hostitelem, který zůstává dostupný jenom pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="5ed22-109">[Obecný hostitel](xref:fundamentals/host/generic-host) je doporučený pro všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="5ed22-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="5ed22-110">Tento článek se zabývá webovým hostitelem, který je určený pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="5ed22-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="5ed22-111">Pro jiné druhy aplikací použijte [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="5ed22-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="5ed22-112">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="5ed22-112">Set up a host</span></span>

<span data-ttu-id="5ed22-113">Vytvořte hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="5ed22-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="5ed22-114">To se obvykle provádí v vstupním bodě aplikace, a to metodou `Main`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="5ed22-115">V šablonách projektu `Main` se nachází v *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5ed22-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="5ed22-116">Typická aplikace volá [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) , aby zahájila nastavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="5ed22-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="5ed22-117">Kód, který volá `CreateDefaultBuilder`, je v metodě s názvem `CreateWebHostBuilder`, která ho odděluje od kódu v `Main`, který volá `Run` na objekt tvůrce.</span><span class="sxs-lookup"><span data-stu-id="5ed22-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="5ed22-118">Toto oddělení se vyžaduje při použití [Entity Framework Corech nástrojů](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="5ed22-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="5ed22-119">Nástroje očekávají, že v době návrhu naleznou metodu `CreateWebHostBuilder`, která může zavolat v době návrhu, aby bylo možné hostitele nakonfigurovat bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="5ed22-120">Alternativou je implementace `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="5ed22-121">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="5ed22-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="5ed22-122">`CreateDefaultBuilder` provádí následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="5ed22-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="5ed22-123">Nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="5ed22-124">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="5ed22-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="5ed22-125">Nastaví kořen obsahu na cestu vrácenou [adresářem. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="5ed22-125">Sets the content root to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="5ed22-126">Načte [konfiguraci hostitele](#host-configuration-values) z:</span><span class="sxs-lookup"><span data-stu-id="5ed22-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="5ed22-127">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="5ed22-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="5ed22-128">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5ed22-128">Command-line arguments.</span></span>
* <span data-ttu-id="5ed22-129">Načte konfiguraci aplikace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="5ed22-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="5ed22-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5ed22-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="5ed22-131">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5ed22-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="5ed22-132">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="5ed22-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="5ed22-133">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-133">Environment variables.</span></span>
  * <span data-ttu-id="5ed22-134">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5ed22-134">Command-line arguments.</span></span>
* <span data-ttu-id="5ed22-135">Konfiguruje [protokolování](xref:fundamentals/logging/index) pro konzolu a výstup ladění.</span><span class="sxs-lookup"><span data-stu-id="5ed22-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="5ed22-136">Protokolování zahrnuje pravidla [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) zadaná v sekci Konfigurace protokolování souboru *appSettings. JSON* nebo *appSettings. { Environment}. JSON* soubor.</span><span class="sxs-lookup"><span data-stu-id="5ed22-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="5ed22-137">Při spuštění za službou IIS s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module)umožňuje `CreateDefaultBuilder` [integraci služby IIS](xref:host-and-deploy/iis/index), která konfiguruje základní adresu a port aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="5ed22-138">Integrace služby IIS také nakonfiguruje aplikaci pro [zachycení chyb při spuštění](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="5ed22-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="5ed22-139">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="5ed22-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="5ed22-140">Nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true`, pokud je prostředí aplikace vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="5ed22-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="5ed22-141">Další informace najdete v tématu [ověřování oboru](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="5ed22-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="5ed22-142">Konfiguraci definovanou pomocí `CreateDefaultBuilder` lze přepsat a rozšířit [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a dalšími metodami a metodami rozšíření [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="5ed22-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="5ed22-143">Následuje několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="5ed22-143">A few examples follow:</span></span>

* <span data-ttu-id="5ed22-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se používá k určení dalších `IConfiguration` pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5ed22-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="5ed22-145">Následující volání `ConfigureAppConfiguration` přidá delegáta pro zahrnutí konfigurace aplikace do souboru *appSettings. XML* .</span><span class="sxs-lookup"><span data-stu-id="5ed22-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="5ed22-146">`ConfigureAppConfiguration` se může volat víckrát.</span><span class="sxs-lookup"><span data-stu-id="5ed22-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="5ed22-147">Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí).</span><span class="sxs-lookup"><span data-stu-id="5ed22-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="5ed22-148">Viz část [hodnoty konfigurace hostitele](#host-configuration-values) .</span><span class="sxs-lookup"><span data-stu-id="5ed22-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="5ed22-149">Následující volání `ConfigureLogging` přidá delegáta pro konfiguraci minimální úrovně protokolování ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="5ed22-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="5ed22-150">Toto nastavení přepisuje nastavení v *appSettings. Development. JSON* (`LogLevel.Debug`) a *appSettings. Produkční. JSON* (`LogLevel.Error`) nakonfigurovaný pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5ed22-151">`ConfigureLogging` se může volat víckrát.</span><span class="sxs-lookup"><span data-stu-id="5ed22-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5ed22-152">Následující volání `ConfigureKestrel` přepíše výchozí [omezení. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) bajtů 30 000 000, které byly vytvořeny při konfiguraci Kestrel pomocí `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5ed22-153">Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepisuje výchozí [omezení. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) z 30 000 000 bajtů zavedených při konfiguraci Kestrel pomocí `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="5ed22-154">*Kořen obsahu* určuje, kde hostitel vyhledává soubory obsahu, například soubory zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="5ed22-154">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="5ed22-155">Při spuštění aplikace z kořenové složky projektu se jako kořen obsahu použije kořenová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="5ed22-156">Toto je výchozí použití v [aplikaci Visual Studio](https://visualstudio.microsoft.com) a [dotnet New Templates](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="5ed22-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="5ed22-157">Další informace o konfiguraci aplikace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5ed22-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="5ed22-158">Alternativou k použití statické metody `CreateDefaultBuilder` je vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) podporovaným přístupem s ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="5ed22-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="5ed22-159">Při nastavování hostitele je možné zadat metody [Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) .</span><span class="sxs-lookup"><span data-stu-id="5ed22-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="5ed22-160">Pokud je zadána třída `Startup`, musí definovat metodu `Configure`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="5ed22-161">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="5ed22-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="5ed22-162">Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání.</span><span class="sxs-lookup"><span data-stu-id="5ed22-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="5ed22-163">Několik volání `Configure` nebo `UseStartup` u `WebHostBuilder` nahrazuje předchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="5ed22-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="5ed22-164">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="5ed22-164">Host configuration values</span></span>

<span data-ttu-id="5ed22-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) spoléhá na následující přístupy k nastavení hodnot konfigurace hostitele:</span><span class="sxs-lookup"><span data-stu-id="5ed22-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="5ed22-166">Konfigurace tvůrce hostitele, což zahrnuje proměnné prostředí ve formátu `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="5ed22-167">Například, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="5ed22-168">Rozšíření jako [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz oddíl pro [přepsání konfigurace](#override-configuration) ).</span><span class="sxs-lookup"><span data-stu-id="5ed22-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="5ed22-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč.</span><span class="sxs-lookup"><span data-stu-id="5ed22-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="5ed22-170">Při nastavení hodnoty s hodnotou `UseSetting` je hodnota nastavena jako řetězec bez ohledu na typ.</span><span class="sxs-lookup"><span data-stu-id="5ed22-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="5ed22-171">Hostitel používá jakoukoli možnost, která hodnotu nastaví jako poslední.</span><span class="sxs-lookup"><span data-stu-id="5ed22-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="5ed22-172">Další informace najdete v tématu [přepsání konfigurace](#override-configuration) v následující části.</span><span class="sxs-lookup"><span data-stu-id="5ed22-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="5ed22-173">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="5ed22-173">Application Key (Name)</span></span>

<span data-ttu-id="5ed22-174">Vlastnost [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="5ed22-174">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="5ed22-175">Hodnota je nastavená na název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="5ed22-176">Chcete-li nastavit hodnotu explicitně, použijte [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="5ed22-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

<span data-ttu-id="5ed22-177">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="5ed22-177">**Key**: applicationName</span></span>  
<span data-ttu-id="5ed22-178">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="5ed22-178">**Type**: *string*</span></span>  
<span data-ttu-id="5ed22-179">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace</span><span class="sxs-lookup"><span data-stu-id="5ed22-179">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="5ed22-180">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5ed22-180">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="5ed22-181">**Proměnná prostředí**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="5ed22-181">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="5ed22-182">Zachytit chyby při spuštění</span><span class="sxs-lookup"><span data-stu-id="5ed22-182">Capture Startup Errors</span></span>

<span data-ttu-id="5ed22-183">Toto nastavení řídí zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5ed22-183">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="5ed22-184">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="5ed22-184">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="5ed22-185">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="5ed22-185">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="5ed22-186">**Výchozí**: Výchozí hodnota je `false`, pokud se aplikace nespustí s Kestrel za IIS, kde výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-186">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="5ed22-187">**Nastavit pomocí**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="5ed22-187">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="5ed22-188">**Proměnná prostředí**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="5ed22-188">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="5ed22-189">Pokud `false`, chyby během spuštění hostitele se ukončí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-189">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="5ed22-190">Při `true` zachytí hostitel při spuštění výjimky a pokusí se spustit server.</span><span class="sxs-lookup"><span data-stu-id="5ed22-190">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="5ed22-191">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="5ed22-191">Content Root</span></span>

<span data-ttu-id="5ed22-192">Toto nastavení určuje, kde ASP.NET Core začít hledat soubory obsahu, například zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="5ed22-192">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="5ed22-193">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="5ed22-193">**Key**: contentRoot</span></span>  
<span data-ttu-id="5ed22-194">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="5ed22-194">**Type**: *string*</span></span>  
<span data-ttu-id="5ed22-195">**Výchozí**: Výchozím nastavením je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-195">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="5ed22-196">**Nastavit pomocí**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="5ed22-196">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="5ed22-197">**Proměnná prostředí**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="5ed22-197">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="5ed22-198">Kořen obsahu se používá také jako základní cesta pro [Nastavení kořenového adresáře webu](#web-root).</span><span class="sxs-lookup"><span data-stu-id="5ed22-198">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="5ed22-199">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="5ed22-199">If the path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a><span data-ttu-id="5ed22-200">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="5ed22-200">Detailed Errors</span></span>

<span data-ttu-id="5ed22-201">Určuje, zda mají být zachyceny podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="5ed22-201">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="5ed22-202">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="5ed22-202">**Key**: detailedErrors</span></span>  
<span data-ttu-id="5ed22-203">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="5ed22-203">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="5ed22-204">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="5ed22-204">**Default**: false</span></span>  
<span data-ttu-id="5ed22-205">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5ed22-205">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="5ed22-206">**Proměnná prostředí**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="5ed22-206">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="5ed22-207">Pokud je tato možnost povolená (nebo když je <a href="#environment">prostředí</a> nastavené na `Development`), aplikace zachycuje podrobné výjimky.</span><span class="sxs-lookup"><span data-stu-id="5ed22-207">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="5ed22-208">Prostředí</span><span class="sxs-lookup"><span data-stu-id="5ed22-208">Environment</span></span>

<span data-ttu-id="5ed22-209">Nastaví prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-209">Sets the app's environment.</span></span>

<span data-ttu-id="5ed22-210">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="5ed22-210">**Key**: environment</span></span>  
<span data-ttu-id="5ed22-211">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="5ed22-211">**Type**: *string*</span></span>  
<span data-ttu-id="5ed22-212">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="5ed22-212">**Default**: Production</span></span>  
<span data-ttu-id="5ed22-213">**Nastavit pomocí**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="5ed22-213">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="5ed22-214">**Proměnná prostředí**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="5ed22-214">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="5ed22-215">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-215">The environment can be set to any value.</span></span> <span data-ttu-id="5ed22-216">Mezi hodnoty definované rozhraním patří `Development`, `Staging` a `Production`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-216">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="5ed22-217">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="5ed22-217">Values aren't case sensitive.</span></span> <span data-ttu-id="5ed22-218">Ve výchozím nastavení je *prostředí* čteno z proměnné prostředí `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-218">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="5ed22-219">Při použití sady [Visual Studio](https://visualstudio.microsoft.com)mohou být proměnné prostředí nastaveny v souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5ed22-219">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="5ed22-220">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5ed22-220">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="5ed22-221">Hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="5ed22-221">Hosting Startup Assemblies</span></span>

<span data-ttu-id="5ed22-222">Nastaví hostování spouštěcích sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-222">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="5ed22-223">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="5ed22-223">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="5ed22-224">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="5ed22-224">**Type**: *string*</span></span>  
<span data-ttu-id="5ed22-225">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="5ed22-225">**Default**: Empty string</span></span>  
<span data-ttu-id="5ed22-226">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5ed22-226">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="5ed22-227">**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5ed22-227">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="5ed22-228">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5ed22-228">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="5ed22-229">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-229">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="5ed22-230">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="5ed22-230">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="5ed22-231">Port HTTPS</span><span class="sxs-lookup"><span data-stu-id="5ed22-231">HTTPS Port</span></span>

<span data-ttu-id="5ed22-232">Nastavte port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5ed22-232">Set the HTTPS redirect port.</span></span> <span data-ttu-id="5ed22-233">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="5ed22-233">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="5ed22-234">**Klíč**: https_port **typ**: *řetězec*
**výchozí**: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="5ed22-234">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="5ed22-235">**Nastavit pomocí**: `UseSetting` @ no__t-1**Proměnná prostředí**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="5ed22-235">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="5ed22-236">Hostování nespouštěcích sestavení pro vyloučení</span><span class="sxs-lookup"><span data-stu-id="5ed22-236">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="5ed22-237">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5ed22-237">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="5ed22-238">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="5ed22-238">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="5ed22-239">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="5ed22-239">**Type**: *string*</span></span>  
<span data-ttu-id="5ed22-240">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="5ed22-240">**Default**: Empty string</span></span>  
<span data-ttu-id="5ed22-241">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5ed22-241">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="5ed22-242">**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="5ed22-242">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="5ed22-243">Preferovat hostování adres URL</span><span class="sxs-lookup"><span data-stu-id="5ed22-243">Prefer Hosting URLs</span></span>

<span data-ttu-id="5ed22-244">Určuje, zda má hostitel naslouchat adresám URL konfigurovaným pomocí `WebHostBuilder` namísto nastavení nakonfigurovaného pomocí implementace `IServer`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-244">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="5ed22-245">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="5ed22-245">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="5ed22-246">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="5ed22-246">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="5ed22-247">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="5ed22-247">**Default**: true</span></span>  
<span data-ttu-id="5ed22-248">**Nastavit pomocí**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="5ed22-248">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="5ed22-249">**Proměnná prostředí**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="5ed22-249">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="5ed22-250">Zabránit spuštění hostování</span><span class="sxs-lookup"><span data-stu-id="5ed22-250">Prevent Hosting Startup</span></span>

<span data-ttu-id="5ed22-251">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-251">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="5ed22-252">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5ed22-252">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="5ed22-253">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="5ed22-253">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="5ed22-254">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="5ed22-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="5ed22-255">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="5ed22-255">**Default**: false</span></span>  
<span data-ttu-id="5ed22-256">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="5ed22-256">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="5ed22-257">**Proměnná prostředí**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="5ed22-257">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="5ed22-258">Adresy URL serveru</span><span class="sxs-lookup"><span data-stu-id="5ed22-258">Server URLs</span></span>

<span data-ttu-id="5ed22-259">Označuje IP adresy nebo adresy hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="5ed22-259">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="5ed22-260">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="5ed22-260">**Key**: urls</span></span>  
<span data-ttu-id="5ed22-261">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="5ed22-261">**Type**: *string*</span></span>  
<span data-ttu-id="5ed22-262">**Výchozí**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="5ed22-262">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="5ed22-263">**Nastavit pomocí**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="5ed22-263">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="5ed22-264">**Proměnná prostředí**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="5ed22-264">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="5ed22-265">Nastavte na středníkem oddělený (;) Seznam předpon adres URL, na které má server odpovědět</span><span class="sxs-lookup"><span data-stu-id="5ed22-265">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="5ed22-266">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-266">For example, `http://localhost:123`.</span></span> <span data-ttu-id="5ed22-267">Pomocí "\*" označíte, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="5ed22-267">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="5ed22-268">Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="5ed22-268">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="5ed22-269">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="5ed22-269">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="5ed22-270">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-270">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="5ed22-271">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5ed22-271">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="5ed22-272">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="5ed22-272">Shutdown Timeout</span></span>

<span data-ttu-id="5ed22-273">Určuje dobu, po kterou se má čekat na vypnutí webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="5ed22-273">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="5ed22-274">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="5ed22-274">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="5ed22-275">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="5ed22-275">**Type**: *int*</span></span>  
<span data-ttu-id="5ed22-276">**Výchozí**: 5</span><span class="sxs-lookup"><span data-stu-id="5ed22-276">**Default**: 5</span></span>  
<span data-ttu-id="5ed22-277">**Nastavit pomocí**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="5ed22-277">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="5ed22-278">**Proměnná prostředí**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="5ed22-278">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="5ed22-279">I když klíč přijímá *int* s `UseSetting` (například `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), metoda rozšíření [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) přebírá [časový interval](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="5ed22-279">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="5ed22-280">Po uplynutí časového limitu hostování:</span><span class="sxs-lookup"><span data-stu-id="5ed22-280">During the timeout period, hosting:</span></span>

* <span data-ttu-id="5ed22-281">Spustí [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="5ed22-281">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="5ed22-282">Pokusy o zastavení hostovaných služeb a protokolování všech chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="5ed22-282">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="5ed22-283">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="5ed22-283">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="5ed22-284">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="5ed22-284">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="5ed22-285">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="5ed22-285">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="5ed22-286">Spouštěcí sestavení</span><span class="sxs-lookup"><span data-stu-id="5ed22-286">Startup Assembly</span></span>

<span data-ttu-id="5ed22-287">Určuje sestavení, ve kterém má být vyhledána třída `Startup`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-287">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="5ed22-288">**Klíč**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="5ed22-288">**Key**: startupAssembly</span></span>  
<span data-ttu-id="5ed22-289">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="5ed22-289">**Type**: *string*</span></span>  
<span data-ttu-id="5ed22-290">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="5ed22-290">**Default**: The app's assembly</span></span>  
<span data-ttu-id="5ed22-291">**Nastavit pomocí**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="5ed22-291">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="5ed22-292">**Proměnná prostředí**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="5ed22-292">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="5ed22-293">Lze odkazovat na sestavení podle názvu (`string`) nebo typu (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="5ed22-293">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="5ed22-294">Je-li voláno více metod `UseStartup`, má poslední přednost.</span><span class="sxs-lookup"><span data-stu-id="5ed22-294">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="5ed22-295">Webový kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="5ed22-295">Web Root</span></span>

<span data-ttu-id="5ed22-296">Nastaví relativní cestu k statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-296">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="5ed22-297">**Klíč**: Webroot</span><span class="sxs-lookup"><span data-stu-id="5ed22-297">**Key**: webroot</span></span>  
<span data-ttu-id="5ed22-298">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="5ed22-298">**Type**: *string*</span></span>  
<span data-ttu-id="5ed22-299">**Výchozí**: Pokud tento parametr nezadáte, výchozí hodnota je "(kořen obsahu)/wwwroot", pokud cesta existuje.</span><span class="sxs-lookup"><span data-stu-id="5ed22-299">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="5ed22-300">Pokud cesta neexistuje, použije se zprostředkovatel souboru no-op.</span><span class="sxs-lookup"><span data-stu-id="5ed22-300">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="5ed22-301">**Nastavit pomocí**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="5ed22-301">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="5ed22-302">**Proměnná prostředí**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="5ed22-302">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a><span data-ttu-id="5ed22-303">Přepsat konfiguraci</span><span class="sxs-lookup"><span data-stu-id="5ed22-303">Override configuration</span></span>

<span data-ttu-id="5ed22-304">Pro konfiguraci webového hostitele použijte [konfiguraci](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="5ed22-304">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="5ed22-305">V následujícím příkladu je konfigurace hostitele volitelně určena v souboru *HostSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="5ed22-305">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="5ed22-306">Jakékoli konfigurace načtené ze souboru *HostSettings. JSON* může přepsat argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5ed22-306">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="5ed22-307">Vytvořená konfigurace (v `config`) se používá ke konfiguraci hostitele pomocí [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="5ed22-307">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="5ed22-308">do konfigurace aplikace se přidala konfigurace `IWebHostBuilder`, ale tato konverzace není pravdivá @ no__t-1 @ no__t-2 nemá vliv na konfiguraci `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-308">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="5ed22-309">Přepsání konfigurace, kterou poskytuje `UseUrls` s první konfigurací *HostSettings. JSON* a konfigurací argumentu příkazového řádku (v sekundách):</span><span class="sxs-lookup"><span data-stu-id="5ed22-309">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="5ed22-310">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5ed22-310">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="5ed22-311">Metoda rozšíření [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) momentálně není schopná analyzovat konfigurační oddíl vrácený `GetSection` (například `.UseConfiguration(Configuration.GetSection("section"))`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-311">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="5ed22-312">Metoda `GetSection` filtruje konfigurační klíče k požadované části, ale ponechá název oddílu na klíčích (například `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="5ed22-312">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="5ed22-313">Metoda `UseConfiguration` očekává, že klíče budou odpovídat klíčům `WebHostBuilder` (například `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="5ed22-313">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="5ed22-314">Přítomnost názvu oddílu u klíčů brání hodnotám oddílu v konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="5ed22-314">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="5ed22-315">Tento problém bude vyřešen v příští verzi.</span><span class="sxs-lookup"><span data-stu-id="5ed22-315">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="5ed22-316">Další informace a alternativní řešení naleznete v [části předání konfiguračního oddílu do WebHostBuilder. UseConfiguration používá úplné klíče](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="5ed22-316">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="5ed22-317">`UseConfiguration` zkopíruje pouze klíče z poskytnuté `IConfiguration` do konfigurace Host Builder.</span><span class="sxs-lookup"><span data-stu-id="5ed22-317">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="5ed22-318">Proto nastavení `reloadOnChange: true` pro soubory JSON, INI a nastavení XML nemá žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="5ed22-318">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="5ed22-319">Chcete-li určit spuštění hostitele na konkrétní adrese URL, lze požadovanou hodnotu předat z příkazového řádku při [spuštění příkazu dotnet](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="5ed22-319">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="5ed22-320">Argument příkazového řádku přepíše hodnotu `urls` ze souboru *HostSettings. JSON* a server naslouchá na portu 8080:</span><span class="sxs-lookup"><span data-stu-id="5ed22-320">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="5ed22-321">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="5ed22-321">Manage the host</span></span>

<span data-ttu-id="5ed22-322">**Spuštění**</span><span class="sxs-lookup"><span data-stu-id="5ed22-322">**Run**</span></span>

<span data-ttu-id="5ed22-323">Metoda `Run` spustí webovou aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="5ed22-323">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="5ed22-324">**Start**</span><span class="sxs-lookup"><span data-stu-id="5ed22-324">**Start**</span></span>

<span data-ttu-id="5ed22-325">Spusťte hostitele neblokujícím způsobem voláním jeho metody `Start`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-325">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="5ed22-326">Pokud je seznam adres URL předán metodě `Start`, naslouchá na zadaných adresách URL:</span><span class="sxs-lookup"><span data-stu-id="5ed22-326">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="5ed22-327">Aplikace může inicializovat a spustit nového hostitele pomocí předem nakonfigurovaných výchozích hodnot `CreateDefaultBuilder` pomocí metody statického usnadnění.</span><span class="sxs-lookup"><span data-stu-id="5ed22-327">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="5ed22-328">Tyto metody spustí server bez výstupu konzoly a s [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) čekají na přerušení (CTRL-C/SIGINT nebo SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="5ed22-328">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="5ed22-329">**Spustit (aplikace RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="5ed22-329">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="5ed22-330">Začněte s `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-330">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="5ed22-331">V prohlížeči vytvořte žádost, aby `http://localhost:5000`, aby se zobrazila odpověď "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="5ed22-331">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="5ed22-332">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="5ed22-332">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="5ed22-333">Aplikace zobrazí zprávu `Console.WriteLine` a počká, až se příkaz ukončí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-333">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="5ed22-334">**Spustit (adresa URL řetězce, aplikace v RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="5ed22-334">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="5ed22-335">Začněte s adresou URL a `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-335">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="5ed22-336">Vytvoří stejný výsledek jako **Start (aplikace RequestDelegate)** , s výjimkou, že aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-336">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="5ed22-337">**Spustit (akce @ no__t-1IRouteBuilder @ no__t-2 routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="5ed22-337">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="5ed22-338">Použijte instanci `IRouteBuilder` ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) pro použití middlewaru pro směrování:</span><span class="sxs-lookup"><span data-stu-id="5ed22-338">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="5ed22-339">Použijte následující požadavky prohlížeče s příkladem:</span><span class="sxs-lookup"><span data-stu-id="5ed22-339">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="5ed22-340">Žádost</span><span class="sxs-lookup"><span data-stu-id="5ed22-340">Request</span></span>                                    | <span data-ttu-id="5ed22-341">Odpověď</span><span class="sxs-lookup"><span data-stu-id="5ed22-341">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="5ed22-342">Hello, Novák!</span><span class="sxs-lookup"><span data-stu-id="5ed22-342">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="5ed22-343">Buenos Dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="5ed22-343">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="5ed22-344">Vyvolá výjimku s řetězcem "Ooops!"</span><span class="sxs-lookup"><span data-stu-id="5ed22-344">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="5ed22-345">Vyvolá výjimku s řetězcem "uh Oh!".</span><span class="sxs-lookup"><span data-stu-id="5ed22-345">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="5ed22-346">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="5ed22-346">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="5ed22-347">Ahoj světe!</span><span class="sxs-lookup"><span data-stu-id="5ed22-347">Hello World!</span></span>                             |

<span data-ttu-id="5ed22-348">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="5ed22-348">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="5ed22-349">Aplikace zobrazí zprávu `Console.WriteLine` a počká, až se příkaz ukončí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-349">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="5ed22-350">**Start (adresa URL řetězce, akce @ no__t-1IRouteBuilder @ no__t-2 routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="5ed22-350">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="5ed22-351">Použijte adresu URL a instanci `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-351">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="5ed22-352">Vytvoří stejný výsledek jako **Start (Action @ no__t-1IRouteBuilder @ no__t-2 routeBuilder)** , s výjimkou toho, že aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-352">Produces the same result as **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="5ed22-353">**StartWith (akce @ no__t-1IApplicationBuilder @ no__t-2 aplikace)**</span><span class="sxs-lookup"><span data-stu-id="5ed22-353">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="5ed22-354">Zadejte delegáta pro konfiguraci `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-354">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="5ed22-355">V prohlížeči vytvořte žádost, aby `http://localhost:5000`, aby se zobrazila odpověď "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="5ed22-355">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="5ed22-356">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="5ed22-356">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="5ed22-357">Aplikace zobrazí zprávu `Console.WriteLine` a počká, až se příkaz ukončí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-357">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="5ed22-358">**StartWith (adresa URL řetězce, akce @ no__t-1IApplicationBuilder @ no__t-2)**</span><span class="sxs-lookup"><span data-stu-id="5ed22-358">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="5ed22-359">Zadejte adresu URL a delegáta pro konfiguraci `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-359">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="5ed22-360">Vytvoří stejný výsledek jako **StartWith (Action @ no__t-1IApplicationBuilder @ no__t-2)** , s výjimkou toho, že aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-360">Produces the same result as **StartWith(Action&lt;IApplicationBuilder&gt; app)**, except the app responds on `http://localhost:8080`.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="5ed22-361">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="5ed22-361">IHostingEnvironment interface</span></span>

<span data-ttu-id="5ed22-362">[Rozhraní IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o prostředí hostování webu aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-362">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="5ed22-363">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání `IHostingEnvironment`, aby bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="5ed22-363">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="5ed22-364">[Přístup založený na konvenci](xref:fundamentals/environments#environment-based-startup-class-and-methods) je možné použít ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-364">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="5ed22-365">Případně můžete vložit `IHostingEnvironment` do konstruktoru `Startup` pro použití v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-365">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="5ed22-366">Kromě metody rozšíření `IsDevelopment` nabízí `IHostingEnvironment` metody `IsStaging`, `IsProduction` a `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-366">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="5ed22-367">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5ed22-367">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5ed22-368">Službu `IHostingEnvironment` lze také vložit přímo do metody `Configure` pro nastavení kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="5ed22-368">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="5ed22-369">`IHostingEnvironment` se dá vložit do metody `Invoke` při vytváření vlastního [middlewaru](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="5ed22-369">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="5ed22-370">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="5ed22-370">IApplicationLifetime interface</span></span>

<span data-ttu-id="5ed22-371">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje aktivity po spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-371">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="5ed22-372">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci metod @no__t 0, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-372">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="5ed22-373">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="5ed22-373">Cancellation Token</span></span>    | <span data-ttu-id="5ed22-374">Aktivováno, když&#8230;</span><span class="sxs-lookup"><span data-stu-id="5ed22-374">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="5ed22-375">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="5ed22-375">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="5ed22-376">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="5ed22-376">The host has fully started.</span></span> |
| [<span data-ttu-id="5ed22-377">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="5ed22-377">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="5ed22-378">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-378">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="5ed22-379">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="5ed22-379">All requests should be processed.</span></span> <span data-ttu-id="5ed22-380">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-380">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="5ed22-381">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="5ed22-381">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="5ed22-382">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-382">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="5ed22-383">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="5ed22-383">Requests may still be processing.</span></span> <span data-ttu-id="5ed22-384">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-384">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="5ed22-385">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) žádá o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-385">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="5ed22-386">Následující třída používá `StopApplication` k bezproblémovému vypnutí aplikace, když je volána metoda `Shutdown` třídy:</span><span class="sxs-lookup"><span data-stu-id="5ed22-386">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="5ed22-387">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="5ed22-387">Scope validation</span></span>

<span data-ttu-id="5ed22-388">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true`, pokud je prostředí aplikace vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="5ed22-388">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="5ed22-389">Pokud je `ValidateScopes` nastavená na `true`, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="5ed22-389">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="5ed22-390">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="5ed22-390">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="5ed22-391">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="5ed22-391">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="5ed22-392">Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="5ed22-392">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="5ed22-393">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-393">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="5ed22-394">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="5ed22-394">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="5ed22-395">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-395">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="5ed22-396">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-396">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="5ed22-397">Pokud chcete vždy ověřit obory, včetně v produkčním prostředí, nakonfigurujte [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) pomocí [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrci hostitele:</span><span class="sxs-lookup"><span data-stu-id="5ed22-397">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="5ed22-398">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5ed22-398">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
