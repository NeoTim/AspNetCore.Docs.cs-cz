---
title: ASP.NET Core hostitele ve službě systému Windows
author: guardrex
description: Naučte se hostovat aplikaci ASP.NET Core ve službě systému Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 71f7bf3f5dcf8068d0ada03675ef7948267b79f4
ms.sourcegitcommit: bd896935e91236e03241f75e6534ad6debcecbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2020
ms.locfileid: "77044899"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="bb999-103">ASP.NET Core hostitele ve službě systému Windows</span><span class="sxs-lookup"><span data-stu-id="bb999-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="bb999-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bb999-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bb999-105">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="bb999-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="bb999-106">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="bb999-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="bb999-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bb999-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bb999-108">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="bb999-108">Prerequisites</span></span>

* [<span data-ttu-id="bb999-109">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="bb999-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="bb999-110">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="bb999-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="bb999-111">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="bb999-111">Worker Service template</span></span>

<span data-ttu-id="bb999-112">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="bb999-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="bb999-113">Použití šablony jako základu pro aplikaci služby systému Windows:</span><span class="sxs-lookup"><span data-stu-id="bb999-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="bb999-114">Vytvořte aplikaci pracovní služby ze šablony .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb999-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="bb999-115">Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.</span><span class="sxs-lookup"><span data-stu-id="bb999-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="bb999-116">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="bb999-116">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb999-117">Aplikace vyžaduje odkaz na balíček pro [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="bb999-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="bb999-118">`IHostBuilder.UseWindowsService` se volá při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="bb999-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="bb999-119">Pokud je aplikace spuštěná jako služba systému Windows, metoda:</span><span class="sxs-lookup"><span data-stu-id="bb999-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="bb999-120">Nastaví dobu života hostitele na `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="bb999-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="bb999-121">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="bb999-121">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="bb999-122">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="bb999-122">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="bb999-123">Povolí protokolování do protokolu událostí:</span><span class="sxs-lookup"><span data-stu-id="bb999-123">Enables logging to the event log:</span></span>
  * <span data-ttu-id="bb999-124">Název aplikace se používá jako výchozí název zdroje.</span><span class="sxs-lookup"><span data-stu-id="bb999-124">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="bb999-125">Výchozí úroveň protokolu je *Upozornění* nebo vyšší pro aplikaci na základě šablony ASP.NET Core, která volá `CreateDefaultBuilder` k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="bb999-125">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="bb999-126">Přepište výchozí úroveň protokolu klíčem `Logging:EventLog:LogLevel:Default` v souboru *appSettings. json*/*appSettings. { Environment}. JSON* nebo jiný poskytovatel konfigurace.</span><span class="sxs-lookup"><span data-stu-id="bb999-126">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="bb999-127">Pouze správci mohou vytvářet nové zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="bb999-127">Only administrators can create new event sources.</span></span> <span data-ttu-id="bb999-128">Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="bb999-128">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="bb999-129">V `CreateHostBuilder` *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bb999-129">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="bb999-130">Toto téma doprovází následující ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="bb999-130">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="bb999-131">Ukázka služby pracovní proces na pozadí &ndash; ukázka newebové aplikace v závislosti na [šabloně pracovní služby](#worker-service-template) , která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="bb999-131">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="bb999-132">Ukázka webového App Service &ndash; ukázkovou webovou aplikaci Razor Pages, která se spouští jako služba systému Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="bb999-132">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="bb999-133">Pokyny pro MVC najdete v článcích <xref:mvc/overview> a <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="bb999-133">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bb999-134">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="bb999-134">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="bb999-135">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb999-135">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="bb999-136">Zkontrolujte, zda je ladicí program připojen nebo zda je k dispozici `--console` přepínač.</span><span class="sxs-lookup"><span data-stu-id="bb999-136">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="bb999-137">Pokud má kterákoli podmínka hodnotu true (aplikace není spuštěná jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="bb999-137">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="bb999-138">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="bb999-138">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="bb999-139">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb999-139">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="bb999-140">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C:\\Windows\\system32* při volání <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="bb999-140">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="bb999-141">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="bb999-141">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="bb999-142">Tento krok se provádí před konfigurací aplikace v `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="bb999-142">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="bb999-143">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="bb999-143">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="bb999-144">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je z argumentů odebraný, než <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijme argumenty.</span><span class="sxs-lookup"><span data-stu-id="bb999-144">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="bb999-145">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="bb999-145">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="bb999-146">Úroveň protokolování nastavte pomocí klíče `Logging:LogLevel:Default` v souboru *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="bb999-146">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="bb999-147">V následujícím příkladu z ukázkové aplikace je `RunAsCustomService` volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>, aby mohla zpracovávat události životního cyklu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb999-147">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="bb999-148">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="bb999-148">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="bb999-149">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="bb999-149">Deployment type</span></span>

<span data-ttu-id="bb999-150">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="bb999-150">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="bb999-151">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="bb999-151">SDK</span></span>

<span data-ttu-id="bb999-152">U služby založené na webové aplikaci, která používá Razor Pages nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="bb999-152">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="bb999-153">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="bb999-153">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="bb999-154">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="bb999-154">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="bb999-155">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="bb999-155">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="bb999-156">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor ( *. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="bb999-156">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb999-157">Pokud používáte [webovou sadu SDK](#sdk), soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci služby systému Windows zapotřebí.</span><span class="sxs-lookup"><span data-stu-id="bb999-157">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="bb999-158">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavena na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="bb999-158">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bb999-159">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="bb999-159">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="bb999-160">V následujícím příkladu je identifikátor RID nastaven na `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="bb999-160">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="bb999-161">Vlastnost `<SelfContained>` je nastavena na hodnotu `false`.</span><span class="sxs-lookup"><span data-stu-id="bb999-161">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="bb999-162">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb999-162">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="bb999-163">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="bb999-163">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="bb999-164">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavena na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="bb999-164">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="bb999-165">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="bb999-165">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="bb999-166">V následujícím příkladu je identifikátor RID nastaven na `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="bb999-166">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="bb999-167">Vlastnost `<SelfContained>` je nastavena na hodnotu `false`.</span><span class="sxs-lookup"><span data-stu-id="bb999-167">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="bb999-168">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb999-168">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="bb999-169">Vlastnost `<UseAppHost>` je nastavena na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="bb999-169">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="bb999-170">Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.</span><span class="sxs-lookup"><span data-stu-id="bb999-170">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="bb999-171">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="bb999-171">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="bb999-172">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavena na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="bb999-172">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="bb999-173">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="bb999-173">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="bb999-174">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="bb999-174">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="bb999-175">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="bb999-175">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="bb999-176">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>`, který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="bb999-176">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="bb999-177">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="bb999-177">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="bb999-178">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="bb999-178">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="bb999-179">Použijte název vlastnosti [\<RuntimeIdentifiers >](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="bb999-179">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="bb999-180">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="bb999-180">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bb999-181">Vlastnost `<SelfContained>` je nastavená na `true`:</span><span class="sxs-lookup"><span data-stu-id="bb999-181">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="bb999-182">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="bb999-182">Service user account</span></span>

<span data-ttu-id="bb999-183">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="bb999-183">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="bb999-184">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="bb999-184">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="bb999-185">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="bb999-185">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="bb999-186">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="bb999-186">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="bb999-187">Pokud není parametr `-AccountExpires` zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s <xref:System.DateTime>vypršení platnosti, platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="bb999-187">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="bb999-188">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="bb999-188">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="bb999-189">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="bb999-189">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="bb999-190">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="bb999-190">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="bb999-191">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="bb999-191">Log on as a service rights</span></span>

<span data-ttu-id="bb999-192">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="bb999-192">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="bb999-193">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="bb999-193">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="bb999-194">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="bb999-194">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="bb999-195">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="bb999-195">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="bb999-196">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="bb999-196">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="bb999-197">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="bb999-197">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="bb999-198">Do pole název objektu zadejte uživatelský účet (`{DOMAIN OR COMPUTER NAME\USER}`) a kliknutím na **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="bb999-198">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="bb999-199">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="bb999-199">Select **Advanced**.</span></span> <span data-ttu-id="bb999-200">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="bb999-200">Select **Find Now**.</span></span> <span data-ttu-id="bb999-201">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="bb999-201">Select the user account from the list.</span></span> <span data-ttu-id="bb999-202">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="bb999-202">Select **OK**.</span></span> <span data-ttu-id="bb999-203">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="bb999-203">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="bb999-204">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="bb999-204">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="bb999-205">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="bb999-205">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="bb999-206">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="bb999-206">Create a service</span></span>

<span data-ttu-id="bb999-207">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="bb999-207">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="bb999-208">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="bb999-208">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="bb999-209">`{EXE PATH}` &ndash; cestu ke složce aplikace na hostiteli (například `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="bb999-209">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="bb999-210">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb999-210">Don't include the app's executable in the path.</span></span> <span data-ttu-id="bb999-211">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="bb999-211">A trailing slash isn't required.</span></span>
* <span data-ttu-id="bb999-212">uživatelský účet služby `{DOMAIN OR COMPUTER NAME\USER}` &ndash; (například `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="bb999-212">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="bb999-213">název služby `{SERVICE NAME}` &ndash; (například `MyService`).</span><span class="sxs-lookup"><span data-stu-id="bb999-213">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="bb999-214">`{EXE FILE PATH}` &ndash; cestu ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="bb999-214">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="bb999-215">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="bb999-215">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="bb999-216">Popis služby `{DESCRIPTION}` &ndash; (například `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="bb999-216">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="bb999-217">Zobrazovaný název služby `{DISPLAY NAME}` &ndash; (například `My Service`).</span><span class="sxs-lookup"><span data-stu-id="bb999-217">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="bb999-218">Spustit službu</span><span class="sxs-lookup"><span data-stu-id="bb999-218">Start a service</span></span>

<span data-ttu-id="bb999-219">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="bb999-219">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="bb999-220">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="bb999-220">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="bb999-221">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="bb999-221">Determine a service's status</span></span>

<span data-ttu-id="bb999-222">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="bb999-222">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="bb999-223">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="bb999-223">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="bb999-224">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="bb999-224">Stop a service</span></span>

<span data-ttu-id="bb999-225">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="bb999-225">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="bb999-226">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="bb999-226">Remove a service</span></span>

<span data-ttu-id="bb999-227">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="bb999-227">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="bb999-228">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="bb999-228">Handle starting and stopping events</span></span>

<span data-ttu-id="bb999-229">Zpracování událostí <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="bb999-229">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="bb999-230">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> pomocí metod `OnStarting`, `OnStarted`a `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="bb999-230">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="bb999-231">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost>, která předá `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="bb999-231">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="bb999-232">V `Program.Main`volejte metodu rozšíření `RunAsCustomService` namísto <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="bb999-232">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="bb999-233">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, přečtěte si ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="bb999-233">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="bb999-234">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="bb999-234">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="bb999-235">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="bb999-235">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="bb999-236">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="bb999-236">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="bb999-237">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="bb999-237">Configure endpoints</span></span>

<span data-ttu-id="bb999-238">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="bb999-238">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="bb999-239">Nakonfigurujte adresu URL a port nastavením proměnné prostředí `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="bb999-239">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="bb999-240">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="bb999-240">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="bb999-241">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bb999-241">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="bb999-242">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="bb999-242">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="bb999-243">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="bb999-243">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="bb999-244">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="bb999-244">Current directory and content root</span></span>

<span data-ttu-id="bb999-245">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C:\\Windows\\system32* .</span><span class="sxs-lookup"><span data-stu-id="bb999-245">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="bb999-246">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="bb999-246">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="bb999-247">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="bb999-247">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="bb999-248">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="bb999-248">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="bb999-249">K vyhledání prostředků aplikace použijte [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="bb999-249">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="bb999-250">Když aplikace běží jako služba, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> nastaví <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="bb999-250">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="bb999-251">Soubory výchozích nastavení aplikace, *appSettings. JSON* a *appSettings. { Environment}. JSON*jsou načteny z kořenu obsahu aplikace voláním [CreateDefaultBuilder během vytváření hostitele](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="bb999-251">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="bb999-252">Pro jiné soubory nastavení načtené vývojářským kódem v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>není nutné volat <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="bb999-252">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="bb999-253">V následujícím příkladu soubor *custom_settings. JSON* existuje v kořenu obsahu aplikace a je načtený bez explicitního nastavení základní cesty:</span><span class="sxs-lookup"><span data-stu-id="bb999-253">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="bb999-254">Nepokoušejte se použít <xref:System.IO.Directory.GetCurrentDirectory*> k získání cesty prostředku, protože aplikace služby systému Windows vrátí složku *C:\\Windows\\system32* jako svůj aktuální adresář.</span><span class="sxs-lookup"><span data-stu-id="bb999-254">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="bb999-255">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="bb999-255">Set the content root path to the app's folder</span></span>

<span data-ttu-id="bb999-256"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> je při vytvoření služby stejná cesta k argumentu `binPath`.</span><span class="sxs-lookup"><span data-stu-id="bb999-256">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="bb999-257">Namísto volání `GetCurrentDirectory` k vytvoření cest k souborům nastavení zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb999-257">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="bb999-258">V `Program.Main`Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="bb999-258">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="bb999-259">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="bb999-259">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="bb999-260">Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.</span><span class="sxs-lookup"><span data-stu-id="bb999-260">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="bb999-261">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="bb999-261">Troubleshoot</span></span>

<span data-ttu-id="bb999-262">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="bb999-262">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="bb999-263">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="bb999-263">Common errors</span></span>

* <span data-ttu-id="bb999-264">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="bb999-264">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="bb999-265">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="bb999-265">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="bb999-266">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="bb999-266">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="bb999-267">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="bb999-267">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="bb999-268">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="bb999-268">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="bb999-269">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="bb999-269">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="bb999-270">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="bb999-270">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="bb999-271">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="bb999-271">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="bb999-272">Základní cesta služby systému Windows je *c:\\Windows\\system32*.</span><span class="sxs-lookup"><span data-stu-id="bb999-272">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="bb999-273">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="bb999-273">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="bb999-274">Při provádění příkazu `New-Service` PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně.</span><span class="sxs-lookup"><span data-stu-id="bb999-274">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="bb999-275">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="bb999-275">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="bb999-276">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="bb999-276">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="bb999-277">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="bb999-277">System and Application Event Logs</span></span>

<span data-ttu-id="bb999-278">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="bb999-278">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="bb999-279">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="bb999-279">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="bb999-280">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="bb999-280">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="bb999-281">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="bb999-281">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="bb999-282">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb999-282">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="bb999-283">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb999-283">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="bb999-284">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="bb999-284">Run the app at a command prompt</span></span>

<span data-ttu-id="bb999-285">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="bb999-285">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="bb999-286">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="bb999-286">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="bb999-287">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="bb999-287">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="bb999-288">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="bb999-288">Clear package caches</span></span>

<span data-ttu-id="bb999-289">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="bb999-289">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="bb999-290">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="bb999-290">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="bb999-291">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="bb999-291">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="bb999-292">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="bb999-292">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="bb999-293">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="bb999-293">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="bb999-294">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a spuštěním příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="bb999-294">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="bb999-295">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="bb999-295">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="bb999-296">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="bb999-296">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="bb999-297">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="bb999-297">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="bb999-298">Pomalá nebo Změ aplikace</span><span class="sxs-lookup"><span data-stu-id="bb999-298">Slow or hanging app</span></span>

<span data-ttu-id="bb999-299">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb999-299">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="bb999-300">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="bb999-300">App crashes or encounters an exception</span></span>

<span data-ttu-id="bb999-301">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="bb999-301">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="bb999-302">Vytvořte složku, do které se budou ukládat soubory s výpisem stavu systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="bb999-302">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="bb999-303">Spusťte [skript PowerShellu EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="bb999-303">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="bb999-304">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="bb999-304">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="bb999-305">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="bb999-305">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="bb999-306">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="bb999-306">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="bb999-307">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb999-307">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="bb999-308">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="bb999-308">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="bb999-309">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="bb999-309">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="bb999-310">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="bb999-310">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="bb999-311">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="bb999-311">Analyze the dump</span></span>

<span data-ttu-id="bb999-312">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="bb999-312">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="bb999-313">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="bb999-313">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb999-314">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bb999-314">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="bb999-315">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="bb999-315">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="bb999-316">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="bb999-316">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
