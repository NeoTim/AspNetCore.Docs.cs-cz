---
title: ASP.NET Core webového hostitele
author: rick-anderson
description: Přečtěte si o webovém hostiteli v ASP.NET Core, který zodpovídá za správu spouštění a životního cyklu aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 67831237ab1f95c6535cf586681150a230b491d0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635265"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="6b405-103">ASP.NET Core webového hostitele</span><span class="sxs-lookup"><span data-stu-id="6b405-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="6b405-104">ASP.NET Core aplikace nakonfigurují a spouštějí *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="6b405-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="6b405-105">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="6b405-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="6b405-106">Minimálně hostitel nakonfiguruje server a kanál zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="6b405-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="6b405-107">Hostitel může také nastavit protokolování, vkládání závislostí a konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="6b405-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6b405-108">Tento článek se zabývá webovým hostitelem, který zůstává dostupný jenom pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="6b405-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="6b405-109">[Obecný hostitel](xref:fundamentals/host/generic-host) je doporučený pro všechny typy aplikací.</span><span class="sxs-lookup"><span data-stu-id="6b405-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b405-110">Tento článek se zabývá webovým hostitelem, který je určený pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="6b405-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="6b405-111">Pro jiné druhy aplikací použijte [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="6b405-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="6b405-112">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="6b405-112">Set up a host</span></span>

<span data-ttu-id="6b405-113">Vytvořte hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="6b405-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="6b405-114">To se obvykle provádí v vstupním bodě aplikace, `Main` metodě.</span><span class="sxs-lookup"><span data-stu-id="6b405-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="6b405-115">V šablonách projektů `Main` se nachází v *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="6b405-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="6b405-116">Typická aplikace volá [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) , aby zahájila nastavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="6b405-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="6b405-117">Kód, který volá, `CreateDefaultBuilder` je v metodě s názvem `CreateWebHostBuilder` , která ho odděluje od kódu v `Main` volání `Run` objektu Builder.</span><span class="sxs-lookup"><span data-stu-id="6b405-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="6b405-118">Toto oddělení se vyžaduje při použití [Entity Framework Corech nástrojů](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="6b405-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="6b405-119">Nástroje očekávají `CreateWebHostBuilder` , že vyvolají metodu, kterou můžou zavolat v době návrhu, aby se nakonfiguroval hostitel bez spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="6b405-120">Alternativou je implementace `IDesignTimeDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="6b405-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="6b405-121">Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="6b405-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="6b405-122">`CreateDefaultBuilder` provede následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="6b405-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="6b405-123">Nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server pomocí poskytovatelů konfigurace hostování aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="6b405-124">Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="6b405-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="6b405-125">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou [adresářem. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="6b405-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="6b405-126">Načte [konfiguraci hostitele](#host-configuration-values) z:</span><span class="sxs-lookup"><span data-stu-id="6b405-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="6b405-127">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT` ).</span><span class="sxs-lookup"><span data-stu-id="6b405-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="6b405-128">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6b405-128">Command-line arguments.</span></span>
* <span data-ttu-id="6b405-129">Načte konfiguraci aplikace v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="6b405-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="6b405-130">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="6b405-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="6b405-131">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="6b405-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="6b405-132">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v `Development` prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="6b405-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="6b405-133">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6b405-133">Environment variables.</span></span>
  * <span data-ttu-id="6b405-134">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6b405-134">Command-line arguments.</span></span>
