---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/03/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 4cfca4b38543ff073bb98dc09b483d96096928ae
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692570"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="2fe37-103">Hostitele ASP.NET Core ve službě Windows</span><span class="sxs-lookup"><span data-stu-id="2fe37-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="2fe37-104">Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="2fe37-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="2fe37-105">Na Windows, jako je možné hostovat aplikace ASP.NET Core [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="2fe37-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="2fe37-106">Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="2fe37-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="2fe37-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fe37-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2fe37-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2fe37-108">Prerequisites</span></span>

* [<span data-ttu-id="2fe37-109">ASP.NET Core SDK 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2fe37-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="2fe37-110">Prostředí PowerShell 6.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2fe37-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="2fe37-111">Šablona služby pracovního procesu</span><span class="sxs-lookup"><span data-stu-id="2fe37-111">Worker Service template</span></span>

<span data-ttu-id="2fe37-112">Šablony ASP.NET Core pracovního procesu služby poskytuje výchozí bod pro psaní dlouho běžící aplikace služby.</span><span class="sxs-lookup"><span data-stu-id="2fe37-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="2fe37-113">Použití šablony jako základ pro aplikace Windows Service:</span><span class="sxs-lookup"><span data-stu-id="2fe37-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="2fe37-114">Vytvoření aplikace služby pracovního procesu v šabloně .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fe37-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="2fe37-115">Postupujte podle pokynů v [konfigurace aplikace](#app-configuration) část aktualizace aplikace služby pracovního procesu, tak, aby mohly běžet jako služba Windows.</span><span class="sxs-lookup"><span data-stu-id="2fe37-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2fe37-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe37-116">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2fe37-117">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="2fe37-117">Create a new project.</span></span>
1. <span data-ttu-id="2fe37-118">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2fe37-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-119">Select **Next**.</span></span>
1. <span data-ttu-id="2fe37-120">Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-120">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="2fe37-121">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-121">Select **Create**.</span></span>
1. <span data-ttu-id="2fe37-122">V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="2fe37-122">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="2fe37-123">Vyberte **služby pracovního procesu** šablony.</span><span class="sxs-lookup"><span data-stu-id="2fe37-123">Select the **Worker Service** template.</span></span> <span data-ttu-id="2fe37-124">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-124">Select **Create**.</span></span>

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="2fe37-125">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2fe37-125">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="2fe37-126">Použití služby pracovního procesu (`worker`) šablony s [dotnet nové](/dotnet/core/tools/dotnet-new) z příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="2fe37-126">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="2fe37-127">V následujícím příkladu se vytvoří aplikace služby pracovního procesu pojmenované `ContosoWorkerService`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-127">In the following example, a Worker Service app is created named `ContosoWorkerService`.</span></span> <span data-ttu-id="2fe37-128">Složka pro `ContosoWorkerService` aplikace se vytvoří automaticky při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-128">A folder for the `ContosoWorkerService` app is created automatically when the command is executed.</span></span>

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="2fe37-129">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="2fe37-129">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fe37-130">`IHostBuilder.UseWindowsService`, poskytuje [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) balíček, je volána, když vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe37-130">`IHostBuilder.UseWindowsService`, provided by the [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) package, is called when building the host.</span></span> <span data-ttu-id="2fe37-131">Pokud aplikace běží jako služba Windows, metodu:</span><span class="sxs-lookup"><span data-stu-id="2fe37-131">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="2fe37-132">Nastaví životnost hostitele `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-132">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="2fe37-133">Nastaví kořenové obsahu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-133">Sets the content root.</span></span>
* <span data-ttu-id="2fe37-134">Povolí protokolování do protokolu událostí s názvem aplikace jako výchozí název zdroje.</span><span class="sxs-lookup"><span data-stu-id="2fe37-134">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="2fe37-135">Úroveň protokolu můžete konfigurovat pomocí `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.</span><span class="sxs-lookup"><span data-stu-id="2fe37-135">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="2fe37-136">Pouze správci mohou vytvářet nové zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="2fe37-136">Only administrators can create new event sources.</span></span> <span data-ttu-id="2fe37-137">Když zdroj událostí nelze vytvořit pomocí názvu aplikace, bude zaznamenána upozornění *aplikace* zdroje a protokoly událostí jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="2fe37-137">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fe37-138">Aplikace vyžaduje odkazy na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="2fe37-138">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="2fe37-139">K testování a ladění, když se provozují mimo službu, přidání kódu k určení, jestli aplikace běží jako službu nebo konzolové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe37-139">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="2fe37-140">Kontrola, pokud je připojen ladicí program nebo `--console` přepínači je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2fe37-140">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="2fe37-141">Pokud je některá podmínka pravdivá (aplikace není spuštěna jako služba), volání <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="2fe37-141">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="2fe37-142">Pokud jsou podmínky hodnotu false (aplikace je spuštěn jako služba):</span><span class="sxs-lookup"><span data-stu-id="2fe37-142">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="2fe37-143">Volání <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe37-143">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="2fe37-144">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace Windows Service vrátí *C:\\WINDOWS\\system32* složky při <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="2fe37-144">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="2fe37-145">Další informace najdete v tématu [aktuálního adresáře a kořenový adresář obsahu](#current-directory-and-content-root) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-145">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="2fe37-146">Tento krok se provádí před aplikace podle konfigurace v `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-146">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="2fe37-147">Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> a spusťte tak aplikaci jako službu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-147">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="2fe37-148">Protože [poskytovatele konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínače se odebere z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijímá argumenty.</span><span class="sxs-lookup"><span data-stu-id="2fe37-148">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="2fe37-149">Zapsat do protokolu událostí Windows, přidejte zprostředkovatele protokolu událostí na <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="2fe37-149">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="2fe37-150">Nastavení úrovně protokolování s `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.</span><span class="sxs-lookup"><span data-stu-id="2fe37-150">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="2fe37-151">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` se nazývá místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke zpracování událostí životního cyklu v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe37-151">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="2fe37-152">Další informace najdete v tématu [zpracování, spouštění a zastavování události](#handle-starting-and-stopping-events) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-152">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="2fe37-153">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="2fe37-153">Deployment type</span></span>

<span data-ttu-id="2fe37-154">Informace a Rady, scénáře nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="2fe37-154">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="2fe37-155">Nasazení závisí na architektuře (chyba)</span><span class="sxs-lookup"><span data-stu-id="2fe37-155">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="2fe37-156">Nasazení závisí na architektuře (chyba) spoléhá na přítomnost sdílené systémová verzi .NET Core v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="2fe37-156">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="2fe37-157">Přijatá chyba scénář je následující pokyny v tomto článku, sady SDK vytvoří spustitelný soubor ( *.exe*), označované jako *spustitelného souboru závisí na architektuře*.</span><span class="sxs-lookup"><span data-stu-id="2fe37-157">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fe37-158">Přidejte následující prvky vlastnosti do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="2fe37-158">Add the following property elements to the project file:</span></span>

* <span data-ttu-id="2fe37-159">`<OutputType>` &ndash; Typ výstupní aplikace (`Exe` pro spustitelný soubor).</span><span class="sxs-lookup"><span data-stu-id="2fe37-159">`<OutputType>` &ndash; The app's output type (`Exe` for executable).</span></span>
* <span data-ttu-id="2fe37-160">`<LangVersion>` &ndash; C# Jazykovou verzi (`latest` nebo `preview`).</span><span class="sxs-lookup"><span data-stu-id="2fe37-160">`<LangVersion>` &ndash; The C# language version (`latest` or `preview`).</span></span>

<span data-ttu-id="2fe37-161">A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows.</span><span class="sxs-lookup"><span data-stu-id="2fe37-161">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="2fe37-162">Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-162">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="2fe37-163">Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-163">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="2fe37-164">V následujícím příkladu RID nastavena `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-164">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="2fe37-165">`<SelfContained>` Je nastavena na `false`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-165">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="2fe37-166">Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru ( *.exe*) soubor pro Windows a aplikace, která závisí na sdílené .NET Core framework.</span><span class="sxs-lookup"><span data-stu-id="2fe37-166">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="2fe37-167">A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows.</span><span class="sxs-lookup"><span data-stu-id="2fe37-167">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="2fe37-168">Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-168">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="2fe37-169">Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-169">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="2fe37-170">V následujícím příkladu RID nastavena `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-170">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="2fe37-171">`<SelfContained>` Je nastavena na `false`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-171">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="2fe37-172">Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru ( *.exe*) soubor pro Windows a aplikace, která závisí na sdílené .NET Core framework.</span><span class="sxs-lookup"><span data-stu-id="2fe37-172">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="2fe37-173">`<UseAppHost>` Je nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-173">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="2fe37-174">Tato vlastnost poskytuje službu s cestou aktivace (spustitelný soubor, *.exe*) pro disketové jednotky.</span><span class="sxs-lookup"><span data-stu-id="2fe37-174">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="2fe37-175">A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows.</span><span class="sxs-lookup"><span data-stu-id="2fe37-175">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="2fe37-176">Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="2fe37-176">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="2fe37-177">Samostatná nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="2fe37-177">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="2fe37-178">Samostatná nasazení (SCD) nemusí spoléhat na přítomnost sdílené architektuře v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="2fe37-178">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="2fe37-179">Modul runtime a závislostí aplikace se nasadí s aplikací.</span><span class="sxs-lookup"><span data-stu-id="2fe37-179">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="2fe37-180">Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje Cílová architektura:</span><span class="sxs-lookup"><span data-stu-id="2fe37-180">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="2fe37-181">Chcete-li publikovat pro více identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="2fe37-181">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="2fe37-182">Zadejte identifikátory RID v seznam oddělený středníkem.</span><span class="sxs-lookup"><span data-stu-id="2fe37-182">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="2fe37-183">Použijte název vlastnosti [ \<RuntimeIdentifiers >](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="2fe37-183">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="2fe37-184">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="2fe37-184">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fe37-185">A `<SelfContained>` je nastavena na `true`:</span><span class="sxs-lookup"><span data-stu-id="2fe37-185">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="2fe37-186">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="2fe37-186">Service user account</span></span>

<span data-ttu-id="2fe37-187">Chcete-li vytvořit uživatelský účet pro službu, použijte [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutiny z správu příkazové okno Powershellu 6.</span><span class="sxs-lookup"><span data-stu-id="2fe37-187">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="2fe37-188">Ve Windows 10. října 2018 Update (verze 1809/sestavení 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="2fe37-188">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="2fe37-189">V operačním systému Windows starší než Windows 10. října 2018 Update (verze 1809/sestavení 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="2fe37-189">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="2fe37-190">Zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po zobrazení výzvy.</span><span class="sxs-lookup"><span data-stu-id="2fe37-190">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="2fe37-191">Pokud `-AccountExpires` parametr zadaný [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutinu s vypršení <xref:System.DateTime>, účet platnost pasu nevyprší.</span><span class="sxs-lookup"><span data-stu-id="2fe37-191">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="2fe37-192">Další informace najdete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a [uživatelské účty služby](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="2fe37-192">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="2fe37-193">Alternativní způsob správy uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="2fe37-193">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="2fe37-194">Další informace najdete v tématu [přehled účtů spravované služby skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="2fe37-194">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="2fe37-195">Přihlaste se jako práva služby</span><span class="sxs-lookup"><span data-stu-id="2fe37-195">Log on as a service rights</span></span>

<span data-ttu-id="2fe37-196">K navázání *přihlásit jako službu* práva pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="2fe37-196">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="2fe37-197">Otevřete editor místních zásad zabezpečení, že spustíte *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="2fe37-197">Open the Local Security Policy editor by running *secpool.msc*.</span></span>
1. <span data-ttu-id="2fe37-198">Rozbalte **místní zásady** uzel a vyberte možnost **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-198">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="2fe37-199">Otevřít **přihlásit jako službu** zásad.</span><span class="sxs-lookup"><span data-stu-id="2fe37-199">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="2fe37-200">Vyberte **přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-200">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="2fe37-201">Zadejte název objektu (uživatelský účet) některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="2fe37-201">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="2fe37-202">Zadejte uživatelský účet (`{DOMAIN OR COMPUTER NAME\USER}`) v objektu název pole a vyberte **OK** přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="2fe37-202">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="2fe37-203">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-203">Select **Advanced**.</span></span> <span data-ttu-id="2fe37-204">Vyberte **najít**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-204">Select **Find Now**.</span></span> <span data-ttu-id="2fe37-205">Vyberte uživatelský účet ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-205">Select the user account from the list.</span></span> <span data-ttu-id="2fe37-206">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="2fe37-206">Select **OK**.</span></span> <span data-ttu-id="2fe37-207">Vyberte **OK** opakujte k přidání uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="2fe37-207">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="2fe37-208">Vyberte **OK** nebo **použít** změny uložte.</span><span class="sxs-lookup"><span data-stu-id="2fe37-208">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="2fe37-209">Vytvořit a spravovat službu Windows</span><span class="sxs-lookup"><span data-stu-id="2fe37-209">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="2fe37-210">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="2fe37-210">Create a service</span></span>

<span data-ttu-id="2fe37-211">Použijte příkazy prostředí PowerShell pro registraci služby.</span><span class="sxs-lookup"><span data-stu-id="2fe37-211">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="2fe37-212">Z pro správu příkazové okno Powershellu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2fe37-212">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="2fe37-213">`{EXE PATH}` &ndash; Cesta ke složce aplikace na hostiteli (například `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="2fe37-213">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="2fe37-214">V cestě nezahrnují spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe37-214">Don't include the app's executable in the path.</span></span> <span data-ttu-id="2fe37-215">Koncové lomítko není povinné.</span><span class="sxs-lookup"><span data-stu-id="2fe37-215">A trailing slash isn't required.</span></span>
* <span data-ttu-id="2fe37-216">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Uživatelský účet služby (například `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="2fe37-216">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="2fe37-217">`{NAME}` &ndash; Název služby (například `MyService`).</span><span class="sxs-lookup"><span data-stu-id="2fe37-217">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="2fe37-218">`{EXE FILE PATH}` &ndash; Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="2fe37-218">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="2fe37-219">Zahrnovat název souboru spustitelný soubor s příponou.</span><span class="sxs-lookup"><span data-stu-id="2fe37-219">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="2fe37-220">`{DESCRIPTION}` &ndash; Popis služby (například `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="2fe37-220">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="2fe37-221">`{DISPLAY NAME}` &ndash; Zobrazovaný název služby (například `My Service`).</span><span class="sxs-lookup"><span data-stu-id="2fe37-221">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="2fe37-222">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="2fe37-222">Start a service</span></span>

<span data-ttu-id="2fe37-223">Spuštění služby pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="2fe37-223">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="2fe37-224">Příkaz trvá několik sekund se spustit službu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-224">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="2fe37-225">Zjistit stav služby</span><span class="sxs-lookup"><span data-stu-id="2fe37-225">Determine a service's status</span></span>

<span data-ttu-id="2fe37-226">Pokud chcete zkontrolovat stav služby, použijte následující příkaz Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="2fe37-226">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="2fe37-227">Stav je uveden jako jeden z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="2fe37-227">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="2fe37-228">Zastavit službu</span><span class="sxs-lookup"><span data-stu-id="2fe37-228">Stop a service</span></span>

<span data-ttu-id="2fe37-229">Zastavte službu pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="2fe37-229">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="2fe37-230">Odebrat službu</span><span class="sxs-lookup"><span data-stu-id="2fe37-230">Remove a service</span></span>

<span data-ttu-id="2fe37-231">Po krátké prodlevě zastavit službu odeberte službu pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="2fe37-231">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="2fe37-232">Zpracování spuštění a zastavení událostí</span><span class="sxs-lookup"><span data-stu-id="2fe37-232">Handle starting and stopping events</span></span>

<span data-ttu-id="2fe37-233">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události:</span><span class="sxs-lookup"><span data-stu-id="2fe37-233">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="2fe37-234">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting`, `OnStarted`, a `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="2fe37-234">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="2fe37-235">Vytvořit metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , který předá `CustomWebHostService` k <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="2fe37-235">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="2fe37-236">V `Program.Main`, zavolejte `RunAsCustomService` místo rozšiřující metoda <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="2fe37-236">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="2fe37-237">Pokud chcete zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, najdete vzorový kód uvedený v [typ nasazení](#deployment-type) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2fe37-237">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2fe37-238">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="2fe37-238">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2fe37-239">Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="2fe37-239">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="2fe37-240">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="2fe37-240">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="2fe37-241">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="2fe37-241">Configure HTTPS</span></span>

<span data-ttu-id="2fe37-242">Nakonfigurujte službu s koncovým bodem zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="2fe37-242">To configure a service with a secure endpoint:</span></span>

1. <span data-ttu-id="2fe37-243">Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.</span><span class="sxs-lookup"><span data-stu-id="2fe37-243">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="2fe37-244">Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.</span><span class="sxs-lookup"><span data-stu-id="2fe37-244">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="2fe37-245">Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="2fe37-245">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="2fe37-246">Aktuální adresář a obsahu root</span><span class="sxs-lookup"><span data-stu-id="2fe37-246">Current directory and content root</span></span>

<span data-ttu-id="2fe37-247">Aktuální pracovní adresář vrátit voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu Windows je *C:\\WINDOWS\\system32* složky.</span><span class="sxs-lookup"><span data-stu-id="2fe37-247">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="2fe37-248">*System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="2fe37-248">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="2fe37-249">Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředků a souborů s nastavením služby.</span><span class="sxs-lookup"><span data-stu-id="2fe37-249">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="2fe37-250">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="2fe37-250">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="2fe37-251">Použití [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> při vyhledávání prostředků vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe37-251">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="2fe37-252">Nastavení obsahu kořenovou cestu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="2fe37-252">Set the content root path to the app's folder</span></span>

<span data-ttu-id="2fe37-253"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Se stejnou cestu k dispozici na `binPath` argument při vytváření služby.</span><span class="sxs-lookup"><span data-stu-id="2fe37-253">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="2fe37-254">Namísto volání metody `GetCurrentDirectory` cest k souborům nastavení vytvoříte volání <xref:System.IO.Directory.SetCurrentDirectory*> cestu ke kořenové obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe37-254">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="2fe37-255">V `Program.Main`, určit cestu ke složce spustitelný soubor služby a použijte cestu k vytvoření obsahu kořenové aplikace:</span><span class="sxs-lookup"><span data-stu-id="2fe37-255">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="2fe37-256">Store soubory služby na vhodné místo na disku</span><span class="sxs-lookup"><span data-stu-id="2fe37-256">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="2fe37-257">Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.</span><span class="sxs-lookup"><span data-stu-id="2fe37-257">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fe37-258">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2fe37-258">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="2fe37-259">[Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="2fe37-259">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="2fe37-260">[Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="2fe37-260">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
