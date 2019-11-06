---
title: ASP.NET Core hostitele ve službě systému Windows
author: guardrex
description: Naučte se hostovat aplikaci ASP.NET Core ve službě systému Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 014585cd1e170fc94f7f577e11ec19824e54572f
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659853"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="0c068-103">ASP.NET Core hostitele ve službě systému Windows</span><span class="sxs-lookup"><span data-stu-id="0c068-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="0c068-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0c068-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0c068-105">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0c068-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="0c068-106">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="0c068-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="0c068-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0c068-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c068-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0c068-108">Prerequisites</span></span>

* [<span data-ttu-id="0c068-109">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0c068-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="0c068-110">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0c068-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="0c068-111">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="0c068-111">Worker Service template</span></span>

<span data-ttu-id="0c068-112">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="0c068-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="0c068-113">Použití šablony jako základu pro aplikaci služby systému Windows:</span><span class="sxs-lookup"><span data-stu-id="0c068-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="0c068-114">Vytvořte aplikaci pracovní služby ze šablony .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c068-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="0c068-115">Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0c068-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="0c068-116">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="0c068-116">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c068-117">Aplikace vyžaduje odkaz na balíček pro [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="0c068-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="0c068-118">`IHostBuilder.UseWindowsService` se volá při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="0c068-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="0c068-119">Pokud je aplikace spuštěná jako služba systému Windows, metoda:</span><span class="sxs-lookup"><span data-stu-id="0c068-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="0c068-120">Nastaví dobu života hostitele na hodnotu `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="0c068-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="0c068-121">Nastaví [kořen obsahu](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="0c068-121">Sets the [content root](xref:fundamentals/index#content-root).</span></span>
* <span data-ttu-id="0c068-122">Povolí protokolování do protokolu událostí s názvem aplikace jako s výchozím názvem zdroje.</span><span class="sxs-lookup"><span data-stu-id="0c068-122">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="0c068-123">Úroveň protokolu lze konfigurovat pomocí klíče `Logging:LogLevel:Default` v souboru *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0c068-123">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="0c068-124">Pouze správci mohou vytvářet nové zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="0c068-124">Only administrators can create new event sources.</span></span> <span data-ttu-id="0c068-125">Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="0c068-125">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="0c068-126">V `CreateHostBuilder` *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="0c068-126">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="0c068-127">Toto téma doprovází následující ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="0c068-127">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="0c068-128">Ukázka služby pracovní proces na pozadí &ndash; ukázka newebové aplikace v závislosti na [šabloně pracovní služby](#worker-service-template) , která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0c068-128">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="0c068-129">Ukázka webového App Service &ndash; ukázkovou webovou aplikaci Razor Pages, která se spouští jako služba systému Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="0c068-129">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="0c068-130">Pokyny pro MVC najdete v článcích <xref:mvc/overview> a <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="0c068-130">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c068-131">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="0c068-131">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="0c068-132">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c068-132">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="0c068-133">Zkontrolujte, zda je ladicí program připojen nebo zda je k dispozici přepínač `--console`.</span><span class="sxs-lookup"><span data-stu-id="0c068-133">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="0c068-134">Pokud má kterákoli podmínka hodnotu true (aplikace není spuštěná jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="0c068-134">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="0c068-135">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="0c068-135">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="0c068-136">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c068-136">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="0c068-137">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C:\\Windows\\system32* při volání <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="0c068-137">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="0c068-138">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="0c068-138">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="0c068-139">Tento krok se provádí před konfigurací aplikace v `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0c068-139">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="0c068-140">Pokud chcete aplikaci spustit jako službu, zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>.</span><span class="sxs-lookup"><span data-stu-id="0c068-140">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="0c068-141">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, přepínač `--console` je z argumentů odebraný, než <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> obdrží argumenty.</span><span class="sxs-lookup"><span data-stu-id="0c068-141">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="0c068-142">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="0c068-142">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="0c068-143">Nastavte úroveň protokolování pomocí klíče `Logging:LogLevel:Default` v souboru *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0c068-143">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="0c068-144">V následujícím příkladu z ukázkové aplikace se volá `RunAsCustomService` místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>, aby bylo možné zpracovávat události životního cyklu v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c068-144">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="0c068-145">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="0c068-145">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="0c068-146">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="0c068-146">Deployment type</span></span>

<span data-ttu-id="0c068-147">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="0c068-147">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="0c068-148">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="0c068-148">SDK</span></span>

<span data-ttu-id="0c068-149">U služby založené na webové aplikaci, která používá Razor Pages nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="0c068-149">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="0c068-150">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="0c068-150">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="0c068-151">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="0c068-151">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="0c068-152">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="0c068-152">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="0c068-153">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor ( *. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="0c068-153">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c068-154">Pokud používáte [webovou sadu SDK](#sdk), soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci služby systému Windows zapotřebí.</span><span class="sxs-lookup"><span data-stu-id="0c068-154">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="0c068-155">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="0c068-155">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="0c068-156">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="0c068-156">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="0c068-157">V následujícím příkladu je identifikátor RID nastaven na `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="0c068-157">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="0c068-158">Vlastnost `<SelfContained>` je nastavena na hodnotu `false`.</span><span class="sxs-lookup"><span data-stu-id="0c068-158">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="0c068-159">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c068-159">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="0c068-160">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="0c068-160">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="0c068-161">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="0c068-161">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="0c068-162">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="0c068-162">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="0c068-163">V následujícím příkladu je identifikátor RID nastaven na `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="0c068-163">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="0c068-164">Vlastnost `<SelfContained>` je nastavena na hodnotu `false`.</span><span class="sxs-lookup"><span data-stu-id="0c068-164">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="0c068-165">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor ( *. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c068-165">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="0c068-166">Vlastnost `<UseAppHost>` je nastavena na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="0c068-166">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="0c068-167">Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.</span><span class="sxs-lookup"><span data-stu-id="0c068-167">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="0c068-168">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="0c068-168">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="0c068-169">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte vlastnost `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="0c068-169">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="0c068-170">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="0c068-170">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="0c068-171">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="0c068-171">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="0c068-172">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="0c068-172">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="0c068-173">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` obsahujícího cílové rozhraní:</span><span class="sxs-lookup"><span data-stu-id="0c068-173">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="0c068-174">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="0c068-174">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="0c068-175">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="0c068-175">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="0c068-176">Použijte název vlastnosti [\<RuntimeIdentifiers >](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="0c068-176">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="0c068-177">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="0c068-177">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c068-178">Vlastnost `<SelfContained>` je nastavena na hodnotu `true`:</span><span class="sxs-lookup"><span data-stu-id="0c068-178">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="0c068-179">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="0c068-179">Service user account</span></span>

<span data-ttu-id="0c068-180">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="0c068-180">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="0c068-181">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="0c068-181">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="0c068-182">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="0c068-182">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="0c068-183">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="0c068-183">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="0c068-184">Pokud rutina [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nezadá parametr `-AccountExpires` s vypršením <xref:System.DateTime>, platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="0c068-184">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="0c068-185">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="0c068-185">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="0c068-186">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="0c068-186">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="0c068-187">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="0c068-187">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="0c068-188">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="0c068-188">Log on as a service rights</span></span>

<span data-ttu-id="0c068-189">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="0c068-189">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="0c068-190">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="0c068-190">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="0c068-191">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="0c068-191">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="0c068-192">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="0c068-192">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="0c068-193">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="0c068-193">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="0c068-194">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="0c068-194">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="0c068-195">Zadejte uživatelský účet (`{DOMAIN OR COMPUTER NAME\USER}`) do pole název objektu a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="0c068-195">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="0c068-196">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="0c068-196">Select **Advanced**.</span></span> <span data-ttu-id="0c068-197">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="0c068-197">Select **Find Now**.</span></span> <span data-ttu-id="0c068-198">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="0c068-198">Select the user account from the list.</span></span> <span data-ttu-id="0c068-199">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="0c068-199">Select **OK**.</span></span> <span data-ttu-id="0c068-200">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="0c068-200">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="0c068-201">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="0c068-201">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="0c068-202">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="0c068-202">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="0c068-203">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="0c068-203">Create a service</span></span>

<span data-ttu-id="0c068-204">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="0c068-204">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="0c068-205">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="0c068-205">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="0c068-206">`{EXE PATH}` &ndash; cesta k složce aplikace na hostiteli (například `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="0c068-206">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="0c068-207">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c068-207">Don't include the app's executable in the path.</span></span> <span data-ttu-id="0c068-208">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="0c068-208">A trailing slash isn't required.</span></span>
* <span data-ttu-id="0c068-209">uživatelský účet služby `{DOMAIN OR COMPUTER NAME\USER}` &ndash; (například `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="0c068-209">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="0c068-210">`{NAME}` název služby &ndash; (například `MyService`).</span><span class="sxs-lookup"><span data-stu-id="0c068-210">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="0c068-211">`{EXE FILE PATH}` &ndash; cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="0c068-211">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="0c068-212">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="0c068-212">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="0c068-213">`{DESCRIPTION}` Popis služby &ndash; (například `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="0c068-213">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="0c068-214">`{DISPLAY NAME}` zobrazovaný název služby &ndash; (například `My Service`).</span><span class="sxs-lookup"><span data-stu-id="0c068-214">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="0c068-215">Spustit službu</span><span class="sxs-lookup"><span data-stu-id="0c068-215">Start a service</span></span>

<span data-ttu-id="0c068-216">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="0c068-216">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="0c068-217">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="0c068-217">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="0c068-218">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="0c068-218">Determine a service's status</span></span>

<span data-ttu-id="0c068-219">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="0c068-219">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="0c068-220">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="0c068-220">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="0c068-221">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="0c068-221">Stop a service</span></span>

<span data-ttu-id="0c068-222">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="0c068-222">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="0c068-223">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="0c068-223">Remove a service</span></span>

<span data-ttu-id="0c068-224">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="0c068-224">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="0c068-225">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="0c068-225">Handle starting and stopping events</span></span>

<span data-ttu-id="0c068-226">Zpracování událostí <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="0c068-226">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="0c068-227">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> pomocí metod `OnStarting`, `OnStarted` a `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="0c068-227">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="0c068-228">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost>, která předá `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="0c068-228">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="0c068-229">V `Program.Main` zavolejte metodu rozšíření `RunAsCustomService` namísto <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="0c068-229">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="0c068-230">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, přečtěte si ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="0c068-230">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0c068-231">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="0c068-231">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0c068-232">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="0c068-232">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="0c068-233">Další informace najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="0c068-233">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="0c068-234">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="0c068-234">Configure endpoints</span></span>

<span data-ttu-id="0c068-235">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="0c068-235">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="0c068-236">Nakonfigurujte adresu URL a port nastavením proměnné prostředí `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="0c068-236">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="0c068-237">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="0c068-237">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="0c068-238">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0c068-238">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="0c068-239">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0c068-239">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="0c068-240">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="0c068-240">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="0c068-241">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="0c068-241">Current directory and content root</span></span>

<span data-ttu-id="0c068-242">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C:\\Windows\\system32* .</span><span class="sxs-lookup"><span data-stu-id="0c068-242">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="0c068-243">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="0c068-243">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="0c068-244">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="0c068-244">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="0c068-245">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="0c068-245">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="0c068-246">Vyhledejte prostředky aplikace pomocí [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="0c068-246">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="0c068-247">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="0c068-247">Set the content root path to the app's folder</span></span>

<span data-ttu-id="0c068-248"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> je při vytvoření služby stejná cesta k argumentu `binPath`.</span><span class="sxs-lookup"><span data-stu-id="0c068-248">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="0c068-249">Namísto volání `GetCurrentDirectory` pro vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c068-249">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="0c068-250">V `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="0c068-250">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="0c068-251">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="0c068-251">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="0c068-252">Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.</span><span class="sxs-lookup"><span data-stu-id="0c068-252">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c068-253">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0c068-253">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="0c068-254">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="0c068-254">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="0c068-255">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="0c068-255">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