* <span data-ttu-id="6b405-135">Konfiguruje [protokolování](xref:fundamentals/logging/index) pro konzolu a výstup ladění.</span><span class="sxs-lookup"><span data-stu-id="6b405-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="6b405-136">Protokolování zahrnuje pravidla [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) zadaná v části konfigurace protokolování *appsettings.jsv* nebo *appSettings. { Environment}. JSON* soubor.</span><span class="sxs-lookup"><span data-stu-id="6b405-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="6b405-137">Při spuštění za službou IIS s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) `CreateDefaultBuilder` umožňuje [integraci služby IIS](xref:host-and-deploy/iis/index), která konfiguruje základní adresu a port aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="6b405-138">Integrace služby IIS také nakonfiguruje aplikaci pro [zachycení chyb při spuštění](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="6b405-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="6b405-139">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="6b405-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="6b405-140">Nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na, `true` Pokud je prostředí aplikace vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="6b405-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="6b405-141">Další informace najdete v tématu [ověřování oboru](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="6b405-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="6b405-142">Konfiguraci definovanou pomocí `CreateDefaultBuilder` lze přepsat a rozšířit [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a dalšími metodami a metodami rozšíření [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="6b405-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="6b405-143">Následuje několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="6b405-143">A few examples follow:</span></span>

* <span data-ttu-id="6b405-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se používá k určení dalších `IConfiguration` aplikací.</span><span class="sxs-lookup"><span data-stu-id="6b405-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="6b405-145">Následující `ConfigureAppConfiguration` volání přidá delegáta pro zahrnutí konfigurace aplikace do souboru *appsettings.xml* .</span><span class="sxs-lookup"><span data-stu-id="6b405-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="6b405-146">`ConfigureAppConfiguration` může být volána několikrát.</span><span class="sxs-lookup"><span data-stu-id="6b405-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="6b405-147">Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí).</span><span class="sxs-lookup"><span data-stu-id="6b405-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="6b405-148">Viz část [hodnoty konfigurace hostitele](#host-configuration-values) .</span><span class="sxs-lookup"><span data-stu-id="6b405-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="6b405-149">Následující `ConfigureLogging` volání přidá delegáta pro konfiguraci minimální úrovně protokolování ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="6b405-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="6b405-150">Toto nastavení přepíše nastavení v *appsettings.Development.jszapnuto* ( `LogLevel.Debug` ) a *appsettings.Production.jsna* ( `LogLevel.Error` ) nakonfigurované pomocí `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6b405-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6b405-151">`ConfigureLogging` může být volána několikrát.</span><span class="sxs-lookup"><span data-stu-id="6b405-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="6b405-152">Následující volání `ConfigureKestrel` přepíše výchozí [omezení. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtů zavedených při konfiguraci Kestrel `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="6b405-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="6b405-153">Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepisuje výchozí [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) z 30 000 000 bajtů zavedených při konfiguraci Kestrel `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="6b405-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="6b405-154">[Kořen obsahu](xref:fundamentals/index#content-root) určuje, kde hostitel vyhledává soubory obsahu, například soubory zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="6b405-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="6b405-155">Při spuštění aplikace z kořenové složky projektu se jako kořen obsahu použije kořenová složka projektu.</span><span class="sxs-lookup"><span data-stu-id="6b405-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="6b405-156">Toto je výchozí použití v [aplikaci Visual Studio](https://visualstudio.microsoft.com) a [dotnet New Templates](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="6b405-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="6b405-157">Další informace o konfiguraci aplikace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="6b405-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="6b405-158">Jako alternativu k použití statické `CreateDefaultBuilder` metody je vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) podporovaným přístupem s ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="6b405-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="6b405-159">Při nastavování hostitele je možné zadat metody [Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) .</span><span class="sxs-lookup"><span data-stu-id="6b405-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="6b405-160">Pokud `Startup` je třída zadána, musí definovat `Configure` metodu.</span><span class="sxs-lookup"><span data-stu-id="6b405-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="6b405-161">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="6b405-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="6b405-162">Několik volání, která se mají `ConfigureServices` navzájem připojit.</span><span class="sxs-lookup"><span data-stu-id="6b405-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="6b405-163">Vícenásobné volání `Configure` nebo `UseStartup` v `WebHostBuilder` nastavení nahradit předchozí.</span><span class="sxs-lookup"><span data-stu-id="6b405-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="6b405-164">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="6b405-164">Host configuration values</span></span>

<span data-ttu-id="6b405-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) spoléhá na následující přístupy k nastavení hodnot konfigurace hostitele:</span><span class="sxs-lookup"><span data-stu-id="6b405-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="6b405-166">Konfigurace tvůrce hostitele, což zahrnuje proměnné prostředí ve formátu `ASPNETCORE_{configurationKey}` .</span><span class="sxs-lookup"><span data-stu-id="6b405-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="6b405-167">Například, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="6b405-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="6b405-168">Rozšíření jako [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz oddíl pro [přepsání konfigurace](#override-configuration) ).</span><span class="sxs-lookup"><span data-stu-id="6b405-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="6b405-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč.</span><span class="sxs-lookup"><span data-stu-id="6b405-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="6b405-170">Při nastavení hodnoty s hodnotou `UseSetting` je hodnota nastavena jako řetězec bez ohledu na typ.</span><span class="sxs-lookup"><span data-stu-id="6b405-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="6b405-171">Hostitel používá jakoukoli možnost, která hodnotu nastaví jako poslední.</span><span class="sxs-lookup"><span data-stu-id="6b405-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="6b405-172">Další informace najdete v tématu [přepsání konfigurace](#override-configuration) v následující části.</span><span class="sxs-lookup"><span data-stu-id="6b405-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="6b405-173">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="6b405-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6b405-174">`IWebHostEnvironment.ApplicationName`Vlastnost je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="6b405-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="6b405-175">Hodnota je nastavená na název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="6b405-176">Chcete-li nastavit hodnotu explicitně, použijte [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="6b405-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b405-177">Vlastnost [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="6b405-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="6b405-178">Hodnota je nastavená na název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="6b405-179">Chcete-li nastavit hodnotu explicitně, použijte [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="6b405-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="6b405-180">**Klíč**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="6b405-180">**Key**: applicationName</span></span>  
<span data-ttu-id="6b405-181">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="6b405-181">**Type**: *string*</span></span>  
<span data-ttu-id="6b405-182">**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="6b405-183">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6b405-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6b405-184">**Proměnná prostředí**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="6b405-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="6b405-185">Zachytit chyby při spuštění</span><span class="sxs-lookup"><span data-stu-id="6b405-185">Capture Startup Errors</span></span>

<span data-ttu-id="6b405-186">Toto nastavení řídí zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6b405-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="6b405-187">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="6b405-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="6b405-188">**Typ**: *bool* ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="6b405-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6b405-189">**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .</span><span class="sxs-lookup"><span data-stu-id="6b405-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="6b405-190">**Nastavit pomocí**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="6b405-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="6b405-191">**Proměnná prostředí**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="6b405-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="6b405-192">`false`V důsledku dojde k chybám při spuštění hostitele.</span><span class="sxs-lookup"><span data-stu-id="6b405-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="6b405-193">Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.</span><span class="sxs-lookup"><span data-stu-id="6b405-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="6b405-194">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="6b405-194">Content root</span></span>

<span data-ttu-id="6b405-195">Toto nastavení určuje, kde ASP.NET Core začne vyhledávat soubory obsahu.</span><span class="sxs-lookup"><span data-stu-id="6b405-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="6b405-196">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="6b405-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="6b405-197">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="6b405-197">**Type**: *string*</span></span>  
<span data-ttu-id="6b405-198">**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="6b405-199">**Nastavit pomocí**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="6b405-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="6b405-200">**Proměnná prostředí**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="6b405-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="6b405-201">Kořen obsahu se používá také jako základní cesta pro [kořenový adresář webu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="6b405-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="6b405-202">Pokud kořenová cesta obsahu neexistuje, hostitele se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="6b405-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="6b405-203">Další informace:</span><span class="sxs-lookup"><span data-stu-id="6b405-203">For more information, see:</span></span>

* [<span data-ttu-id="6b405-204">Základy: kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="6b405-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="6b405-205">Webový kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="6b405-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="6b405-206">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="6b405-206">Detailed Errors</span></span>

<span data-ttu-id="6b405-207">Určuje, zda mají být zachyceny podrobné chyby.</span><span class="sxs-lookup"><span data-stu-id="6b405-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="6b405-208">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="6b405-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="6b405-209">**Typ**: *bool* ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="6b405-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6b405-210">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="6b405-210">**Default**: false</span></span>  
<span data-ttu-id="6b405-211">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6b405-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6b405-212">**Proměnná prostředí**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="6b405-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="6b405-213">Když je tato možnost povolená (nebo když je <a href="#environment">prostředí</a> nastavené na `Development` ), aplikace zachycuje podrobné výjimky.</span><span class="sxs-lookup"><span data-stu-id="6b405-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="6b405-214">Prostředí</span><span class="sxs-lookup"><span data-stu-id="6b405-214">Environment</span></span>

<span data-ttu-id="6b405-215">Nastaví prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-215">Sets the app's environment.</span></span>

<span data-ttu-id="6b405-216">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="6b405-216">**Key**: environment</span></span>  
<span data-ttu-id="6b405-217">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="6b405-217">**Type**: *string*</span></span>  
<span data-ttu-id="6b405-218">**Výchozí**: výroba</span><span class="sxs-lookup"><span data-stu-id="6b405-218">**Default**: Production</span></span>  
<span data-ttu-id="6b405-219">**Nastavit pomocí**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="6b405-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="6b405-220">**Proměnná prostředí**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="6b405-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="6b405-221">Prostředí lze nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6b405-221">The environment can be set to any value.</span></span> <span data-ttu-id="6b405-222">Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` .</span><span class="sxs-lookup"><span data-stu-id="6b405-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="6b405-223">U hodnot se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="6b405-223">Values aren't case sensitive.</span></span> <span data-ttu-id="6b405-224">Ve výchozím nastavení je *prostředí* načteno z `ASPNETCORE_ENVIRONMENT` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6b405-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="6b405-225">Při použití sady [Visual Studio](https://visualstudio.microsoft.com)mohou být proměnné prostředí nastaveny v *launchSettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="6b405-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="6b405-226">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6b405-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="6b405-227">Hostování spouštěcích sestavení</span><span class="sxs-lookup"><span data-stu-id="6b405-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="6b405-228">Nastaví hostování spouštěcích sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="6b405-229">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="6b405-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="6b405-230">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="6b405-230">**Type**: *string*</span></span>  
<span data-ttu-id="6b405-231">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="6b405-231">**Default**: Empty string</span></span>  
<span data-ttu-id="6b405-232">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6b405-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6b405-233">**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="6b405-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="6b405-234">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6b405-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="6b405-235">I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="6b405-236">Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.</span><span class="sxs-lookup"><span data-stu-id="6b405-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="6b405-237">Port HTTPS</span><span class="sxs-lookup"><span data-stu-id="6b405-237">HTTPS Port</span></span>

<span data-ttu-id="6b405-238">Nastavte port přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6b405-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="6b405-239">Používá se při [vynucování https](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="6b405-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6b405-240">**Klíč**: https_port **typ**: *string* 
 **výchozí**hodnota řetězce: není nastavena výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="6b405-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="6b405-241">**Nastavit pomocí**: `UseSetting` 
 **Proměnná prostředí**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="6b405-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="6b405-242">Hostování nespouštěcích sestavení pro vyloučení</span><span class="sxs-lookup"><span data-stu-id="6b405-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="6b405-243">Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.</span><span class="sxs-lookup"><span data-stu-id="6b405-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="6b405-244">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="6b405-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="6b405-245">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="6b405-245">**Type**: *string*</span></span>  
<span data-ttu-id="6b405-246">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="6b405-246">**Default**: Empty string</span></span>  
<span data-ttu-id="6b405-247">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6b405-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6b405-248">**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="6b405-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="6b405-249">Preferovat hostování adres URL</span><span class="sxs-lookup"><span data-stu-id="6b405-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="6b405-250">Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `WebHostBuilder` místo nakonfigurovaných s `IServer` implementací.</span><span class="sxs-lookup"><span data-stu-id="6b405-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="6b405-251">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="6b405-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="6b405-252">**Typ**: *bool* ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="6b405-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6b405-253">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="6b405-253">**Default**: true</span></span>  
<span data-ttu-id="6b405-254">**Nastavit pomocí**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="6b405-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="6b405-255">**Proměnná prostředí**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="6b405-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="6b405-256">Zabránit spuštění hostování</span><span class="sxs-lookup"><span data-stu-id="6b405-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="6b405-257">Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="6b405-258">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6b405-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="6b405-259">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="6b405-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="6b405-260">**Typ**: *bool* ( `true` nebo `1` )</span><span class="sxs-lookup"><span data-stu-id="6b405-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="6b405-261">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="6b405-261">**Default**: false</span></span>  
<span data-ttu-id="6b405-262">**Nastavit pomocí**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="6b405-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="6b405-263">**Proměnná prostředí**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="6b405-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="6b405-264">Adresy URL serveru</span><span class="sxs-lookup"><span data-stu-id="6b405-264">Server URLs</span></span>

<span data-ttu-id="6b405-265">Označuje IP adresy nebo adresy hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.</span><span class="sxs-lookup"><span data-stu-id="6b405-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="6b405-266">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="6b405-266">**Key**: urls</span></span>  
<span data-ttu-id="6b405-267">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="6b405-267">**Type**: *string*</span></span>  
<span data-ttu-id="6b405-268">**Výchozí**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="6b405-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="6b405-269">**Nastavit pomocí**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="6b405-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="6b405-270">**Proměnná prostředí**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="6b405-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="6b405-271">Nastavte na středníkem oddělený (;) Seznam předpon adres URL, na které má server odpovědět</span><span class="sxs-lookup"><span data-stu-id="6b405-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="6b405-272">Například, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="6b405-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="6b405-273">Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="6b405-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="6b405-274">Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6b405-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="6b405-275">Podporované formáty se mezi servery liší.</span><span class="sxs-lookup"><span data-stu-id="6b405-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="6b405-276">Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="6b405-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="6b405-277">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6b405-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="6b405-278">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="6b405-278">Shutdown Timeout</span></span>

<span data-ttu-id="6b405-279">Určuje dobu, po kterou se má čekat na vypnutí webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="6b405-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="6b405-280">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="6b405-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="6b405-281">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="6b405-281">**Type**: *int*</span></span>  
<span data-ttu-id="6b405-282">**Výchozí**: 5</span><span class="sxs-lookup"><span data-stu-id="6b405-282">**Default**: 5</span></span>  
<span data-ttu-id="6b405-283">**Nastavit pomocí**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="6b405-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="6b405-284">**Proměnná prostředí**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="6b405-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="6b405-285">I když klíč přijímá *int* s `UseSetting` (například `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")` ), metoda rozšíření [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) přebírá [časový interval](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="6b405-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="6b405-286">Po uplynutí časového limitu hostování:</span><span class="sxs-lookup"><span data-stu-id="6b405-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="6b405-287">Spustí [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="6b405-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="6b405-288">Pokusy o zastavení hostovaných služeb a protokolování všech chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="6b405-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="6b405-289">Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná.</span><span class="sxs-lookup"><span data-stu-id="6b405-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="6b405-290">Služby se zastavují i v případě, že se nedokončily zpracování.</span><span class="sxs-lookup"><span data-stu-id="6b405-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="6b405-291">Pokud služby vyžadují ukončení déle, zvyšte časový limit.</span><span class="sxs-lookup"><span data-stu-id="6b405-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="6b405-292">Spouštěcí sestavení</span><span class="sxs-lookup"><span data-stu-id="6b405-292">Startup Assembly</span></span>

<span data-ttu-id="6b405-293">Určuje sestavení, ve kterém má být vyhledána `Startup` Třída.</span><span class="sxs-lookup"><span data-stu-id="6b405-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="6b405-294">**Klíč**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="6b405-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="6b405-295">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="6b405-295">**Type**: *string*</span></span>  
<span data-ttu-id="6b405-296">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="6b405-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="6b405-297">**Nastavit pomocí**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="6b405-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="6b405-298">**Proměnná prostředí**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="6b405-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="6b405-299">Lze odkazovat na sestavení podle názvu ( `string` ) nebo typu ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="6b405-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="6b405-300">Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.</span><span class="sxs-lookup"><span data-stu-id="6b405-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="6b405-301">Webový kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="6b405-301">Web root</span></span>

<span data-ttu-id="6b405-302">Nastaví relativní cestu k statickým assetům aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="6b405-303">**Klíč**: Webroot</span><span class="sxs-lookup"><span data-stu-id="6b405-303">**Key**: webroot</span></span>  
<span data-ttu-id="6b405-304">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="6b405-304">**Type**: *string*</span></span>  
<span data-ttu-id="6b405-305">**Výchozí**: výchozí hodnota je `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="6b405-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="6b405-306">Cesta k *obsahu {root}/wwwroot* musí existovat.</span><span class="sxs-lookup"><span data-stu-id="6b405-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="6b405-307">Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.</span><span class="sxs-lookup"><span data-stu-id="6b405-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="6b405-308">**Nastavit pomocí**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="6b405-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="6b405-309">**Proměnná prostředí**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="6b405-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="6b405-310">Další informace:</span><span class="sxs-lookup"><span data-stu-id="6b405-310">For more information, see:</span></span>

* [<span data-ttu-id="6b405-311">Základy: web root</span><span class="sxs-lookup"><span data-stu-id="6b405-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="6b405-312">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="6b405-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="6b405-313">Přepsat konfiguraci</span><span class="sxs-lookup"><span data-stu-id="6b405-313">Override configuration</span></span>

<span data-ttu-id="6b405-314">Pro konfiguraci webového hostitele použijte [konfiguraci](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="6b405-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="6b405-315">V následujícím příkladu je konfigurace hostitele volitelně určena v *hostsettings.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="6b405-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="6b405-316">Jakákoli konfigurace načtená z *hostsettings.jsv* souboru může být přepsána argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6b405-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="6b405-317">Vytvořená konfigurace (v `config` ) se používá ke konfiguraci hostitele pomocí [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="6b405-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="6b405-318">`IWebHostBuilder`konfigurace je přidána do konfigurace aplikace, ale tato konverzace není pravdivá, &mdash; `ConfigureAppConfiguration` nemá vliv na `IWebHostBuilder` konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="6b405-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="6b405-319">Přepsání konfigurace, kterou poskytuje `UseUrls` , pomocí *hostsettings.jspři* prvním nastavení konfigurace argumentu příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="6b405-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="6b405-320">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="6b405-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="6b405-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) zkopíruje pouze klíče ze zadaného `IConfiguration` do konfigurace tvůrce hostitele.</span><span class="sxs-lookup"><span data-stu-id="6b405-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="6b405-322">Nastavení `reloadOnChange: true` souborů JSON, ini a XML nastavení proto nemá žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="6b405-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="6b405-323">Chcete-li určit spuštění hostitele na konkrétní adrese URL, lze požadovanou hodnotu předat z příkazového řádku při [spuštění příkazu dotnet](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="6b405-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="6b405-324">Argument příkazového řádku Přepisuje `urls` hodnotu z *hostsettings.jsv* souboru a server naslouchá na portu 8080:</span><span class="sxs-lookup"><span data-stu-id="6b405-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="6b405-325">Správa hostitele</span><span class="sxs-lookup"><span data-stu-id="6b405-325">Manage the host</span></span>

<span data-ttu-id="6b405-326">**Spustit**</span><span class="sxs-lookup"><span data-stu-id="6b405-326">**Run**</span></span>

<span data-ttu-id="6b405-327">`Run`Metoda spustí webovou aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý:</span><span class="sxs-lookup"><span data-stu-id="6b405-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="6b405-328">**Zahájení**</span><span class="sxs-lookup"><span data-stu-id="6b405-328">**Start**</span></span>

<span data-ttu-id="6b405-329">Spusťte hostitele neblokujícím způsobem voláním jeho `Start` metody:</span><span class="sxs-lookup"><span data-stu-id="6b405-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="6b405-330">Pokud se do metody předává seznam adres URL `Start` , naslouchá na zadaných adresách URL:</span><span class="sxs-lookup"><span data-stu-id="6b405-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="6b405-331">Aplikace může inicializovat a spustit nového hostitele s použitím předem nakonfigurovaných výchozích hodnot `CreateDefaultBuilder` pomocí statické metody pro usnadnění.</span><span class="sxs-lookup"><span data-stu-id="6b405-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="6b405-332">Tyto metody spustí server bez výstupu konzoly a s [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) čekají na přerušení (CTRL-C/SIGINT nebo SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="6b405-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="6b405-333">**Spustit (aplikace RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="6b405-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="6b405-334">Začněte s `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="6b405-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="6b405-335">V prohlížeči vytvořte žádost, aby `http://localhost:5000` obdržela odpověď "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="6b405-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="6b405-336">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="6b405-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="6b405-337">Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.</span><span class="sxs-lookup"><span data-stu-id="6b405-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="6b405-338">**Spustit (adresa URL řetězce, aplikace v RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="6b405-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="6b405-339">Začněte s adresou URL a `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="6b405-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="6b405-340">Vytvoří stejný výsledek jako **Start (aplikace RequestDelegate)**, s výjimkou, že aplikace reaguje `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="6b405-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="6b405-341">**Spustit (akce \<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="6b405-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="6b405-342">`IRouteBuilder`Pro použití middlewaru směrování použijte instanci ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)):</span><span class="sxs-lookup"><span data-stu-id="6b405-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="6b405-343">Použijte následující požadavky prohlížeče s příkladem:</span><span class="sxs-lookup"><span data-stu-id="6b405-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="6b405-344">Žádost</span><span class="sxs-lookup"><span data-stu-id="6b405-344">Request</span></span>                                    | <span data-ttu-id="6b405-345">Odpověď</span><span class="sxs-lookup"><span data-stu-id="6b405-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="6b405-346">Hello, Novák!</span><span class="sxs-lookup"><span data-stu-id="6b405-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="6b405-347">Buenos Dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="6b405-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="6b405-348">Vyvolá výjimku s řetězcem "Ooops!"</span><span class="sxs-lookup"><span data-stu-id="6b405-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="6b405-349">Vyvolá výjimku s řetězcem "uh Oh!".</span><span class="sxs-lookup"><span data-stu-id="6b405-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="6b405-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="6b405-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="6b405-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="6b405-351">Hello World!</span></span>                             |

<span data-ttu-id="6b405-352">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="6b405-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="6b405-353">Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.</span><span class="sxs-lookup"><span data-stu-id="6b405-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="6b405-354">**Start (adresa URL řetězce, akce \<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="6b405-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="6b405-355">Použijte adresu URL a instanci `IRouteBuilder` :</span><span class="sxs-lookup"><span data-stu-id="6b405-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="6b405-356">Vytvoří stejný výsledek jako **Start (Action \<IRouteBuilder> routeBuilder)**, s výjimkou, že aplikace reaguje na `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="6b405-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="6b405-357">**StartWith (akce \<IApplicationBuilder> aplikace)**</span><span class="sxs-lookup"><span data-stu-id="6b405-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="6b405-358">Zadejte delegáta pro konfiguraci `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="6b405-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="6b405-359">V prohlížeči vytvořte žádost, aby `http://localhost:5000` obdržela odpověď "Hello World!".</span><span class="sxs-lookup"><span data-stu-id="6b405-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="6b405-360">`WaitForShutdown` blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="6b405-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="6b405-361">Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.</span><span class="sxs-lookup"><span data-stu-id="6b405-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="6b405-362">**StartWith (adresa URL řetězce, \<IApplicationBuilder> aplikace akce)**</span><span class="sxs-lookup"><span data-stu-id="6b405-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="6b405-363">Zadejte adresu URL a delegáta pro konfiguraci `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="6b405-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="6b405-364">Vytvoří stejný výsledek jako **StartWith (aplikace akce \<IApplicationBuilder> )**, kromě toho, že aplikace reaguje `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="6b405-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="6b405-365">Rozhraní IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="6b405-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="6b405-366">`IWebHostEnvironment`Rozhraní poskytuje informace o prostředí hostování webu aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="6b405-367">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, aby `IWebHostEnvironment` bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6b405-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="6b405-368">[Přístup založený na konvenci](xref:fundamentals/environments#environment-based-startup-class-and-methods) je možné použít ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="6b405-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="6b405-369">Alternativně můžete vložit `IWebHostEnvironment` do `Startup` konstruktoru konstruktor pro použití v `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6b405-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="6b405-370">Kromě `IsDevelopment` metody rozšíření, `IWebHostEnvironment` nabídky `IsStaging` , `IsProduction` a `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="6b405-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="6b405-371">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6b405-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6b405-372">`IWebHostEnvironment`Službu je také možné vložit přímo do `Configure` metody nastavení kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="6b405-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="6b405-373">`IWebHostEnvironment` dá se vložit do `Invoke` metody při vytváření vlastního [middlewaru](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="6b405-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="6b405-374">Rozhraní IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="6b405-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="6b405-375">[Rozhraní IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o prostředí hostování webu aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="6b405-376">Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, aby `IHostingEnvironment` bylo možné použít jeho vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6b405-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="6b405-377">[Přístup založený na konvenci](xref:fundamentals/environments#environment-based-startup-class-and-methods) je možné použít ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="6b405-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="6b405-378">Alternativně můžete vložit `IHostingEnvironment` do `Startup` konstruktoru konstruktor pro použití v `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6b405-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="6b405-379">Kromě `IsDevelopment` metody rozšíření, `IHostingEnvironment` nabídky `IsStaging` , `IsProduction` a `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="6b405-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="6b405-380">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6b405-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6b405-381">`IHostingEnvironment`Službu je také možné vložit přímo do `Configure` metody nastavení kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="6b405-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="6b405-382">`IHostingEnvironment` dá se vložit do `Invoke` metody při vytváření vlastního [middlewaru](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="6b405-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="6b405-383">Rozhraní IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="6b405-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="6b405-384">`IHostApplicationLifetime` umožňuje aktivity po spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6b405-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="6b405-385">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6b405-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="6b405-386">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="6b405-386">Cancellation Token</span></span>    | <span data-ttu-id="6b405-387">Aktivované při&#8230;</span><span class="sxs-lookup"><span data-stu-id="6b405-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="6b405-388">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="6b405-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="6b405-389">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6b405-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="6b405-390">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="6b405-390">All requests should be processed.</span></span> <span data-ttu-id="6b405-391">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="6b405-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="6b405-392">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6b405-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="6b405-393">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="6b405-393">Requests may still be processing.</span></span> <span data-ttu-id="6b405-394">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="6b405-394">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="6b405-395">`StopApplication` požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="6b405-396">Následující třída používá `StopApplication` k bezproblémovému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="6b405-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="6b405-397">Rozhraní IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="6b405-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="6b405-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje aktivity po spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6b405-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="6b405-399">Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6b405-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="6b405-400">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="6b405-400">Cancellation Token</span></span>    | <span data-ttu-id="6b405-401">Aktivované při&#8230;</span><span class="sxs-lookup"><span data-stu-id="6b405-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="6b405-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="6b405-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="6b405-403">Hostitel byl plně spuštěn.</span><span class="sxs-lookup"><span data-stu-id="6b405-403">The host has fully started.</span></span> |
| [<span data-ttu-id="6b405-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="6b405-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="6b405-405">Hostitel dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6b405-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="6b405-406">Všechny požadavky by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="6b405-406">All requests should be processed.</span></span> <span data-ttu-id="6b405-407">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="6b405-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="6b405-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="6b405-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="6b405-409">Hostitel provádí bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="6b405-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="6b405-410">Žádosti se pořád dají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="6b405-410">Requests may still be processing.</span></span> <span data-ttu-id="6b405-411">Bloky vypnutí, dokud se tato událost nedokončí.</span><span class="sxs-lookup"><span data-stu-id="6b405-411">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="6b405-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) žádá o ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="6b405-413">Následující třída používá `StopApplication` k bezproblémovému vypnutí aplikace při `Shutdown` volání metody třídy:</span><span class="sxs-lookup"><span data-stu-id="6b405-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="6b405-414">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="6b405-414">Scope validation</span></span>

<span data-ttu-id="6b405-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na, `true` Pokud je prostředí aplikace vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="6b405-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="6b405-416">Když `ValidateScopes` je nastavená na `true` , výchozí poskytovatel služby provede kontrolu, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="6b405-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="6b405-417">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="6b405-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="6b405-418">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="6b405-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="6b405-419">Poskytovatel kořenové služby se vytvoří, když se zavolá [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) .</span><span class="sxs-lookup"><span data-stu-id="6b405-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="6b405-420">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b405-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="6b405-421">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="6b405-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="6b405-422">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="6b405-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="6b405-423">Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .</span><span class="sxs-lookup"><span data-stu-id="6b405-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="6b405-424">Pokud chcete vždy ověřit obory, včetně v produkčním prostředí, nakonfigurujte [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) pomocí [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrci hostitele:</span><span class="sxs-lookup"><span data-stu-id="6b405-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="6b405-425">Další materiály</span><span class="sxs-lookup"><span data-stu-id="6b405-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
