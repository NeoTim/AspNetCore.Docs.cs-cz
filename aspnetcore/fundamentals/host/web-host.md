---
title: ASP.NET Core webového hostitele
author: guardrex
description: Přečtěte si o webovém hostiteli v ASP.NET Core, který zodpovídá za správu spouštění a životního cyklu aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: d387098662cc832cc0e49b6a1636f0ebcc7308de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081693"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="edbd3-103">ASP.NET Core webového hostitele</span><span class="sxs-lookup"><span data-stu-id="edbd3-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="edbd3-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="edbd3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="edbd3-105">ASP.NET Core aplikace nakonfigurují a spouštějí *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="edbd3-105">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="edbd3-106">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="edbd3-106">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="edbd3-107">Minimálně hostitel nakonfiguruje server a kanál zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="edbd3-107">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="edbd3-108">Hostitel může také nastavit protokolování, vkládání závislostí a konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="edbd3-108">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="edbd3-109">Tento článek se zabývá webovým hostitelem, který zůstává dostupný jenom pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="edbd3-109">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="edbd3-110">[Obecný hostitel](xref:fundamentals/host/generic-host) je doporučený pro všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="edbd3-110">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="edbd3-111">Tento článek se zabývá webovým hostitelem, který je určený pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="edbd3-111">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="edbd3-112">Pro jiné druhy aplikací použijte [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="edbd3-112">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="edbd3-113">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="edbd3-113">Set up a host</span></span>

<span data-ttu-id="edbd3-114">Vytvořte hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="edbd3-114">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="edbd3-115">To se obvykle provádí v vstupním bodě aplikace, `Main` metodě.</span><span class="sxs-lookup"><span data-stu-id="edbd3-115">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="edbd3-116">V šablonách `Main` projektů se nachází v *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="edbd3-116">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="edbd3-117">Typická aplikace volá [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) , aby zahájila nastavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="edbd3-117">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="edbd3-118">Kód, který volá `CreateDefaultBuilder` , je v metodě s `CreateWebHostBuilder`názvem, která ho odděluje od kódu v `Main` volání `Run` objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="edbd3-118">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="edbd3-119">Toto oddělení se vyžaduje při použití [Entity Framework Corech nástrojů](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="edbd3-119">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="edbd3-120">Nástroje očekávají `CreateWebHostBuilder` , že vyvolají metodu, kterou můžou zavolat v době návrhu, aby se nakonfiguroval hostitel bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-120">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="edbd3-121">Alternativou je implementace `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="edbd3-121">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="edbd3-122">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="edbd3-122">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="edbd3-123">`CreateDefaultBuilder`provádí následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="edbd3-123">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="edbd3-124">Nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-124">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="edbd3-125">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="edbd3-125">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="edbd3-126">Nastaví kořen obsahu na cestu vrácenou [adresářem. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="edbd3-126">Sets the content root to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="edbd3-127">Načte [konfiguraci hostitele](#host-configuration-values) z:</span><span class="sxs-lookup"><span data-stu-id="edbd3-127">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="edbd3-128">Proměnné prostředí s `ASPNETCORE_` předponou ( `ASPNETCORE_ENVIRONMENT`například).</span><span class="sxs-lookup"><span data-stu-id="edbd3-128">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="edbd3-129">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="edbd3-129">Command-line arguments.</span></span>
* <span data-ttu-id="edbd3-130">Načte konfiguraci aplikace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="edbd3-130">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="edbd3-131">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="edbd3-131">*appsettings.json*.</span></span>
  * <span data-ttu-id="edbd3-132">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="edbd3-132">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="edbd3-133">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží `Development` v prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="edbd3-133">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="edbd3-134">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-134">Environment variables.</span></span>
  * <span data-ttu-id="edbd3-135">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="edbd3-135">Command-line arguments.</span></span>
* <span data-ttu-id="edbd3-136">Konfiguruje [protokolování](xref:fundamentals/logging/index) pro konzolu a výstup ladění.</span><span class="sxs-lookup"><span data-stu-id="edbd3-136">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="edbd3-137">Protokolování zahrnuje pravidla [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) zadaná v sekci Konfigurace protokolování souboru *appSettings. JSON* nebo *appSettings. { Environment}. JSON* soubor.</span><span class="sxs-lookup"><span data-stu-id="edbd3-137">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="edbd3-138">Při spuštění za službou IIS `CreateDefaultBuilder` s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module)umožňuje [integraci služby IIS](xref:host-and-deploy/iis/index), která konfiguruje základní adresu a port aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-138">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="edbd3-139">Integrace služby IIS také nakonfiguruje aplikaci pro [zachycení chyb při spuštění](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="edbd3-139">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="edbd3-140">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="edbd3-140">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="edbd3-141">Nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` , pokud je prostředí aplikace vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="edbd3-141">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="edbd3-142">Další informace najdete v tématu [ověřování oboru](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="edbd3-142">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="edbd3-143">`CreateDefaultBuilder` Konfiguraci definovanou pomocí lze přepsat a rozšířit [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a dalšími metodami a metodami rozšíření [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="edbd3-143">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="edbd3-144">Následuje několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="edbd3-144">A few examples follow:</span></span>

* <span data-ttu-id="edbd3-145">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se používá k určení dalších `IConfiguration` aplikací.</span><span class="sxs-lookup"><span data-stu-id="edbd3-145">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="edbd3-146">Následující `ConfigureAppConfiguration` volání přidá delegáta pro zahrnutí konfigurace aplikace do souboru *appSettings. XML* .</span><span class="sxs-lookup"><span data-stu-id="edbd3-146">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="edbd3-147">`ConfigureAppConfiguration`může být volána několikrát.</span><span class="sxs-lookup"><span data-stu-id="edbd3-147">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="edbd3-148">Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí).</span><span class="sxs-lookup"><span data-stu-id="edbd3-148">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="edbd3-149">Viz část [hodnoty konfigurace hostitele](#host-configuration-values) .</span><span class="sxs-lookup"><span data-stu-id="edbd3-149">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="edbd3-150">Následující `ConfigureLogging` volání přidá delegáta pro konfiguraci minimální úrovně protokolování ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="edbd3-150">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="edbd3-151">Toto nastavení přepisuje nastavení v *appSettings. Development. JSON* (`LogLevel.Debug`) a *appSettings. Produkční. JSON* (`LogLevel.Error`) nakonfigurovaný pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="edbd3-151">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="edbd3-152">`ConfigureLogging`může být volána několikrát.</span><span class="sxs-lookup"><span data-stu-id="edbd3-152">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="edbd3-153">Následující volání `ConfigureKestrel` přepíše výchozí [omezení. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtů zavedených při konfiguraci `CreateDefaultBuilder`Kestrel:</span><span class="sxs-lookup"><span data-stu-id="edbd3-153">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="edbd3-154">Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepisuje výchozí [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) z 30 000 000 bajtů zavedených při konfiguraci `CreateDefaultBuilder`Kestrel:</span><span class="sxs-lookup"><span data-stu-id="edbd3-154">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="edbd3-155">*Kořen obsahu* určuje, kde hostitel vyhledává soubory obsahu, například soubory zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="edbd3-155">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="edbd3-156">Při spuštění aplikace z kořenové složky projektu se jako kořen obsahu použije kořenová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="edbd3-156">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="edbd3-157">Toto je výchozí použití v [aplikaci Visual Studio](https://visualstudio.microsoft.com) a [dotnet New Templates](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="edbd3-157">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="edbd3-158">Další informace o konfiguraci aplikace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="edbd3-158">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="edbd3-159">Jako alternativu k použití statické `CreateDefaultBuilder` metody je vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) podporovaným přístupem s ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="edbd3-159">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="edbd3-160">Při nastavování hostitele je možné zadat metody [Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) .</span><span class="sxs-lookup"><span data-stu-id="edbd3-160">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="edbd3-161">`Configure` Pokud je `Startup` třída zadána, musí definovat metodu.</span><span class="sxs-lookup"><span data-stu-id="edbd3-161">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="edbd3-162">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="edbd3-162">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="edbd3-163">Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání.</span><span class="sxs-lookup"><span data-stu-id="edbd3-163">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="edbd3-164">Vícenásobné volání `Configure` nebo `UseStartup` v `WebHostBuilder` nastavení nahradit předchozí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-164">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="edbd3-165">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="edbd3-165">Host configuration values</span></span>

<span data-ttu-id="edbd3-166">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) spoléhá na následující přístupy k nastavení hodnot konfigurace hostitele:</span><span class="sxs-lookup"><span data-stu-id="edbd3-166">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="edbd3-167">Konfigurace tvůrce hostitele, což zahrnuje proměnné prostředí ve formátu `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="edbd3-167">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="edbd3-168">Například, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="edbd3-168">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="edbd3-169">Rozšíření jako [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz oddíl pro [přepsání konfigurace](#override-configuration) ).</span><span class="sxs-lookup"><span data-stu-id="edbd3-169">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="edbd3-170">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč.</span><span class="sxs-lookup"><span data-stu-id="edbd3-170">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="edbd3-171">Při nastavení hodnoty s `UseSetting`hodnotou je hodnota nastavena jako řetězec bez ohledu na typ.</span><span class="sxs-lookup"><span data-stu-id="edbd3-171">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="edbd3-172">Hostitel používá jakoukoli možnost, která hodnotu nastaví jako poslední.</span><span class="sxs-lookup"><span data-stu-id="edbd3-172">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="edbd3-173">Další informace najdete v tématu [přepsání konfigurace](#override-configuration) v následující části.</span><span class="sxs-lookup"><span data-stu-id="edbd3-173">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="edbd3-174">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="edbd3-174">Application Key (Name)</span></span>

<span data-ttu-id="edbd3-175">Vlastnost [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="edbd3-175">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="edbd3-176">Hodnota je nastavená na název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-176">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="edbd3-177">Chcete-li nastavit hodnotu explicitně, použijte [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="edbd3-177">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

<span data-ttu-id="edbd3-178">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="edbd3-178">**Key**: applicationName</span></span>  
<span data-ttu-id="edbd3-179">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="edbd3-179">**Type**: *string*</span></span>  
<span data-ttu-id="edbd3-180">**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace</span><span class="sxs-lookup"><span data-stu-id="edbd3-180">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="edbd3-181">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="edbd3-181">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="edbd3-182">**Proměnná prostředí**:`ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="edbd3-182">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="edbd3-183">Zachytit chyby při spuštění</span><span class="sxs-lookup"><span data-stu-id="edbd3-183">Capture Startup Errors</span></span>

<span data-ttu-id="edbd3-184">Toto nastavení řídí zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="edbd3-184">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="edbd3-185">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="edbd3-185">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="edbd3-186">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="edbd3-186">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="edbd3-187">**Výchozí**: Použije se výchozí hodnota, `true` PokudseaplikacenespustísKestrelzaIIS,kdejevýchozíhodnota.`false`</span><span class="sxs-lookup"><span data-stu-id="edbd3-187">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="edbd3-188">**Nastavit pomocí**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="edbd3-188">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="edbd3-189">**Proměnná prostředí**:`ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="edbd3-189">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="edbd3-190">V `false`důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="edbd3-190">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="edbd3-191">Když `true`hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="edbd3-191">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="edbd3-192">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="edbd3-192">Content Root</span></span>

<span data-ttu-id="edbd3-193">Toto nastavení určuje, kde ASP.NET Core začít hledat soubory obsahu, například zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="edbd3-193">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="edbd3-194">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="edbd3-194">**Key**: contentRoot</span></span>  
<span data-ttu-id="edbd3-195">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="edbd3-195">**Type**: *string*</span></span>  
<span data-ttu-id="edbd3-196">**Výchozí**: Výchozím nastavením je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-196">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="edbd3-197">**Nastavit pomocí**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="edbd3-197">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="edbd3-198">**Proměnná prostředí**:`ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="edbd3-198">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="edbd3-199">Kořen obsahu se používá také jako základní cesta pro [Nastavení kořenového adresáře webu](#web-root).</span><span class="sxs-lookup"><span data-stu-id="edbd3-199">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="edbd3-200">Pokud cesta neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="edbd3-200">If the path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a><span data-ttu-id="edbd3-201">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="edbd3-201">Detailed Errors</span></span>

<span data-ttu-id="edbd3-202">Určuje, zda mají být zachyceny podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="edbd3-202">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="edbd3-203">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="edbd3-203">**Key**: detailedErrors</span></span>  
<span data-ttu-id="edbd3-204">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="edbd3-204">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="edbd3-205">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="edbd3-205">**Default**: false</span></span>  
<span data-ttu-id="edbd3-206">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="edbd3-206">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="edbd3-207">**Proměnná prostředí**:`ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="edbd3-207">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="edbd3-208">Když je tato možnost povolená <a href="#environment"></a> (nebo když je `Development`prostředí nastavené na), aplikace zachycuje podrobné výjimky.</span><span class="sxs-lookup"><span data-stu-id="edbd3-208">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="edbd3-209">Prostředí</span><span class="sxs-lookup"><span data-stu-id="edbd3-209">Environment</span></span>

<span data-ttu-id="edbd3-210">Nastaví prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-210">Sets the app's environment.</span></span>

<span data-ttu-id="edbd3-211">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="edbd3-211">**Key**: environment</span></span>  
<span data-ttu-id="edbd3-212">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="edbd3-212">**Type**: *string*</span></span>  
<span data-ttu-id="edbd3-213">**Výchozí**: Produkční</span><span class="sxs-lookup"><span data-stu-id="edbd3-213">**Default**: Production</span></span>  
<span data-ttu-id="edbd3-214">**Nastavit pomocí**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="edbd3-214">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="edbd3-215">**Proměnná prostředí**:`ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="edbd3-215">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="edbd3-216">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="edbd3-216">The environment can be set to any value.</span></span> <span data-ttu-id="edbd3-217">Hodnoty definované rozhraním zahrnují `Development`, `Staging`a `Production`.</span><span class="sxs-lookup"><span data-stu-id="edbd3-217">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="edbd3-218">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="edbd3-218">Values aren't case sensitive.</span></span> <span data-ttu-id="edbd3-219">Ve výchozím nastavení je *prostředí* načteno z `ASPNETCORE_ENVIRONMENT` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-219">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="edbd3-220">Při použití sady [Visual Studio](https://visualstudio.microsoft.com)mohou být proměnné prostředí nastaveny v souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="edbd3-220">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="edbd3-221">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="edbd3-221">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="edbd3-222">Hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="edbd3-222">Hosting Startup Assemblies</span></span>

<span data-ttu-id="edbd3-223">Nastaví hostování spouštěcích sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-223">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="edbd3-224">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="edbd3-224">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="edbd3-225">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="edbd3-225">**Type**: *string*</span></span>  
<span data-ttu-id="edbd3-226">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="edbd3-226">**Default**: Empty string</span></span>  
<span data-ttu-id="edbd3-227">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="edbd3-227">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="edbd3-228">**Proměnná prostředí**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="edbd3-228">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="edbd3-229">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="edbd3-229">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="edbd3-230">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-230">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="edbd3-231">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="edbd3-231">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="edbd3-232">Port HTTPS</span><span class="sxs-lookup"><span data-stu-id="edbd3-232">HTTPS Port</span></span>

<span data-ttu-id="edbd3-233">Nastavte port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="edbd3-233">Set the HTTPS redirect port.</span></span> <span data-ttu-id="edbd3-234">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="edbd3-234">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="edbd3-235">**Klíč**: https_port **typ**:**Výchozí hodnota** *řetězce*
: Výchozí hodnota není nastavena.</span><span class="sxs-lookup"><span data-stu-id="edbd3-235">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="edbd3-236">**Nastavit pomocí**: `UseSetting`
**Proměnná prostředí**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="edbd3-236">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="edbd3-237">Hostování nespouštěcích sestavení pro vyloučení</span><span class="sxs-lookup"><span data-stu-id="edbd3-237">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="edbd3-238">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="edbd3-238">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="edbd3-239">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="edbd3-239">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="edbd3-240">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="edbd3-240">**Type**: *string*</span></span>  
<span data-ttu-id="edbd3-241">**Výchozí**: Prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="edbd3-241">**Default**: Empty string</span></span>  
<span data-ttu-id="edbd3-242">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="edbd3-242">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="edbd3-243">**Proměnná prostředí**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="edbd3-243">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="edbd3-244">Preferovat hostování adres URL</span><span class="sxs-lookup"><span data-stu-id="edbd3-244">Prefer Hosting URLs</span></span>

<span data-ttu-id="edbd3-245">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným `WebHostBuilder` pomocí a místo nakonfigurovaných `IServer` s implementací.</span><span class="sxs-lookup"><span data-stu-id="edbd3-245">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="edbd3-246">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="edbd3-246">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="edbd3-247">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="edbd3-247">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="edbd3-248">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="edbd3-248">**Default**: true</span></span>  
<span data-ttu-id="edbd3-249">**Nastavit pomocí**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="edbd3-249">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="edbd3-250">**Proměnná prostředí**:`ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="edbd3-250">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="edbd3-251">Zabránit spuštění hostování</span><span class="sxs-lookup"><span data-stu-id="edbd3-251">Prevent Hosting Startup</span></span>

<span data-ttu-id="edbd3-252">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-252">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="edbd3-253">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="edbd3-253">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="edbd3-254">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="edbd3-254">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="edbd3-255">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="edbd3-255">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="edbd3-256">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="edbd3-256">**Default**: false</span></span>  
<span data-ttu-id="edbd3-257">**Nastavit pomocí**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="edbd3-257">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="edbd3-258">**Proměnná prostředí**:`ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="edbd3-258">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="edbd3-259">Adresy URL serveru</span><span class="sxs-lookup"><span data-stu-id="edbd3-259">Server URLs</span></span>

<span data-ttu-id="edbd3-260">Označuje IP adresy nebo adresy hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="edbd3-260">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="edbd3-261">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="edbd3-261">**Key**: urls</span></span>  
<span data-ttu-id="edbd3-262">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="edbd3-262">**Type**: *string*</span></span>  
<span data-ttu-id="edbd3-263">**Výchozí**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="edbd3-263">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="edbd3-264">**Nastavit pomocí**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="edbd3-264">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="edbd3-265">**Proměnná prostředí**:`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="edbd3-265">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="edbd3-266">Nastavte na středníkem oddělený (;) Seznam předpon adres URL, na které má server odpovědět</span><span class="sxs-lookup"><span data-stu-id="edbd3-266">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="edbd3-267">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="edbd3-267">For example, `http://localhost:123`.</span></span> <span data-ttu-id="edbd3-268">Pomocí příkazu\*"" určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu ( `http://*:5000`například).</span><span class="sxs-lookup"><span data-stu-id="edbd3-268">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="edbd3-269">Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="edbd3-269">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="edbd3-270">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="edbd3-270">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="edbd3-271">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="edbd3-271">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="edbd3-272">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="edbd3-272">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="edbd3-273">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="edbd3-273">Shutdown Timeout</span></span>

<span data-ttu-id="edbd3-274">Určuje dobu, po kterou se má čekat na vypnutí webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="edbd3-274">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="edbd3-275">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="edbd3-275">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="edbd3-276">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="edbd3-276">**Type**: *int*</span></span>  
<span data-ttu-id="edbd3-277">**Výchozí**: 5</span><span class="sxs-lookup"><span data-stu-id="edbd3-277">**Default**: 5</span></span>  
<span data-ttu-id="edbd3-278">**Nastavit pomocí**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="edbd3-278">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="edbd3-279">**Proměnná prostředí**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="edbd3-279">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="edbd3-280">I když klíč přijímá *int* `UseSetting` s `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`(například), metoda rozšíření [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) přebírá [časový interval](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="edbd3-280">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="edbd3-281">Po uplynutí časového limitu hostování:</span><span class="sxs-lookup"><span data-stu-id="edbd3-281">During the timeout period, hosting:</span></span>

* <span data-ttu-id="edbd3-282">Spustí [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="edbd3-282">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="edbd3-283">Pokusy o zastavení hostovaných služeb a protokolování všech chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="edbd3-283">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="edbd3-284">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="edbd3-284">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="edbd3-285">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="edbd3-285">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="edbd3-286">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="edbd3-286">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="edbd3-287">Spouštěcí sestavení</span><span class="sxs-lookup"><span data-stu-id="edbd3-287">Startup Assembly</span></span>

<span data-ttu-id="edbd3-288">Určuje sestavení, ve kterém má být `Startup` vyhledána třída.</span><span class="sxs-lookup"><span data-stu-id="edbd3-288">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="edbd3-289">**Klíč**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="edbd3-289">**Key**: startupAssembly</span></span>  
<span data-ttu-id="edbd3-290">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="edbd3-290">**Type**: *string*</span></span>  
<span data-ttu-id="edbd3-291">**Výchozí**: Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="edbd3-291">**Default**: The app's assembly</span></span>  
<span data-ttu-id="edbd3-292">**Nastavit pomocí**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="edbd3-292">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="edbd3-293">**Proměnná prostředí**:`ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="edbd3-293">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="edbd3-294">Lze odkazovat na sestavení podle`string`názvu () nebo`TStartup`typu ().</span><span class="sxs-lookup"><span data-stu-id="edbd3-294">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="edbd3-295">Pokud je `UseStartup` voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="edbd3-295">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="edbd3-296">Webový kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="edbd3-296">Web Root</span></span>

<span data-ttu-id="edbd3-297">Nastaví relativní cestu k statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-297">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="edbd3-298">**Klíč**: Webroot</span><span class="sxs-lookup"><span data-stu-id="edbd3-298">**Key**: webroot</span></span>  
<span data-ttu-id="edbd3-299">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="edbd3-299">**Type**: *string*</span></span>  
<span data-ttu-id="edbd3-300">**Výchozí**: Pokud tento parametr nezadáte, výchozí hodnota je "(kořen obsahu)/wwwroot", pokud cesta existuje.</span><span class="sxs-lookup"><span data-stu-id="edbd3-300">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="edbd3-301">Pokud cesta neexistuje, použije se zprostředkovatel souboru no-op.</span><span class="sxs-lookup"><span data-stu-id="edbd3-301">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="edbd3-302">**Nastavit pomocí**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="edbd3-302">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="edbd3-303">**Proměnná prostředí**:`ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="edbd3-303">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a><span data-ttu-id="edbd3-304">Přepsat konfiguraci</span><span class="sxs-lookup"><span data-stu-id="edbd3-304">Override configuration</span></span>

<span data-ttu-id="edbd3-305">Pro konfiguraci webového hostitele použijte [konfiguraci](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="edbd3-305">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="edbd3-306">V následujícím příkladu je konfigurace hostitele volitelně určena v souboru *HostSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="edbd3-306">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="edbd3-307">Jakékoli konfigurace načtené ze souboru *HostSettings. JSON* může přepsat argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="edbd3-307">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="edbd3-308">Vytvořená konfigurace (v `config`) se používá ke konfiguraci hostitele pomocí [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="edbd3-308">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="edbd3-309">`IWebHostBuilder`konfigurace je přidána do konfigurace aplikace, ale tato konverzace není pravdivá&mdash; `ConfigureAppConfiguration` , nemá vliv na `IWebHostBuilder` konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="edbd3-309">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="edbd3-310">Přepsání konfigurace, kterou `UseUrls` poskytuje, pomocí konfigurace *HostSettings. JSON* First, druhé konfigurace argumentů příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="edbd3-310">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="edbd3-311">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="edbd3-311">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="edbd3-312">Metoda rozšíření [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) momentálně není schopná analyzovat konfigurační oddíl, který vrácený `GetSection` `.UseConfiguration(Configuration.GetSection("section"))`(například.</span><span class="sxs-lookup"><span data-stu-id="edbd3-312">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="edbd3-313">Metoda filtruje konfigurační klíče na požadovanou část, ale ponechá název oddílu na klíčích ( `section:urls`například, `section:environment`). `GetSection`</span><span class="sxs-lookup"><span data-stu-id="edbd3-313">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="edbd3-314">Metoda očekává, že klíče budou `WebHostBuilder` odpovídat `urls`klíčům (například, `environment`). `UseConfiguration`</span><span class="sxs-lookup"><span data-stu-id="edbd3-314">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="edbd3-315">Přítomnost názvu oddílu u klíčů brání hodnotám oddílu v konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="edbd3-315">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="edbd3-316">Tento problém bude vyřešen v příští verzi.</span><span class="sxs-lookup"><span data-stu-id="edbd3-316">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="edbd3-317">Další informace a alternativní řešení naleznete v [části předání konfiguračního oddílu do WebHostBuilder. UseConfiguration používá úplné klíče](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="edbd3-317">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="edbd3-318">`UseConfiguration`pouze kopíruje klíče ze zadaného `IConfiguration` do konfigurace tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="edbd3-318">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="edbd3-319">Nastavení `reloadOnChange: true` souborů JSON, ini a XML nastavení proto nemá žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="edbd3-319">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="edbd3-320">Chcete-li určit spuštění hostitele na konkrétní adrese URL, lze požadovanou hodnotu předat z příkazového řádku při [spuštění příkazu dotnet](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="edbd3-320">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="edbd3-321">Argument příkazového řádku Přepisuje `urls` hodnotu ze souboru *HostSettings. JSON* a server naslouchá na portu 8080:</span><span class="sxs-lookup"><span data-stu-id="edbd3-321">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="edbd3-322">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="edbd3-322">Manage the host</span></span>

<span data-ttu-id="edbd3-323">**Spuštění**</span><span class="sxs-lookup"><span data-stu-id="edbd3-323">**Run**</span></span>

<span data-ttu-id="edbd3-324">`Run` Metoda spustí webovou aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="edbd3-324">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="edbd3-325">**Start**</span><span class="sxs-lookup"><span data-stu-id="edbd3-325">**Start**</span></span>

<span data-ttu-id="edbd3-326">Spusťte hostitele neblokujícím způsobem voláním jeho `Start` metody:</span><span class="sxs-lookup"><span data-stu-id="edbd3-326">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="edbd3-327">Pokud se do `Start` metody předává seznam adres URL, naslouchá na zadaných adresách URL:</span><span class="sxs-lookup"><span data-stu-id="edbd3-327">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="edbd3-328">Aplikace může inicializovat a spustit nového hostitele s použitím předem nakonfigurovaných výchozích hodnot pomocí statické `CreateDefaultBuilder` metody pro usnadnění.</span><span class="sxs-lookup"><span data-stu-id="edbd3-328">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="edbd3-329">Tyto metody spustí server bez výstupu konzoly a s [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) čekají na přerušení (CTRL-C/SIGINT nebo SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="edbd3-329">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="edbd3-330">**Spustit (aplikace RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="edbd3-330">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="edbd3-331">Začněte s `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="edbd3-331">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="edbd3-332">V prohlížeči `http://localhost:5000` vytvořte žádost, aby obdržela odpověď "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="edbd3-332">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="edbd3-333">`WaitForShutdown`blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="edbd3-333">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="edbd3-334">Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-334">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="edbd3-335">**Spustit (adresa URL řetězce, aplikace v RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="edbd3-335">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="edbd3-336">Začněte s adresou URL a `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="edbd3-336">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="edbd3-337">Vytvoří stejný výsledek jako **Start (aplikace RequestDelegate)** , s výjimkou, že aplikace `http://localhost:8080`reaguje.</span><span class="sxs-lookup"><span data-stu-id="edbd3-337">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="edbd3-338">**Spustit (akce&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="edbd3-338">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="edbd3-339">Pro použití middlewaru `IRouteBuilder` směrování použijte instanci ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)):</span><span class="sxs-lookup"><span data-stu-id="edbd3-339">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="edbd3-340">Použijte následující požadavky prohlížeče s příkladem:</span><span class="sxs-lookup"><span data-stu-id="edbd3-340">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="edbd3-341">Request</span><span class="sxs-lookup"><span data-stu-id="edbd3-341">Request</span></span>                                    | <span data-ttu-id="edbd3-342">Odpověď</span><span class="sxs-lookup"><span data-stu-id="edbd3-342">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="edbd3-343">Hello, Novák!</span><span class="sxs-lookup"><span data-stu-id="edbd3-343">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="edbd3-344">Buenos Dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="edbd3-344">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="edbd3-345">Vyvolá výjimku s řetězcem "Ooops!"</span><span class="sxs-lookup"><span data-stu-id="edbd3-345">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="edbd3-346">Vyvolá výjimku s řetězcem "uh Oh!".</span><span class="sxs-lookup"><span data-stu-id="edbd3-346">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="edbd3-347">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="edbd3-347">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="edbd3-348">Ahoj světe!</span><span class="sxs-lookup"><span data-stu-id="edbd3-348">Hello World!</span></span>                             |

<span data-ttu-id="edbd3-349">`WaitForShutdown`blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="edbd3-349">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="edbd3-350">Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-350">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="edbd3-351">**Spustit (adresa URL řetězce,&lt;Action&gt; IRouteBuilder routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="edbd3-351">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="edbd3-352">Použijte adresu URL a instanci `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="edbd3-352">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="edbd3-353">Vytvoří stejný výsledek jako **Start (Action&lt;&gt; IRouteBuilder routeBuilder)** , s výjimkou, že aplikace `http://localhost:8080`reaguje na.</span><span class="sxs-lookup"><span data-stu-id="edbd3-353">Produces the same result as **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="edbd3-354">**StartWith (akce&lt;IApplicationBuilder&gt; aplikace)**</span><span class="sxs-lookup"><span data-stu-id="edbd3-354">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="edbd3-355">Zadejte delegáta pro konfiguraci `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="edbd3-355">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="edbd3-356">V prohlížeči `http://localhost:5000` vytvořte žádost, aby obdržela odpověď "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="edbd3-356">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="edbd3-357">`WaitForShutdown`blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="edbd3-357">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="edbd3-358">Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-358">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="edbd3-359">**StartWith (adresa URL řetězce,&lt;akce&gt; aplikace IApplicationBuilder)**</span><span class="sxs-lookup"><span data-stu-id="edbd3-359">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="edbd3-360">Zadejte adresu URL a delegáta pro konfiguraci `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="edbd3-360">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="edbd3-361">Vytvoří stejný výsledek jako **StartWith (aplikace Action&lt;IApplicationBuilder&gt; )** , s výjimkou, že aplikace `http://localhost:8080`reaguje.</span><span class="sxs-lookup"><span data-stu-id="edbd3-361">Produces the same result as **StartWith(Action&lt;IApplicationBuilder&gt; app)**, except the app responds on `http://localhost:8080`.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="edbd3-362">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="edbd3-362">IHostingEnvironment interface</span></span>

<span data-ttu-id="edbd3-363">[Rozhraní IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o prostředí hostování webu aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-363">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="edbd3-364">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, `IHostingEnvironment` aby bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="edbd3-364">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="edbd3-365">[Přístup založený na konvenci](xref:fundamentals/environments#environment-based-startup-class-and-methods) je možné použít ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-365">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="edbd3-366">Alternativně můžete vložit `IHostingEnvironment` `Startup` do konstruktoru konstruktor pro použití v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="edbd3-366">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="edbd3-367">`IsDevelopment` Kromě metody rozšíření, `IHostingEnvironment` nabídky `IsStaging`, `IsProduction`a metody.`IsEnvironment(string environmentName)`</span><span class="sxs-lookup"><span data-stu-id="edbd3-367">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="edbd3-368">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="edbd3-368">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="edbd3-369">Službu `IHostingEnvironment` je také možné vložit přímo `Configure` do metody nastavení kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="edbd3-369">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="edbd3-370">`IHostingEnvironment`dá se vložit do `Invoke` metody při vytváření vlastního [middlewaru](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="edbd3-370">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="edbd3-371">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="edbd3-371">IApplicationLifetime interface</span></span>

<span data-ttu-id="edbd3-372">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje aktivity po spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-372">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="edbd3-373">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-373">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="edbd3-374">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="edbd3-374">Cancellation Token</span></span>    | <span data-ttu-id="edbd3-375">Aktivováno, když&#8230;</span><span class="sxs-lookup"><span data-stu-id="edbd3-375">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="edbd3-376">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="edbd3-376">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="edbd3-377">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="edbd3-377">The host has fully started.</span></span> |
| [<span data-ttu-id="edbd3-378">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="edbd3-378">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="edbd3-379">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-379">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="edbd3-380">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="edbd3-380">All requests should be processed.</span></span> <span data-ttu-id="edbd3-381">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-381">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="edbd3-382">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="edbd3-382">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="edbd3-383">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-383">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="edbd3-384">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="edbd3-384">Requests may still be processing.</span></span> <span data-ttu-id="edbd3-385">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="edbd3-385">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="edbd3-386">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) žádá o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-386">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="edbd3-387">Následující třída používá `StopApplication` k bezproblémovému vypnutí aplikace při volání `Shutdown` metody třídy:</span><span class="sxs-lookup"><span data-stu-id="edbd3-387">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="edbd3-388">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="edbd3-388">Scope validation</span></span>

<span data-ttu-id="edbd3-389">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` , pokud je prostředí aplikace vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="edbd3-389">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="edbd3-390">Když `ValidateScopes` je nastavená `true`na, výchozí poskytovatel služby provede kontrolu, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="edbd3-390">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="edbd3-391">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="edbd3-391">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="edbd3-392">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="edbd3-392">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="edbd3-393">Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="edbd3-393">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="edbd3-394">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-394">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="edbd3-395">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="edbd3-395">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="edbd3-396">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbd3-396">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="edbd3-397">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="edbd3-397">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="edbd3-398">Pokud chcete vždy ověřit obory, včetně v produkčním prostředí, nakonfigurujte [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) pomocí [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrci hostitele:</span><span class="sxs-lookup"><span data-stu-id="edbd3-398">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="edbd3-399">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="edbd3-399">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
