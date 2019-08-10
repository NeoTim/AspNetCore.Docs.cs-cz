---
title: ASP.NET Core hostitele ve službě systému Windows
author: guardrex
description: Naučte se hostovat aplikaci ASP.NET Core ve službě systému Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/03/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 703edff19d82d10415bedb9b92d83709c275100f
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913921"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="cc277-103">ASP.NET Core hostitele ve službě systému Windows</span><span class="sxs-lookup"><span data-stu-id="cc277-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="cc277-104">[Luke Latham](https://github.com/guardrex) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="cc277-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="cc277-105">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="cc277-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="cc277-106">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="cc277-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="cc277-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cc277-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc277-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="cc277-108">Prerequisites</span></span>

* [<span data-ttu-id="cc277-109">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cc277-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="cc277-110">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cc277-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="cc277-111">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="cc277-111">Worker Service template</span></span>

<span data-ttu-id="cc277-112">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="cc277-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="cc277-113">Použití šablony jako základu pro aplikaci služby systému Windows:</span><span class="sxs-lookup"><span data-stu-id="cc277-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="cc277-114">Vytvořte aplikaci pracovní služby ze šablony .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc277-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="cc277-115">Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.</span><span class="sxs-lookup"><span data-stu-id="cc277-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cc277-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc277-116">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cc277-117">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="cc277-117">Create a new project.</span></span>
1. <span data-ttu-id="cc277-118">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="cc277-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cc277-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="cc277-119">Select **Next**.</span></span>
1. <span data-ttu-id="cc277-120">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="cc277-120">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="cc277-121">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cc277-121">Select **Create**.</span></span>
1. <span data-ttu-id="cc277-122">V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .</span><span class="sxs-lookup"><span data-stu-id="cc277-122">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="cc277-123">Vyberte šablonu **služby pracovního procesu** .</span><span class="sxs-lookup"><span data-stu-id="cc277-123">Select the **Worker Service** template.</span></span> <span data-ttu-id="cc277-124">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cc277-124">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cc277-125">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc277-125">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cc277-126">Použijte šablonu Worker Service`worker`() s příkazem [dotnet New](/dotnet/core/tools/dotnet-new) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="cc277-126">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="cc277-127">V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorkerService`.</span><span class="sxs-lookup"><span data-stu-id="cc277-127">In the following example, a Worker Service app is created named `ContosoWorkerService`.</span></span> <span data-ttu-id="cc277-128">Složka pro `ContosoWorkerService` aplikaci se vytvoří automaticky při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="cc277-128">A folder for the `ContosoWorkerService` app is created automatically when the command is executed.</span></span>

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="cc277-129">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="cc277-129">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc277-130">`IHostBuilder.UseWindowsService`, poskytnutý balíčkem [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) , se volá při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="cc277-130">`IHostBuilder.UseWindowsService`, provided by the [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) package, is called when building the host.</span></span> <span data-ttu-id="cc277-131">Pokud je aplikace spuštěná jako služba systému Windows, metoda:</span><span class="sxs-lookup"><span data-stu-id="cc277-131">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="cc277-132">Nastaví dobu života hostitele na `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="cc277-132">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="cc277-133">Nastaví kořen obsahu.</span><span class="sxs-lookup"><span data-stu-id="cc277-133">Sets the content root.</span></span>
* <span data-ttu-id="cc277-134">Povolí protokolování do protokolu událostí s názvem aplikace jako s výchozím názvem zdroje.</span><span class="sxs-lookup"><span data-stu-id="cc277-134">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="cc277-135">Úroveň protokolu se dá nakonfigurovat pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cc277-135">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="cc277-136">Pouze správci mohou vytvářet nové zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="cc277-136">Only administrators can create new event sources.</span></span> <span data-ttu-id="cc277-137">Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="cc277-137">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc277-138">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="cc277-138">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="cc277-139">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc277-139">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="cc277-140">Zkontrolujte, zda je ladicí program připojen nebo `--console` zda je k dispozici přepínač.</span><span class="sxs-lookup"><span data-stu-id="cc277-140">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="cc277-141">Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="cc277-141">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="cc277-142">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="cc277-142">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="cc277-143">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc277-143">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="cc277-144">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C:\\Windows\\system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="cc277-144">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="cc277-145">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="cc277-145">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="cc277-146">Tento krok se provádí před konfigurací aplikace v `CreateWebHostBuilder`nástroji.</span><span class="sxs-lookup"><span data-stu-id="cc277-146">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="cc277-147">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="cc277-147">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="cc277-148">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového `--console` řádku, přepínač je odebrán z argumentů <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> před přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="cc277-148">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="cc277-149">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>do.</span><span class="sxs-lookup"><span data-stu-id="cc277-149">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="cc277-150">Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cc277-150">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="cc277-151">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> místo pro zpracování událostí životního cyklu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cc277-151">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="cc277-152">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="cc277-152">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="cc277-153">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="cc277-153">Deployment type</span></span>

<span data-ttu-id="cc277-154">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="cc277-154">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="cc277-155">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="cc277-155">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="cc277-156">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="cc277-156">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="cc277-157">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor ( *. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="cc277-157">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc277-158">Do souboru projektu přidejte následující prvky vlastností:</span><span class="sxs-lookup"><span data-stu-id="cc277-158">Add the following property elements to the project file:</span></span>

* <span data-ttu-id="cc277-159">`<OutputType>`Typ výstupu aplikace (`Exe` pro spustitelný soubor). &ndash;</span><span class="sxs-lookup"><span data-stu-id="cc277-159">`<OutputType>` &ndash; The app's output type (`Exe` for executable).</span></span>
* <span data-ttu-id="cc277-160">`<LangVersion>`&ndash; Verze C# jazyka(`latest` nebo`preview`).</span><span class="sxs-lookup"><span data-stu-id="cc277-160">`<LangVersion>` &ndash; The C# language version (`latest` or `preview`).</span></span>

<span data-ttu-id="cc277-161">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="cc277-161">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="cc277-162">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="cc277-162">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <OutputType>Exe</OutputType>
  <LangVersion>preview</LangVersion>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="cc277-163">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="cc277-163">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="cc277-164">V následujícím příkladu je identifikátor RID nastaven na `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="cc277-164">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="cc277-165">Vlastnost je nastavena na `false`hodnotu. `<SelfContained>`</span><span class="sxs-lookup"><span data-stu-id="cc277-165">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="cc277-166">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc277-166">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="cc277-167">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="cc277-167">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="cc277-168">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="cc277-168">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="cc277-169">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="cc277-169">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="cc277-170">V následujícím příkladu je identifikátor RID nastaven na `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="cc277-170">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="cc277-171">Vlastnost je nastavena na `false`hodnotu. `<SelfContained>`</span><span class="sxs-lookup"><span data-stu-id="cc277-171">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="cc277-172">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc277-172">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="cc277-173">Vlastnost je nastavena na `true`hodnotu. `<UseAppHost>`</span><span class="sxs-lookup"><span data-stu-id="cc277-173">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="cc277-174">Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.</span><span class="sxs-lookup"><span data-stu-id="cc277-174">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="cc277-175">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="cc277-175">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="cc277-176">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="cc277-176">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="cc277-177">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="cc277-177">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="cc277-178">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="cc277-178">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="cc277-179">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="cc277-179">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="cc277-180">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="cc277-180">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="cc277-181">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="cc277-181">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="cc277-182">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="cc277-182">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="cc277-183">Použijte název [ \<vlastnosti RuntimeIdentifiers >](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="cc277-183">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="cc277-184">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="cc277-184">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc277-185">Vlastnost je nastavena na `true`hodnotu: `<SelfContained>`</span><span class="sxs-lookup"><span data-stu-id="cc277-185">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="cc277-186">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="cc277-186">Service user account</span></span>

<span data-ttu-id="cc277-187">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="cc277-187">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="cc277-188">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="cc277-188">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="cc277-189">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="cc277-189">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="cc277-190">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="cc277-190">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="cc277-191">Pokud není <xref:System.DateTime>parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností, platnost účtu nekončí. `-AccountExpires`</span><span class="sxs-lookup"><span data-stu-id="cc277-191">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="cc277-192">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="cc277-192">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="cc277-193">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="cc277-193">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="cc277-194">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="cc277-194">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="cc277-195">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="cc277-195">Log on as a service rights</span></span>

<span data-ttu-id="cc277-196">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="cc277-196">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="cc277-197">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="cc277-197">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="cc277-198">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="cc277-198">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="cc277-199">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="cc277-199">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="cc277-200">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="cc277-200">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="cc277-201">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="cc277-201">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="cc277-202">Do pole název objektu zadejte`{DOMAIN OR COMPUTER NAME\USER}`uživatelský účet () a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="cc277-202">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="cc277-203">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="cc277-203">Select **Advanced**.</span></span> <span data-ttu-id="cc277-204">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="cc277-204">Select **Find Now**.</span></span> <span data-ttu-id="cc277-205">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="cc277-205">Select the user account from the list.</span></span> <span data-ttu-id="cc277-206">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="cc277-206">Select **OK**.</span></span> <span data-ttu-id="cc277-207">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="cc277-207">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="cc277-208">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="cc277-208">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="cc277-209">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="cc277-209">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="cc277-210">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="cc277-210">Create a service</span></span>

<span data-ttu-id="cc277-211">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="cc277-211">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="cc277-212">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cc277-212">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="cc277-213">`{EXE PATH}`Cesta ke složce aplikace na hostiteli ( `d:\myservice`například). &ndash;</span><span class="sxs-lookup"><span data-stu-id="cc277-213">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="cc277-214">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc277-214">Don't include the app's executable in the path.</span></span> <span data-ttu-id="cc277-215">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="cc277-215">A trailing slash isn't required.</span></span>
* <span data-ttu-id="cc277-216">`{DOMAIN OR COMPUTER NAME\USER}`Uživatelský účet služby ( `Contoso\ServiceUser`například). &ndash;</span><span class="sxs-lookup"><span data-stu-id="cc277-216">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="cc277-217">`{NAME}`Název služby ( `MyService`například). &ndash;</span><span class="sxs-lookup"><span data-stu-id="cc277-217">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="cc277-218">`{EXE FILE PATH}`Cesta ke spustitelnému souboru aplikace ( `d:\myservice\myservice.exe`například). &ndash;</span><span class="sxs-lookup"><span data-stu-id="cc277-218">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="cc277-219">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="cc277-219">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="cc277-220">`{DESCRIPTION}`Popis služby ( `My sample service`například). &ndash;</span><span class="sxs-lookup"><span data-stu-id="cc277-220">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="cc277-221">`{DISPLAY NAME}`Zobrazovaný název služby ( `My Service`například). &ndash;</span><span class="sxs-lookup"><span data-stu-id="cc277-221">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="cc277-222">Spustit službu</span><span class="sxs-lookup"><span data-stu-id="cc277-222">Start a service</span></span>

<span data-ttu-id="cc277-223">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="cc277-223">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="cc277-224">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="cc277-224">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="cc277-225">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="cc277-225">Determine a service's status</span></span>

<span data-ttu-id="cc277-226">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="cc277-226">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="cc277-227">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="cc277-227">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="cc277-228">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="cc277-228">Stop a service</span></span>

<span data-ttu-id="cc277-229">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="cc277-229">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="cc277-230">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="cc277-230">Remove a service</span></span>

<span data-ttu-id="cc277-231">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="cc277-231">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="cc277-232">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="cc277-232">Handle starting and stopping events</span></span>

<span data-ttu-id="cc277-233">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>událostí <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="cc277-233">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="cc277-234">Vytvořte třídu, která je odvozena <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> z `OnStarting`s metodami, `OnStopping` `OnStarted`a:</span><span class="sxs-lookup"><span data-stu-id="cc277-234">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="cc277-235">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která `CustomWebHostService` předá <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="cc277-235">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="cc277-236">V `Program.Main` <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>volejte metodu rozšíření místo: `RunAsCustomService`</span><span class="sxs-lookup"><span data-stu-id="cc277-236">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="cc277-237">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`nástroji, podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="cc277-237">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="cc277-238">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="cc277-238">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="cc277-239">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="cc277-239">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="cc277-240">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="cc277-240">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="cc277-241">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="cc277-241">Configure HTTPS</span></span>

<span data-ttu-id="cc277-242">Konfigurace služby pomocí zabezpečeného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="cc277-242">To configure a service with a secure endpoint:</span></span>

1. <span data-ttu-id="cc277-243">Vytvořte certifikát X. 509 pro hostující systém pomocí mechanismů pro získání a nasazení certifikátů vaší platformy.</span><span class="sxs-lookup"><span data-stu-id="cc277-243">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="cc277-244">Zadejte [konfiguraci koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) pro použití certifikátu.</span><span class="sxs-lookup"><span data-stu-id="cc277-244">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="cc277-245">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="cc277-245">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="cc277-246">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="cc277-246">Current directory and content root</span></span>

<span data-ttu-id="cc277-247">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C:\\Windows\\system32* .</span><span class="sxs-lookup"><span data-stu-id="cc277-247">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="cc277-248">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="cc277-248">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="cc277-249">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="cc277-249">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="cc277-250">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="cc277-250">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="cc277-251">K vyhledání prostředků aplikace použijte <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo.</span><span class="sxs-lookup"><span data-stu-id="cc277-251">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="cc277-252">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="cc277-252">Set the content root path to the app's folder</span></span>

<span data-ttu-id="cc277-253">Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby. <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*></span><span class="sxs-lookup"><span data-stu-id="cc277-253">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="cc277-254">Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k kořenu obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc277-254">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="cc277-255">V `Program.Main`nástroji Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="cc277-255">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="cc277-256">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="cc277-256">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="cc277-257"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> Při<xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> použití do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="cc277-257">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc277-258">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cc277-258">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="cc277-259">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="cc277-259">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="cc277-260">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="cc277-260">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
