---
title: Webového hostitele ASP.NET Core
author: guardrex
description: Další informace o webového hostitele v ASP.NET Core, který je zodpovědný za spouštění a životního cyklu správy aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2018
uid: fundamentals/host/web-host
ms.openlocfilehash: 5af09ad715768d51ce8ef2c8425cc51ebada6859
ms.sourcegitcommit: 1d6ab43eed9cb3df6211c22b97bb3a9351ec4419
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51597820"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="51dc5-103">Webového hostitele ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51dc5-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="51dc5-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="51dc5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="51dc5-105">1.1 verzi tohoto tématu, stáhněte si [ASP.NET Core webového hostitele (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="51dc5-105">For the 1.1 version of this topic, download [ASP.NET Core Web Host (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="51dc5-106">Konfigurace aplikace ASP.NET Core a spouštění *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="51dc5-106">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="51dc5-107">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="51dc5-107">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="51dc5-108">Minimálně hostitele nakonfiguruje server a kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="51dc5-108">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="51dc5-109">Toto téma popisuje webového hostitele ASP.NET Core ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)), což je užitečné pro hostování webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="51dc5-109">This topic covers the ASP.NET Core Web Host ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)), which is useful for hosting web apps.</span></span> <span data-ttu-id="51dc5-110">Pro pokrytí obecný hostitele .NET ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)), najdete v článku <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-110">For coverage of the .NET Generic Host ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)), see <xref:fundamentals/host/generic-host>.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="51dc5-111">Nastavení hostitele</span><span class="sxs-lookup"><span data-stu-id="51dc5-111">Set up a host</span></span>

<span data-ttu-id="51dc5-112">Vytvoření hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="51dc5-112">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="51dc5-113">To se obvykle provádí v vstupní bod aplikace, `Main` metody.</span><span class="sxs-lookup"><span data-stu-id="51dc5-113">This is typically performed in the app's entry point, the `Main` method.</span></span> <span data-ttu-id="51dc5-114">V šablonách projektů `Main` se nachází v *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="51dc5-114">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="51dc5-115">Typické *Program.cs* volání [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) spustit nastavení hostitele:</span><span class="sxs-lookup"><span data-stu-id="51dc5-115">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="51dc5-116">`CreateDefaultBuilder` provádí následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="51dc5-116">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="51dc5-117">Nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) webového serveru a nakonfiguruje server pomocí aplikace poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-117">Configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server and configures the server using the app's hosting configuration providers.</span></span> <span data-ttu-id="51dc5-118">Výchozí možnosti Kestrel najdete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-118">For the Kestrel default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="51dc5-119">Nastaví obsahu kořenovou cesta vrácená procedurou [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="51dc5-119">Sets the content root to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="51dc5-120">Načtení [konfigurace hostitele](#host-configuration-values) od:</span><span class="sxs-lookup"><span data-stu-id="51dc5-120">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="51dc5-121">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="51dc5-121">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="51dc5-122">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="51dc5-122">Command-line arguments.</span></span>
* <span data-ttu-id="51dc5-123">Načte konfiguraci aplikací v uvedeném pořadí od:</span><span class="sxs-lookup"><span data-stu-id="51dc5-123">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="51dc5-124">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="51dc5-124">*appsettings.json*.</span></span>
  * <span data-ttu-id="51dc5-125">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="51dc5-125">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="51dc5-126">[Tajný klíč správce](xref:security/app-secrets) při spuštění aplikace `Development` prostředí s využitím vstupní sestavení.</span><span class="sxs-lookup"><span data-stu-id="51dc5-126">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="51dc5-127">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-127">Environment variables.</span></span>
  * <span data-ttu-id="51dc5-128">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="51dc5-128">Command-line arguments.</span></span>
