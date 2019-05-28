---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/21/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: ab36bc1b2827c80bb1e7b9e8cee558b346a991f8
ms.sourcegitcommit: b8ed594ab9f47fa32510574f3e1b210cff000967
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251418"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="496ac-103">Hostitele ASP.NET Core ve službě Windows</span><span class="sxs-lookup"><span data-stu-id="496ac-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="496ac-104">Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="496ac-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="496ac-105">Na Windows, jako je možné hostovat aplikace ASP.NET Core [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="496ac-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="496ac-106">Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="496ac-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="496ac-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="496ac-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="496ac-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="496ac-108">Prerequisites</span></span>

* [<span data-ttu-id="496ac-109">ASP.NET Core SDK 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="496ac-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="496ac-110">Prostředí PowerShell 6.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="496ac-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="496ac-111">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="496ac-111">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="496ac-112">`IHostBuilder.UseWindowsService`, poskytuje [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) balíček, je volána, když vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="496ac-112">`IHostBuilder.UseWindowsService`, provided by the [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) package, is called when building the host.</span></span> <span data-ttu-id="496ac-113">Pokud aplikace běží jako služba Windows, metodu:</span><span class="sxs-lookup"><span data-stu-id="496ac-113">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="496ac-114">Nastaví životnost hostitele `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="496ac-114">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="496ac-115">Nastaví kořenové obsahu.</span><span class="sxs-lookup"><span data-stu-id="496ac-115">Sets the content root.</span></span>
* <span data-ttu-id="496ac-116">Povolí protokolování do protokolu událostí s názvem aplikace jako výchozí název zdroje.</span><span class="sxs-lookup"><span data-stu-id="496ac-116">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="496ac-117">Úroveň protokolu můžete konfigurovat pomocí `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.</span><span class="sxs-lookup"><span data-stu-id="496ac-117">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="496ac-118">Pouze správci mohou vytvářet nové zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="496ac-118">Only administrators can create new event sources.</span></span> <span data-ttu-id="496ac-119">Když zdroj událostí nelze vytvořit pomocí názvu aplikace, bude zaznamenána upozornění *aplikace* zdroje a protokoly událostí jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="496ac-119">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="496ac-120">Aplikace vyžaduje odkazy na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="496ac-120">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="496ac-121">K testování a ladění, když se provozují mimo službu, přidání kódu k určení, jestli aplikace běží jako službu nebo konzolové aplikace.</span><span class="sxs-lookup"><span data-stu-id="496ac-121">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="496ac-122">Kontrola, pokud je připojen ladicí program nebo `--console` přepínači je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="496ac-122">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="496ac-123">Pokud je některá podmínka pravdivá (aplikace není spuštěna jako služba), volání <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="496ac-123">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="496ac-124">Pokud jsou podmínky hodnotu false (aplikace je spuštěn jako služba):</span><span class="sxs-lookup"><span data-stu-id="496ac-124">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="496ac-125">Volání <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="496ac-125">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="496ac-126">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace Windows Service vrátí *C:\\WINDOWS\\system32* složky při <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="496ac-126">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="496ac-127">Další informace najdete v tématu [aktuálního adresáře a kořenový adresář obsahu](#current-directory-and-content-root) oddílu.</span><span class="sxs-lookup"><span data-stu-id="496ac-127">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="496ac-128">Tento krok se provádí před aplikace podle konfigurace v `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="496ac-128">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="496ac-129">Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> a spusťte tak aplikaci jako službu.</span><span class="sxs-lookup"><span data-stu-id="496ac-129">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="496ac-130">Protože [poskytovatele konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínače se odebere z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijímá argumenty.</span><span class="sxs-lookup"><span data-stu-id="496ac-130">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="496ac-131">Zapsat do protokolu událostí Windows, přidejte zprostředkovatele protokolu událostí na <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="496ac-131">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="496ac-132">Nastavení úrovně protokolování s `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.</span><span class="sxs-lookup"><span data-stu-id="496ac-132">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="496ac-133">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` se nazývá místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke zpracování událostí životního cyklu v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="496ac-133">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="496ac-134">Další informace najdete v tématu [zpracování, spouštění a zastavování události](#handle-starting-and-stopping-events) oddílu.</span><span class="sxs-lookup"><span data-stu-id="496ac-134">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="496ac-135">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="496ac-135">Deployment type</span></span>

<span data-ttu-id="496ac-136">Informace a Rady, scénáře nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="496ac-136">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="496ac-137">Nasazení závisí na architektuře (chyba)</span><span class="sxs-lookup"><span data-stu-id="496ac-137">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="496ac-138">Nasazení závisí na architektuře (chyba) spoléhá na přítomnost sdílené systémová verzi .NET Core v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="496ac-138">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="496ac-139">Přijatá chyba scénář je následující pokyny v tomto článku, sady SDK vytvoří spustitelný soubor ( *.exe*), označované jako *spustitelného souboru závisí na architektuře*.</span><span class="sxs-lookup"><span data-stu-id="496ac-139">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="496ac-140">Přidejte následující prvky vlastnosti do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="496ac-140">Add the following property elements to the project file:</span></span>

* <span data-ttu-id="496ac-141">`<OutputType>` &ndash; Typ výstupní aplikace (`Exe` pro spustitelný soubor).</span><span class="sxs-lookup"><span data-stu-id="496ac-141">`<OutputType>` &ndash; The app's output type (`Exe` for executable).</span></span>
* <span data-ttu-id="496ac-142">`<LangVersion>` &ndash; C# Jazykovou verzi (`latest` nebo `preview`).</span><span class="sxs-lookup"><span data-stu-id="496ac-142">`<LangVersion>` &ndash; The C# language version (`latest` or `preview`).</span></span>

<span data-ttu-id="496ac-143">A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows.</span><span class="sxs-lookup"><span data-stu-id="496ac-143">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="496ac-144">Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="496ac-144">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="496ac-145">Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="496ac-145">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="496ac-146">V následujícím příkladu RID nastavena `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="496ac-146">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="496ac-147">`<SelfContained>` Je nastavena na `false`.</span><span class="sxs-lookup"><span data-stu-id="496ac-147">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="496ac-148">Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru ( *.exe*) soubor pro Windows a aplikace, která závisí na sdílené .NET Core framework.</span><span class="sxs-lookup"><span data-stu-id="496ac-148">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="496ac-149">A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows.</span><span class="sxs-lookup"><span data-stu-id="496ac-149">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="496ac-150">Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="496ac-150">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="496ac-151">Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier >](/dotnet/core/tools/csproj#runtimeidentifier)) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="496ac-151">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="496ac-152">V následujícím příkladu RID nastavena `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="496ac-152">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="496ac-153">`<SelfContained>` Je nastavena na `false`.</span><span class="sxs-lookup"><span data-stu-id="496ac-153">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="496ac-154">Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru ( *.exe*) soubor pro Windows a aplikace, která závisí na sdílené .NET Core framework.</span><span class="sxs-lookup"><span data-stu-id="496ac-154">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="496ac-155">`<UseAppHost>` Je nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="496ac-155">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="496ac-156">Tato vlastnost poskytuje službu s cestou aktivace (spustitelný soubor, *.exe*) pro disketové jednotky.</span><span class="sxs-lookup"><span data-stu-id="496ac-156">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="496ac-157">A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows.</span><span class="sxs-lookup"><span data-stu-id="496ac-157">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="496ac-158">Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="496ac-158">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="496ac-159">Samostatná nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="496ac-159">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="496ac-160">Samostatná nasazení (SCD) nemusí spoléhat na přítomnost sdílené architektuře v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="496ac-160">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="496ac-161">Modul runtime a závislostí aplikace se nasadí s aplikací.</span><span class="sxs-lookup"><span data-stu-id="496ac-161">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="496ac-162">Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje Cílová architektura:</span><span class="sxs-lookup"><span data-stu-id="496ac-162">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="496ac-163">Chcete-li publikovat pro více identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="496ac-163">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="496ac-164">Zadejte identifikátory RID v seznam oddělený středníkem.</span><span class="sxs-lookup"><span data-stu-id="496ac-164">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="496ac-165">Použijte název vlastnosti [ \<RuntimeIdentifiers >](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="496ac-165">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="496ac-166">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="496ac-166">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="496ac-167">A `<SelfContained>` je nastavena na `true`:</span><span class="sxs-lookup"><span data-stu-id="496ac-167">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="496ac-168">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="496ac-168">Service user account</span></span>

<span data-ttu-id="496ac-169">Chcete-li vytvořit uživatelský účet pro službu, použijte [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutiny z správu příkazové okno Powershellu 6.</span><span class="sxs-lookup"><span data-stu-id="496ac-169">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="496ac-170">Ve Windows 10. října 2018 Update (verze 1809/sestavení 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="496ac-170">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="496ac-171">V operačním systému Windows starší než Windows 10. října 2018 Update (verze 1809/sestavení 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="496ac-171">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="496ac-172">Zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po zobrazení výzvy.</span><span class="sxs-lookup"><span data-stu-id="496ac-172">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="496ac-173">Pokud `-AccountExpires` parametr zadaný [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutinu s vypršení <xref:System.DateTime>, účet platnost pasu nevyprší.</span><span class="sxs-lookup"><span data-stu-id="496ac-173">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="496ac-174">Další informace najdete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a [uživatelské účty služby](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="496ac-174">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="496ac-175">Alternativní způsob správy uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="496ac-175">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="496ac-176">Další informace najdete v tématu [přehled účtů spravované služby skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="496ac-176">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="496ac-177">Přihlaste se jako práva služby</span><span class="sxs-lookup"><span data-stu-id="496ac-177">Log on as a service rights</span></span>

<span data-ttu-id="496ac-178">K navázání *přihlásit jako službu* práva pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="496ac-178">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="496ac-179">Otevřete editor místních zásad zabezpečení, že spustíte *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="496ac-179">Open the Local Security Policy editor by running *secpool.msc*.</span></span>
1. <span data-ttu-id="496ac-180">Rozbalte **místní zásady** uzel a vyberte možnost **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="496ac-180">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="496ac-181">Otevřít **přihlásit jako službu** zásad.</span><span class="sxs-lookup"><span data-stu-id="496ac-181">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="496ac-182">Vyberte **přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="496ac-182">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="496ac-183">Zadejte název objektu (uživatelský účet) některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="496ac-183">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="496ac-184">Zadejte uživatelský účet (`{DOMAIN OR COMPUTER NAME\USER}`) v objektu název pole a vyberte **OK** přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="496ac-184">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="496ac-185">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="496ac-185">Select **Advanced**.</span></span> <span data-ttu-id="496ac-186">Vyberte **najít**.</span><span class="sxs-lookup"><span data-stu-id="496ac-186">Select **Find Now**.</span></span> <span data-ttu-id="496ac-187">Vyberte uživatelský účet ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="496ac-187">Select the user account from the list.</span></span> <span data-ttu-id="496ac-188">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="496ac-188">Select **OK**.</span></span> <span data-ttu-id="496ac-189">Vyberte **OK** opakujte k přidání uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="496ac-189">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="496ac-190">Vyberte **OK** nebo **použít** změny uložte.</span><span class="sxs-lookup"><span data-stu-id="496ac-190">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="496ac-191">Vytvořit a spravovat službu Windows</span><span class="sxs-lookup"><span data-stu-id="496ac-191">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="496ac-192">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="496ac-192">Create a service</span></span>

<span data-ttu-id="496ac-193">Použijte příkazy prostředí PowerShell pro registraci služby.</span><span class="sxs-lookup"><span data-stu-id="496ac-193">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="496ac-194">Z pro správu příkazové okno Powershellu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="496ac-194">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="496ac-195">`{EXE PATH}` &ndash; Cesta ke složce aplikace na hostiteli (například `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="496ac-195">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="496ac-196">V cestě nezahrnují spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="496ac-196">Don't include the app's executable in the path.</span></span> <span data-ttu-id="496ac-197">Koncové lomítko není povinné.</span><span class="sxs-lookup"><span data-stu-id="496ac-197">A trailing slash isn't required.</span></span>
* <span data-ttu-id="496ac-198">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Uživatelský účet služby (například `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="496ac-198">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="496ac-199">`{NAME}` &ndash; Název služby (například `MyService`).</span><span class="sxs-lookup"><span data-stu-id="496ac-199">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="496ac-200">`{EXE FILE PATH}` &ndash; Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="496ac-200">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="496ac-201">Zahrnovat název souboru spustitelný soubor s příponou.</span><span class="sxs-lookup"><span data-stu-id="496ac-201">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="496ac-202">`{DESCRIPTION}` &ndash; Popis služby (například `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="496ac-202">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="496ac-203">`{DISPLAY NAME}` &ndash; Zobrazovaný název služby (například `My Service`).</span><span class="sxs-lookup"><span data-stu-id="496ac-203">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="496ac-204">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="496ac-204">Start a service</span></span>

<span data-ttu-id="496ac-205">Spuštění služby pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="496ac-205">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="496ac-206">Příkaz trvá několik sekund se spustit službu.</span><span class="sxs-lookup"><span data-stu-id="496ac-206">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="496ac-207">Zjistit stav služby</span><span class="sxs-lookup"><span data-stu-id="496ac-207">Determine a service's status</span></span>

<span data-ttu-id="496ac-208">Pokud chcete zkontrolovat stav služby, použijte následující příkaz Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="496ac-208">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="496ac-209">Stav je uveden jako jeden z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="496ac-209">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="496ac-210">Zastavit službu</span><span class="sxs-lookup"><span data-stu-id="496ac-210">Stop a service</span></span>

<span data-ttu-id="496ac-211">Zastavte službu pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="496ac-211">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="496ac-212">Odebrat službu</span><span class="sxs-lookup"><span data-stu-id="496ac-212">Remove a service</span></span>

<span data-ttu-id="496ac-213">Po krátké prodlevě zastavit službu odeberte službu pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="496ac-213">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="496ac-214">Zpracování spuštění a zastavení událostí</span><span class="sxs-lookup"><span data-stu-id="496ac-214">Handle starting and stopping events</span></span>

<span data-ttu-id="496ac-215">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události:</span><span class="sxs-lookup"><span data-stu-id="496ac-215">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="496ac-216">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting`, `OnStarted`, a `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="496ac-216">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="496ac-217">Vytvořit metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , který předá `CustomWebHostService` k <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="496ac-217">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="496ac-218">V `Program.Main`, zavolejte `RunAsCustomService` místo rozšiřující metoda <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="496ac-218">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="496ac-219">Pokud chcete zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, najdete vzorový kód uvedený v [typ nasazení](#deployment-type) oddílu.</span><span class="sxs-lookup"><span data-stu-id="496ac-219">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="496ac-220">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="496ac-220">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="496ac-221">Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="496ac-221">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="496ac-222">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="496ac-222">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="496ac-223">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="496ac-223">Configure HTTPS</span></span>

<span data-ttu-id="496ac-224">Nakonfigurujte službu s koncovým bodem zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="496ac-224">To configure a service with a secure endpoint:</span></span>

1. <span data-ttu-id="496ac-225">Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.</span><span class="sxs-lookup"><span data-stu-id="496ac-225">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="496ac-226">Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.</span><span class="sxs-lookup"><span data-stu-id="496ac-226">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="496ac-227">Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="496ac-227">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="496ac-228">Aktuální adresář a obsahu root</span><span class="sxs-lookup"><span data-stu-id="496ac-228">Current directory and content root</span></span>

<span data-ttu-id="496ac-229">Aktuální pracovní adresář vrátit voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu Windows je *C:\\WINDOWS\\system32* složky.</span><span class="sxs-lookup"><span data-stu-id="496ac-229">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="496ac-230">*System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="496ac-230">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="496ac-231">Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředků a souborů s nastavením služby.</span><span class="sxs-lookup"><span data-stu-id="496ac-231">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="496ac-232">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="496ac-232">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="496ac-233">Použití [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> při vyhledávání prostředků vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="496ac-233">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="496ac-234">Nastavení obsahu kořenovou cestu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="496ac-234">Set the content root path to the app's folder</span></span>

<span data-ttu-id="496ac-235"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Se stejnou cestu k dispozici na `binPath` argument při vytváření služby.</span><span class="sxs-lookup"><span data-stu-id="496ac-235">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="496ac-236">Namísto volání metody `GetCurrentDirectory` cest k souborům nastavení vytvoříte volání <xref:System.IO.Directory.SetCurrentDirectory*> cestu ke kořenové obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="496ac-236">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="496ac-237">V `Program.Main`, určit cestu ke složce spustitelný soubor služby a použijte cestu k vytvoření obsahu kořenové aplikace:</span><span class="sxs-lookup"><span data-stu-id="496ac-237">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="496ac-238">Store soubory služby na vhodné místo na disku</span><span class="sxs-lookup"><span data-stu-id="496ac-238">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="496ac-239">Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.</span><span class="sxs-lookup"><span data-stu-id="496ac-239">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="496ac-240">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="496ac-240">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="496ac-241">[Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="496ac-241">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="496ac-242">[Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="496ac-242">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