* <span data-ttu-id="51dc5-129">Nakonfiguruje [protokolování](xref:fundamentals/logging/index) pro výstup konzoly a ladění.</span><span class="sxs-lookup"><span data-stu-id="51dc5-129">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="51dc5-130">Protokolování zahrnuje [filtrování protokolu](xref:fundamentals/logging/index#log-filtering) pravidel specifikovaných v části Konfigurace protokolování *appsettings.json* nebo *appsettings. { Prostředí} .json* souboru.</span><span class="sxs-lookup"><span data-stu-id="51dc5-130">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="51dc5-131">Při spuštění za služby IIS umožňuje [integrace služby IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="51dc5-131">When running behind IIS, enables [IIS integration](xref:host-and-deploy/iis/index).</span></span> <span data-ttu-id="51dc5-132">Nastaví základní cesta a portu server naslouchá na při použití [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="51dc5-132">Configures the base path and port the server listens on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="51dc5-133">Modul vytváří reverzní proxy server mezi službou IIS a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="51dc5-133">The module creates a reverse proxy between IIS and Kestrel.</span></span> <span data-ttu-id="51dc5-134">Také nakonfiguruje aplikaci [zachycení chyb při spuštění](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="51dc5-134">Also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="51dc5-135">Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-135">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="51dc5-136">Nastaví [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) k `true` jestli vývojové prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-136">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="51dc5-137">Další informace najdete v tématu [oboru ověření](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="51dc5-137">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="51dc5-138">Konfigurace určené `CreateDefaultBuilder` můžete přepsat a rozšířen o [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a jiné metody a metody rozšíření [ IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="51dc5-138">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="51dc5-139">Následuje několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="51dc5-139">A few examples follow:</span></span>

* <span data-ttu-id="51dc5-140">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) slouží k určení dalších `IConfiguration` pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="51dc5-140">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="51dc5-141">Následující `ConfigureAppConfiguration` volání přidá delegáta a nezahrnují konfiguraci aplikace v *appsettings.xml* souboru.</span><span class="sxs-lookup"><span data-stu-id="51dc5-141">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="51dc5-142">`ConfigureAppConfiguration` může být volána více než jednou.</span><span class="sxs-lookup"><span data-stu-id="51dc5-142">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="51dc5-143">Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí).</span><span class="sxs-lookup"><span data-stu-id="51dc5-143">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="51dc5-144">Najdete v článku [hostovat konfigurační hodnoty](#host-configuration-values) oddílu.</span><span class="sxs-lookup"><span data-stu-id="51dc5-144">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="51dc5-145">Následující `ConfigureLogging` volání přidá delegáta konfigurace minimální protokolování úrovně ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) k [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="51dc5-145">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="51dc5-146">Toto nastavení přepíše nastavení v *appsettings. Development.JSON* (`LogLevel.Debug`) a *appsettings. Production.JSON* (`LogLevel.Error`) nakonfiguroval `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-146">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="51dc5-147">`ConfigureLogging` může být volána více než jednou.</span><span class="sxs-lookup"><span data-stu-id="51dc5-147">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="51dc5-148">Následující volání `ConfigureKestrel` přepíše výchozí [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtů navázat, když Kestrel nakonfigurovaly `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="51dc5-148">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="51dc5-149">Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepíše výchozí [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtů navázat, když Kestrel nakonfigurovaly `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="51dc5-149">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="51dc5-150">*Obsahu kořenové* Určuje, kde hostitele vyhledává soubory obsahu, jako jsou například soubory zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="51dc5-150">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="51dc5-151">Při spuštění aplikace z kořenové složky projektu, kořenové složky projektu slouží jako kořenový adresář obsahu.</span><span class="sxs-lookup"><span data-stu-id="51dc5-151">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="51dc5-152">Toto je výchozí hodnotu použitou v [sady Visual Studio](https://www.visualstudio.com/) a [nové šablony pro dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="51dc5-152">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="51dc5-153">Další informace o konfiguraci aplikací najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-153">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="51dc5-154">Jako alternativu k použití statické `CreateDefaultBuilder` metoda vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) je podporované přístup pomocí ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="51dc5-154">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="51dc5-155">Další informace najdete v tématu na kartě ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="51dc5-155">For more information, see the ASP.NET Core 1.x tab.</span></span>

<span data-ttu-id="51dc5-156">Při nastavování hostitele, [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) metody lze zadat.</span><span class="sxs-lookup"><span data-stu-id="51dc5-156">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods can be provided.</span></span> <span data-ttu-id="51dc5-157">Pokud `Startup` Zadaná třída, musíte definovat `Configure` metoda.</span><span class="sxs-lookup"><span data-stu-id="51dc5-157">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="51dc5-158">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-158">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="51dc5-159">Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání.</span><span class="sxs-lookup"><span data-stu-id="51dc5-159">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="51dc5-160">Více volání `Configure` nebo `UseStartup` na `WebHostBuilder` nahradit předchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="51dc5-160">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="51dc5-161">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="51dc5-161">Host configuration values</span></span>

<span data-ttu-id="51dc5-162">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) závisí na následujících dvou přístupů k nastavení hodnoty konfigurace hostitele:</span><span class="sxs-lookup"><span data-stu-id="51dc5-162">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="51dc5-163">Tvůrce konfiguraci hostitele, která zahrnuje proměnné prostředí s formátem `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-163">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="51dc5-164">Například `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-164">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="51dc5-165">Rozšíření, jako [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz [přepsání konfigurace](#override-configuration) části).</span><span class="sxs-lookup"><span data-stu-id="51dc5-165">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="51dc5-166">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč.</span><span class="sxs-lookup"><span data-stu-id="51dc5-166">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="51dc5-167">Při nastavování hodnoty s `UseSetting`, je hodnota nastavena jako řetězec bez ohledu na typ.</span><span class="sxs-lookup"><span data-stu-id="51dc5-167">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="51dc5-168">Hostitel používá kterékoli z těchto možností nastaví hodnotu poslední.</span><span class="sxs-lookup"><span data-stu-id="51dc5-168">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="51dc5-169">Další informace najdete v tématu [konfigurace přepisování](#override-configuration) v další části.</span><span class="sxs-lookup"><span data-stu-id="51dc5-169">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="51dc5-170">Klíč aplikace (název)</span><span class="sxs-lookup"><span data-stu-id="51dc5-170">Application Key (Name)</span></span>

<span data-ttu-id="51dc5-171">[IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) vlastnost je nastavena automaticky při [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) je volána v průběhu vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="51dc5-171">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="51dc5-172">Je hodnota nastavena na název sestavení obsahující vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-172">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="51dc5-173">Pokud chcete explicitně nastavit hodnotu, použijte [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="51dc5-173">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

<span data-ttu-id="51dc5-174">**Klíč**: applicationName</span><span class="sxs-lookup"><span data-stu-id="51dc5-174">**Key**: applicationName</span></span>  
<span data-ttu-id="51dc5-175">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="51dc5-175">**Type**: *string*</span></span>  
<span data-ttu-id="51dc5-176">**Výchozí**: název sestavení obsahující vstupní bod aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-176">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="51dc5-177">**Sada s použitím**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="51dc5-177">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="51dc5-178">**Proměnná prostředí**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="51dc5-178">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="51dc5-179">Zachycení chyb při spuštění</span><span class="sxs-lookup"><span data-stu-id="51dc5-179">Capture Startup Errors</span></span>

<span data-ttu-id="51dc5-180">Toto nastavení řídí zaznamenávání chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="51dc5-180">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="51dc5-181">**Klíč**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="51dc5-181">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="51dc5-182">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="51dc5-182">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="51dc5-183">**Výchozí**: výchozí hodnota je `false` Pokud aplikace běží s Kestrel za služby IIS, kde je jako výchozí `true`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-183">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="51dc5-184">**Sada s použitím**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="51dc5-184">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="51dc5-185">**Proměnná prostředí**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="51dc5-185">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="51dc5-186">Když `false`, chyby během spuštění výsledku na hostiteli. operace bude ukončena.</span><span class="sxs-lookup"><span data-stu-id="51dc5-186">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="51dc5-187">Když `true`, zachytí výjimky během spouštění hostitele a se pokusí spustit na serveru.</span><span class="sxs-lookup"><span data-stu-id="51dc5-187">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="51dc5-188">Obsah kořenové</span><span class="sxs-lookup"><span data-stu-id="51dc5-188">Content Root</span></span>

<span data-ttu-id="51dc5-189">Toto nastavení určuje, kde ASP.NET Core začne vyhledávat soubory obsahu, například zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="51dc5-189">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="51dc5-190">**Klíč**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="51dc5-190">**Key**: contentRoot</span></span>  
<span data-ttu-id="51dc5-191">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="51dc5-191">**Type**: *string*</span></span>  
<span data-ttu-id="51dc5-192">**Výchozí**: výchozí hodnota je složka, ve které se nachází sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-192">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="51dc5-193">**Sada s použitím**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="51dc5-193">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="51dc5-194">**Proměnná prostředí**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="51dc5-194">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="51dc5-195">Obsah kořenové slouží také jako základní cesta pro [kořenový adresář webové nastavení](#web-root).</span><span class="sxs-lookup"><span data-stu-id="51dc5-195">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="51dc5-196">Pokud cesta neexistuje, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="51dc5-196">If the path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a><span data-ttu-id="51dc5-197">Podrobné chyby</span><span class="sxs-lookup"><span data-stu-id="51dc5-197">Detailed Errors</span></span>

<span data-ttu-id="51dc5-198">Určuje, zda podrobné chyby mají být zaznamenány.</span><span class="sxs-lookup"><span data-stu-id="51dc5-198">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="51dc5-199">**Klíč**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="51dc5-199">**Key**: detailedErrors</span></span>  
<span data-ttu-id="51dc5-200">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="51dc5-200">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="51dc5-201">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="51dc5-201">**Default**: false</span></span>  
<span data-ttu-id="51dc5-202">**Sada s použitím**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="51dc5-202">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="51dc5-203">**Proměnná prostředí**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="51dc5-203">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="51dc5-204">Při povolené (nebo když <a href="#environment">prostředí</a> je nastavena na `Development`), aplikace zaznamenává podrobné výjimky.</span><span class="sxs-lookup"><span data-stu-id="51dc5-204">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="51dc5-205">Prostředí</span><span class="sxs-lookup"><span data-stu-id="51dc5-205">Environment</span></span>

<span data-ttu-id="51dc5-206">Nastaví prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-206">Sets the app's environment.</span></span>

<span data-ttu-id="51dc5-207">**Klíč**: prostředí</span><span class="sxs-lookup"><span data-stu-id="51dc5-207">**Key**: environment</span></span>  
<span data-ttu-id="51dc5-208">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="51dc5-208">**Type**: *string*</span></span>  
<span data-ttu-id="51dc5-209">**Výchozí**: produkčního prostředí</span><span class="sxs-lookup"><span data-stu-id="51dc5-209">**Default**: Production</span></span>  
<span data-ttu-id="51dc5-210">**Sada s použitím**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="51dc5-210">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="51dc5-211">**Proměnná prostředí**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="51dc5-211">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="51dc5-212">Prostředí můžete nastavit na libovolnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="51dc5-212">The environment can be set to any value.</span></span> <span data-ttu-id="51dc5-213">Hodnoty definované v rámci rozhraní zahrnují `Development`, `Staging`, a `Production`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-213">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="51dc5-214">Hodnoty se velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="51dc5-214">Values aren't case sensitive.</span></span> <span data-ttu-id="51dc5-215">Ve výchozím nastavení *prostředí* číst z `ASPNETCORE_ENVIRONMENT` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-215">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="51dc5-216">Při použití [sady Visual Studio](https://www.visualstudio.com/), lze nastavit proměnné prostředí *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="51dc5-216">When using [Visual Studio](https://www.visualstudio.com/), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="51dc5-217">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-217">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="51dc5-218">Hostování při spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="51dc5-218">Hosting Startup Assemblies</span></span>

<span data-ttu-id="51dc5-219">Nastaví hostování sestavení po spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-219">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="51dc5-220">**Klíč**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="51dc5-220">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="51dc5-221">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="51dc5-221">**Type**: *string*</span></span>  
<span data-ttu-id="51dc5-222">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="51dc5-222">**Default**: Empty string</span></span>  
<span data-ttu-id="51dc5-223">**Sada s použitím**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="51dc5-223">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="51dc5-224">**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="51dc5-224">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="51dc5-225">Středníkem oddělený řetězec hostování načíst při spuštění po spuštění sestavení.</span><span class="sxs-lookup"><span data-stu-id="51dc5-225">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="51dc5-226">I když výchozí hodnota konfigurace je prázdný řetězec, hostování při spuštění sestavení vždy zahrnovat sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-226">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="51dc5-227">Při hostování při spuštění sestavení jsou k dispozici, se přidají do sestavení aplikace pro načtení, když aplikace je sestavena své běžné služby během spouštění.</span><span class="sxs-lookup"><span data-stu-id="51dc5-227">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="51dc5-228">HTTPS Port</span><span class="sxs-lookup"><span data-stu-id="51dc5-228">HTTPS Port</span></span>

<span data-ttu-id="51dc5-229">Nastavení přesměrování portu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="51dc5-229">Set the HTTPS redirect port.</span></span> <span data-ttu-id="51dc5-230">Použít v [vynucování HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="51dc5-230">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="51dc5-231">**Klíč**: https_port **typ**: *řetězec*
**výchozí**: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="51dc5-231">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="51dc5-232">**Sada s použitím**: `UseSetting` 
 **proměnnou prostředí**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="51dc5-232">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="51dc5-233">Hostování sestavení vyloučit při spuštění</span><span class="sxs-lookup"><span data-stu-id="51dc5-233">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="51dc5-234">Řetězec oddělený středníkem při spuštění sestavení mají vyloučit při spuštění hostování.</span><span class="sxs-lookup"><span data-stu-id="51dc5-234">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="51dc5-235">**Klíč**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="51dc5-235">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="51dc5-236">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="51dc5-236">**Type**: *string*</span></span>  
<span data-ttu-id="51dc5-237">**Výchozí**: prázdný řetězec</span><span class="sxs-lookup"><span data-stu-id="51dc5-237">**Default**: Empty string</span></span>  
<span data-ttu-id="51dc5-238">**Sada s použitím**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="51dc5-238">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="51dc5-239">**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="51dc5-239">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="51dc5-240">Dáváte přednost, který je hostitelem adresy URL</span><span class="sxs-lookup"><span data-stu-id="51dc5-240">Prefer Hosting URLs</span></span>

<span data-ttu-id="51dc5-241">Určuje, zda hostitel naslouchat požadavkům na adresy URL nakonfigurované `WebHostBuilder` místo nastavení nakonfigurovaného pomocí `IServer` implementace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-241">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="51dc5-242">**Klíč**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="51dc5-242">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="51dc5-243">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="51dc5-243">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="51dc5-244">**Výchozí**: true</span><span class="sxs-lookup"><span data-stu-id="51dc5-244">**Default**: true</span></span>  
<span data-ttu-id="51dc5-245">**Sada s použitím**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="51dc5-245">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="51dc5-246">**Proměnná prostředí**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="51dc5-246">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="51dc5-247">Zabránit spuštění hostování</span><span class="sxs-lookup"><span data-stu-id="51dc5-247">Prevent Hosting Startup</span></span>

<span data-ttu-id="51dc5-248">Brání automatické načítání hostování při spuštění sestavení, včetně hostování při spuštění sestavení nakonfiguroval sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-248">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="51dc5-249">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-249">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="51dc5-250">**Klíč**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="51dc5-250">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="51dc5-251">**Typ**: *bool* (`true` nebo `1`)</span><span class="sxs-lookup"><span data-stu-id="51dc5-251">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="51dc5-252">**Výchozí**: false</span><span class="sxs-lookup"><span data-stu-id="51dc5-252">**Default**: false</span></span>  
<span data-ttu-id="51dc5-253">**Sada s použitím**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="51dc5-253">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="51dc5-254">**Proměnná prostředí**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="51dc5-254">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="51dc5-255">Serverové adresy URL</span><span class="sxs-lookup"><span data-stu-id="51dc5-255">Server URLs</span></span>

<span data-ttu-id="51dc5-256">Určuje IP adresy nebo adresy hostitele s porty a protokoly, které server naslouchat požadavkům na požadavky.</span><span class="sxs-lookup"><span data-stu-id="51dc5-256">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="51dc5-257">**Klíč**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="51dc5-257">**Key**: urls</span></span>  
<span data-ttu-id="51dc5-258">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="51dc5-258">**Type**: *string*</span></span>  
<span data-ttu-id="51dc5-259">**Výchozí**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="51dc5-259">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="51dc5-260">**Sada s použitím**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="51dc5-260">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="51dc5-261">**Proměnná prostředí**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="51dc5-261">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="51dc5-262">Nastavte na oddělený středníkem (;) na server by měl odpovídat předpony adres seznam adresy URL.</span><span class="sxs-lookup"><span data-stu-id="51dc5-262">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="51dc5-263">Například `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-263">For example, `http://localhost:123`.</span></span> <span data-ttu-id="51dc5-264">Použití "\*" k označení, že by měl server naslouchat požadavkům na IP adresu nebo název hostitele zadaný port a protokol (například `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="51dc5-264">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="51dc5-265">Protokol (`http://` nebo `https://`) musí být součástí jednotlivé adresy URL.</span><span class="sxs-lookup"><span data-stu-id="51dc5-265">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="51dc5-266">Podporované formáty lišit mezi servery.</span><span class="sxs-lookup"><span data-stu-id="51dc5-266">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="51dc5-267">Kestrel má svůj vlastní konfigurace koncového bodu rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="51dc5-267">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="51dc5-268">Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-268">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="51dc5-269">Časový limit vypnutí</span><span class="sxs-lookup"><span data-stu-id="51dc5-269">Shutdown Timeout</span></span>

<span data-ttu-id="51dc5-270">Určuje dobu čekání webového hostitele pro vypnutí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-270">Specifies the amount of time to wait for the web host to shut down.</span></span>

<span data-ttu-id="51dc5-271">**Klíč**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="51dc5-271">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="51dc5-272">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="51dc5-272">**Type**: *int*</span></span>  
<span data-ttu-id="51dc5-273">**Výchozí**: 5</span><span class="sxs-lookup"><span data-stu-id="51dc5-273">**Default**: 5</span></span>  
<span data-ttu-id="51dc5-274">**Sada s použitím**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="51dc5-274">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="51dc5-275">**Proměnná prostředí**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="51dc5-275">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="51dc5-276">I když se přijme klíč *int* s `UseSetting` (například `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) rozšiřující metoda přijímá [časový interval](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="51dc5-276">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="51dc5-277">Během časového limitu, který je hostitelem:</span><span class="sxs-lookup"><span data-stu-id="51dc5-277">During the timeout period, hosting:</span></span>

* <span data-ttu-id="51dc5-278">Aktivační události [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="51dc5-278">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="51dc5-279">Pokusy o zastavení hostovaných služeb, protokolování všech chyb pro služby, které se nepodařilo zastavit.</span><span class="sxs-lookup"><span data-stu-id="51dc5-279">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="51dc5-280">Pokud časový limit vyprší před všechny zarážky hostovaných služeb, zastaví se všechny zbývající služby active při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-280">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="51dc5-281">Zastavení služeb i v případě, že se nedokončilo zpracování.</span><span class="sxs-lookup"><span data-stu-id="51dc5-281">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="51dc5-282">Pokud služby vyžadují další čas ukončení, zvýšit časový limit.</span><span class="sxs-lookup"><span data-stu-id="51dc5-282">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="51dc5-283">Po spuštění sestavení</span><span class="sxs-lookup"><span data-stu-id="51dc5-283">Startup Assembly</span></span>

<span data-ttu-id="51dc5-284">Určuje sestavení, které chcete vyhledat `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="51dc5-284">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="51dc5-285">**Klíč**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="51dc5-285">**Key**: startupAssembly</span></span>  
<span data-ttu-id="51dc5-286">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="51dc5-286">**Type**: *string*</span></span>  
<span data-ttu-id="51dc5-287">**Výchozí**: sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="51dc5-287">**Default**: The app's assembly</span></span>  
<span data-ttu-id="51dc5-288">**Sada s použitím**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="51dc5-288">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="51dc5-289">**Proměnná prostředí**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="51dc5-289">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="51dc5-290">Sestavení podle názvu (`string`) nebo typ (`TStartup`) může být odkazováno.</span><span class="sxs-lookup"><span data-stu-id="51dc5-290">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="51dc5-291">Pokud je položek víc `UseStartup` metody jsou volány, má přednost před poslední z nich.</span><span class="sxs-lookup"><span data-stu-id="51dc5-291">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="51dc5-292">Kořenový adresář webové</span><span class="sxs-lookup"><span data-stu-id="51dc5-292">Web Root</span></span>

<span data-ttu-id="51dc5-293">Nastaví relativní cestu k statických prostředků aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-293">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="51dc5-294">**Klíč**: webroot</span><span class="sxs-lookup"><span data-stu-id="51dc5-294">**Key**: webroot</span></span>  
<span data-ttu-id="51dc5-295">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="51dc5-295">**Type**: *string*</span></span>  
<span data-ttu-id="51dc5-296">**Výchozí**: Pokud se nezadá, výchozí hodnota je "(Content Root)/wwwroot", pokud cesta existuje.</span><span class="sxs-lookup"><span data-stu-id="51dc5-296">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="51dc5-297">Pokud cesta neexistuje, no-op soubor zprostředkovatele slouží.</span><span class="sxs-lookup"><span data-stu-id="51dc5-297">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="51dc5-298">**Sada s použitím**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="51dc5-298">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="51dc5-299">**Proměnná prostředí**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="51dc5-299">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a><span data-ttu-id="51dc5-300">Přepsání konfigurace</span><span class="sxs-lookup"><span data-stu-id="51dc5-300">Override configuration</span></span>

<span data-ttu-id="51dc5-301">Použití [konfigurace](xref:fundamentals/configuration/index) ke konfiguraci webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="51dc5-301">Use [Configuration](xref:fundamentals/configuration/index) to configure the web host.</span></span> <span data-ttu-id="51dc5-302">V následujícím příkladu je konfigurace hostitele volitelně podle *hostsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="51dc5-302">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="51dc5-303">Všechny konfigurace načtena z *hostsettings.json* soubor lze přepsat pomocí argumentů příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="51dc5-303">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="51dc5-304">Konfigurace sestavení (v `config`) se používá ke konfiguraci hostitele s [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="51dc5-304">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="51dc5-305">`IWebHostBuilder` konfigurace je přidaný na konfiguraci aplikace, ale neplatí první&mdash; `ConfigureAppConfiguration` nemá vliv `IWebHostBuilder` konfigurace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-305">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="51dc5-306">Přepsání konfigurace poskytované `UseUrls` s *hostsettings.json* config argument příkazového řádku, první config druhý:</span><span class="sxs-lookup"><span data-stu-id="51dc5-306">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="51dc5-307">*hostsettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="51dc5-307">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="51dc5-308">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) rozšiřující metoda není aktuálně podporuje při analýze oddílu konfigurace vrácený `GetSection` (například `.UseConfiguration(Configuration.GetSection("section"))`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-308">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="51dc5-309">`GetSection` Metoda filtry konfigurační klíče do části Požadovaná, ale ponechá název oddílu, který na klíče (například `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="51dc5-309">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="51dc5-310">`UseConfiguration` Metoda očekává klíčů tak, aby odpovídaly `WebHostBuilder` klíče (například `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="51dc5-310">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="51dc5-311">Přítomnost název oddílu, který klíčů brání hodnoty v části Konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="51dc5-311">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="51dc5-312">Tento problém bude vyřešen v příští verzi.</span><span class="sxs-lookup"><span data-stu-id="51dc5-312">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="51dc5-313">Další informace a řešení najdete v tématu [předávání konfigurační oddíl do WebHostBuilder.UseConfiguration využívá úplnou klíčů](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="51dc5-313">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="51dc5-314">`UseConfiguration` zkopíruje jen klíče ze zadaného `IConfiguration` Tvůrce konfiguraci hostitele.</span><span class="sxs-lookup"><span data-stu-id="51dc5-314">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="51dc5-315">Proto nastavení `reloadOnChange: true` soubory XML, JSON a INI nastavení nemá žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="51dc5-315">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="51dc5-316">K určení hostitele spouštět na určitou adresu URL, na požadovanou hodnotu lze předat v příkazovém řádku při provádění [dotnet spustit](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="51dc5-316">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="51dc5-317">Argument příkazového řádku přepíše `urls` hodnotu *hostsettings.json* souboru a server naslouchá na portu 8080:</span><span class="sxs-lookup"><span data-stu-id="51dc5-317">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="51dc5-318">Spravovat hostitele</span><span class="sxs-lookup"><span data-stu-id="51dc5-318">Manage the host</span></span>

<span data-ttu-id="51dc5-319">**Spuštění**</span><span class="sxs-lookup"><span data-stu-id="51dc5-319">**Run**</span></span>

<span data-ttu-id="51dc5-320">`Run` Metoda spustí webovou aplikaci a blokuje volající vlákno, dokud nebude ukončen hostitele:</span><span class="sxs-lookup"><span data-stu-id="51dc5-320">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="51dc5-321">**Start**</span><span class="sxs-lookup"><span data-stu-id="51dc5-321">**Start**</span></span>

<span data-ttu-id="51dc5-322">Spustí hostitele způsobem neblokující zavoláním jeho `Start` metody:</span><span class="sxs-lookup"><span data-stu-id="51dc5-322">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="51dc5-323">Pokud je předán seznam adres URL `Start` metody naslouchá na zadaných adresách URL:</span><span class="sxs-lookup"><span data-stu-id="51dc5-323">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="51dc5-324">Aplikaci můžete inicializaci a spuštění nového hostitele pomocí předem nakonfigurovaných výchozích nastavení `CreateDefaultBuilder` pomocí pohodlí statické metody.</span><span class="sxs-lookup"><span data-stu-id="51dc5-324">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="51dc5-325">Tyto metody spuštění serveru bez výstup na konzole a s [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) počkejte přerušení (Ctrl-C/SIGINT nebo SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="51dc5-325">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="51dc5-326">**Spuštění (RequestDelegate aplikace)**</span><span class="sxs-lookup"><span data-stu-id="51dc5-326">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="51dc5-327">Začněte `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="51dc5-327">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="51dc5-328">Vytvořit žádost o v prohlížeči `http://localhost:5000` trvá příjem odpovědi "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="51dc5-328">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="51dc5-329">`WaitForShutdown` blokuje, dokud se vydává přerušení (Ctrl-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="51dc5-329">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="51dc5-330">Aplikace zobrazí `Console.WriteLine` zprávu a čeká na stisknutí klávesy ukončete.</span><span class="sxs-lookup"><span data-stu-id="51dc5-330">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="51dc5-331">**Spustit (řetězec adresu url, RequestDelegate aplikace)**</span><span class="sxs-lookup"><span data-stu-id="51dc5-331">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="51dc5-332">Začněte s adresou URL a `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="51dc5-332">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="51dc5-333">Vytvoří stejný výsledek jako **Start (RequestDelegate aplikace)**, s výjimkou aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-333">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="51dc5-334">**Spustit (akce&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="51dc5-334">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="51dc5-335">Použijte instanci `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) používat middleware směrování:</span><span class="sxs-lookup"><span data-stu-id="51dc5-335">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="51dc5-336">Použijte následující požadavky na prohlížeč s příkladem:</span><span class="sxs-lookup"><span data-stu-id="51dc5-336">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="51dc5-337">Požadavek</span><span class="sxs-lookup"><span data-stu-id="51dc5-337">Request</span></span>                                    | <span data-ttu-id="51dc5-338">Odpověď</span><span class="sxs-lookup"><span data-stu-id="51dc5-338">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="51dc5-339">Dobrý den, Martin!</span><span class="sxs-lookup"><span data-stu-id="51dc5-339">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="51dc5-340">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="51dc5-340">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="51dc5-341">Vyvolá výjimku s řetězcem "ooops!"</span><span class="sxs-lookup"><span data-stu-id="51dc5-341">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="51dc5-342">Vyvolá výjimku s řetězcem "Uh ale!"</span><span class="sxs-lookup"><span data-stu-id="51dc5-342">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="51dc5-343">Santé, Kevin!</span><span class="sxs-lookup"><span data-stu-id="51dc5-343">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="51dc5-344">Ahoj světe!</span><span class="sxs-lookup"><span data-stu-id="51dc5-344">Hello World!</span></span>                             |

<span data-ttu-id="51dc5-345">`WaitForShutdown` blokuje, dokud se vydává přerušení (Ctrl-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="51dc5-345">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="51dc5-346">Aplikace zobrazí `Console.WriteLine` zprávu a čeká na stisknutí klávesy ukončete.</span><span class="sxs-lookup"><span data-stu-id="51dc5-346">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="51dc5-347">**Spustit (řetězec adresy url, akce&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="51dc5-347">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="51dc5-348">Použijte adresu URL a instance `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="51dc5-348">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="51dc5-349">Vytvoří stejný výsledek jako **spuštění (akce&lt;IRouteBuilder&gt; routeBuilder)**, s výjimkou aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-349">Produces the same result as **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="51dc5-350">**StartWith (akce&lt;IApplicationBuilder&gt; aplikace)**</span><span class="sxs-lookup"><span data-stu-id="51dc5-350">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="51dc5-351">Zadejte delegát pro konfiguraci `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="51dc5-351">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="51dc5-352">Vytvořit žádost o v prohlížeči `http://localhost:5000` trvá příjem odpovědi "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="51dc5-352">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="51dc5-353">`WaitForShutdown` blokuje, dokud se vydává přerušení (Ctrl-C/SIGINT nebo SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="51dc5-353">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="51dc5-354">Aplikace zobrazí `Console.WriteLine` zprávu a čeká na stisknutí klávesy ukončete.</span><span class="sxs-lookup"><span data-stu-id="51dc5-354">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="51dc5-355">**StartWith (řetězec adresy url, akce&lt;IApplicationBuilder&gt; aplikace)**</span><span class="sxs-lookup"><span data-stu-id="51dc5-355">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="51dc5-356">Zadejte adresu URL a delegát pro konfiguraci `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="51dc5-356">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="51dc5-357">Vytvoří stejný výsledek jako **StartWith (akce&lt;IApplicationBuilder&gt; aplikace)**, s výjimkou aplikace reaguje na `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-357">Produces the same result as **StartWith(Action&lt;IApplicationBuilder&gt; app)**, except the app responds on `http://localhost:8080`.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="51dc5-358">IHostingEnvironment rozhraní</span><span class="sxs-lookup"><span data-stu-id="51dc5-358">IHostingEnvironment interface</span></span>

<span data-ttu-id="51dc5-359">[IHostingEnvironment rozhraní](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o hostování prostředí webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-359">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="51dc5-360">Použít [konstruktor vkládání](xref:fundamentals/dependency-injection) získat `IHostingEnvironment` Chcete-li použít její vlastnosti a metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="51dc5-360">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="51dc5-361">A [přístup podle úmluvy](xref:fundamentals/environments#environment-based-startup-class-and-methods) slouží ke konfiguraci aplikace při spuštění na základě prostředí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-361">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="51dc5-362">Můžete také vložit `IHostingEnvironment` do `Startup` konstruktoru pro použití v `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="51dc5-362">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="51dc5-363">Kromě `IsDevelopment` rozšiřující metoda `IHostingEnvironment` nabízí `IsStaging`, `IsProduction`, a `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="51dc5-363">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="51dc5-364">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="51dc5-364">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="51dc5-365">`IHostingEnvironment` Service můžete také vloženy přímo do `Configure` metodu pro vytvoření kanálu zpracování:</span><span class="sxs-lookup"><span data-stu-id="51dc5-365">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
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

<span data-ttu-id="51dc5-366">`IHostingEnvironment` mohou být vloženy do `Invoke` metoda při vytváření vlastní [middleware](xref:fundamentals/middleware/index#write-middleware):</span><span class="sxs-lookup"><span data-stu-id="51dc5-366">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/index#write-middleware):</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="51dc5-367">IApplicationLifetime rozhraní</span><span class="sxs-lookup"><span data-stu-id="51dc5-367">IApplicationLifetime interface</span></span>

<span data-ttu-id="51dc5-368">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje po spuštění a vypnutí aktivity.</span><span class="sxs-lookup"><span data-stu-id="51dc5-368">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="51dc5-369">Tři vlastnosti v rozhraní jsou tokeny zrušení použije k registraci `Action` metody, které definují události spuštění a vypnutí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-369">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="51dc5-370">Token zrušení</span><span class="sxs-lookup"><span data-stu-id="51dc5-370">Cancellation Token</span></span>    | <span data-ttu-id="51dc5-371">Při aktivaci&#8230;</span><span class="sxs-lookup"><span data-stu-id="51dc5-371">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="51dc5-372">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="51dc5-372">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="51dc5-373">Hostitel plně byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="51dc5-373">The host has fully started.</span></span> |
| [<span data-ttu-id="51dc5-374">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="51dc5-374">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="51dc5-375">Hostitel se dokončuje řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-375">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="51dc5-376">By měl zpracovat všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="51dc5-376">All requests should be processed.</span></span> <span data-ttu-id="51dc5-377">Vypnutí blokuje, dokud se tato událost se dokončí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-377">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="51dc5-378">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="51dc5-378">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="51dc5-379">Hostitel provádí řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-379">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="51dc5-380">Žádosti se možná ještě zpracovávají.</span><span class="sxs-lookup"><span data-stu-id="51dc5-380">Requests may still be processing.</span></span> <span data-ttu-id="51dc5-381">Vypnutí blokuje, dokud se tato událost se dokončí.</span><span class="sxs-lookup"><span data-stu-id="51dc5-381">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="51dc5-382">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) požaduje ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-382">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="51dc5-383">Následující třídy používá `StopApplication` řádně vypnout aplikaci při třídy `Shutdown` volání metody:</span><span class="sxs-lookup"><span data-stu-id="51dc5-383">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="51dc5-384">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="51dc5-384">Scope validation</span></span>

<span data-ttu-id="51dc5-385">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) k `true` jestli vývojové prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-385">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="51dc5-386">Když `ValidateScopes` je nastavena na `true`, poskytovatele služeb výchozí provádí kontroly pro ověření, že:</span><span class="sxs-lookup"><span data-stu-id="51dc5-386">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="51dc5-387">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="51dc5-387">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="51dc5-388">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="51dc5-388">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="51dc5-389">Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="51dc5-389">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="51dc5-390">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-390">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="51dc5-391">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="51dc5-391">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="51dc5-392">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="51dc5-392">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="51dc5-393">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="51dc5-393">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="51dc5-394">Vždy ověřovaly obory, včetně produkčního prostředí, nakonfigurujte [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) s [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrce hostitele:</span><span class="sxs-lookup"><span data-stu-id="51dc5-394">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="51dc5-395">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="51dc5-395">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
