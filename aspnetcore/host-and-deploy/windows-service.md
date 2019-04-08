---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/04/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 544eefa87898e82ec2bf8f9f61ce4e26dd554bb7
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068333"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="1b8e9-103">Hostitele ASP.NET Core ve službě Windows</span><span class="sxs-lookup"><span data-stu-id="1b8e9-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="1b8e9-104">Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="1b8e9-105">Na Windows, jako je možné hostovat aplikace ASP.NET Core [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1b8e9-106">Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování počítače.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="1b8e9-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b8e9-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1b8e9-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1b8e9-108">Prerequisites</span></span>

* [<span data-ttu-id="1b8e9-109">Prostředí PowerShell 6.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1b8e9-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

> [!NOTE]
> <span data-ttu-id="1b8e9-110">Pro operační systém Windows starších než Windows 10. října 2018 Update (verze 1809/sestavení 10.0.17763), [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts) modulu musí být importovány pomocí [WindowsCompatibility modulu](https://github.com/PowerShell/WindowsCompatibility)k získání přístupu k [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutiny používané [vytvoření uživatelského účtu](#create-a-user-account) části:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-110">For Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763), the [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts) module must be imported with the [WindowsCompatibility module](https://github.com/PowerShell/WindowsCompatibility) to gain access to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet used in the [Create a user account](#create-a-user-account) section:</span></span>
>
> ```powershell
> Install-Module WindowsCompatibility -Scope CurrentUser
> Import-WinModule Microsoft.PowerShell.LocalAccounts
> ```

## <a name="deployment-type"></a><span data-ttu-id="1b8e9-111">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="1b8e9-111">Deployment type</span></span>

<span data-ttu-id="1b8e9-112">Můžete vytvořit buď závisí na architektuře nebo samostatná Windows nasazení služby.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-112">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="1b8e9-113">Informace a Rady, scénáře nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-113">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="1b8e9-114">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="1b8e9-114">Framework-dependent deployment</span></span>

<span data-ttu-id="1b8e9-115">Nasazení závisí na architektuře (chyba) spoléhá na přítomnost sdílené systémová verzi .NET Core v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-115">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1b8e9-116">Při použití scénář disketové jednotky s aplikací ASP.NET Core Windows Service SDK vytvoří spustitelný soubor (*\*.exe*), označované jako *spustitelného souboru závisí na architektuře*.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-116">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="1b8e9-117">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="1b8e9-117">Self-contained deployment</span></span>

<span data-ttu-id="1b8e9-118">Samostatná nasazení (SCD) nemusí spoléhat na přítomnost sdílené komponenty v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-118">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="1b8e9-119">Modul runtime a závislostí aplikace jsou nasazené v aplikaci k hostování systému.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-119">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="1b8e9-120">Převést projekt do služby Windows</span><span class="sxs-lookup"><span data-stu-id="1b8e9-120">Convert a project into a Windows Service</span></span>

<span data-ttu-id="1b8e9-121">Do existujícího projektu ASP.NET Core a spusťte tak aplikaci jako službu, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-121">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="1b8e9-122">Aktualizace souboru projektu</span><span class="sxs-lookup"><span data-stu-id="1b8e9-122">Project file updates</span></span>

<span data-ttu-id="1b8e9-123">Podle podle vaší volby [typ nasazení](#deployment-type), aktualizujte soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-123">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1b8e9-124">Nasazení závisí na architektuře (chyba)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-124">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="1b8e9-125">Přidat Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) k `<PropertyGroup>` , která obsahuje cílové rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-125">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="1b8e9-126">V následujícím příkladu RID nastavena `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-126">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1b8e9-127">Přidat `<SelfContained>` nastavenou na `false`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-127">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="1b8e9-128">Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru (*.exe*) souborů pro Windows.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-128">These properties instruct the SDK to generate an executable (*.exe*) file for Windows.</span></span>

<span data-ttu-id="1b8e9-129">A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-129">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1b8e9-130">Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-130">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

::: moniker range=">= aspnetcore-2.2"

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

<span data-ttu-id="1b8e9-131">Přidat `<UseAppHost>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-131">Add the `<UseAppHost>` property set to `true`.</span></span> <span data-ttu-id="1b8e9-132">Tato vlastnost poskytuje službu s cestou aktivace (spustitelný soubor, *.exe*) pro disketové jednotky.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-132">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1b8e9-133">Samostatná nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-133">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="1b8e9-134">Ověřte existenci Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) nebo přidání identifikátorů RID pro `<PropertyGroup>` , která obsahuje cílové rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-134">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="1b8e9-135">Zakázat vytváření *web.config* souboru tak, že přidáte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-135">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="1b8e9-136">Chcete-li publikovat pro více identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-136">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1b8e9-137">Zadejte identifikátory RID v seznam oddělený středníkem.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-137">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1b8e9-138">Použijte název vlastnosti `<RuntimeIdentifiers>` (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-138">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="1b8e9-139">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-139">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="1b8e9-140">Přidat odkaz na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-140">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="1b8e9-141">Pokud chcete povolit protokolování protokolu událostí Windows, přidejte odkaz na balíček pro [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-141">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="1b8e9-142">Další informace najdete v tématu [zpracování, spouštění a zastavování události](#handle-starting-and-stopping-events) oddílu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-142">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="1b8e9-143">Aktualizace Program.Main</span><span class="sxs-lookup"><span data-stu-id="1b8e9-143">Program.Main updates</span></span>

<span data-ttu-id="1b8e9-144">Proveďte následující změny v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-144">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="1b8e9-145">K testování a ladění, když se provozují mimo službu, přidání kódu k určení, jestli aplikace běží jako službu nebo konzolové aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-145">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="1b8e9-146">Kontrola, pokud je připojen ladicí program nebo `--console` argument příkazového řádku je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-146">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="1b8e9-147">Pokud je některá podmínka pravdivá (aplikace není spuštěna jako služba), volání <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> na webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-147">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="1b8e9-148">Pokud jsou podmínky hodnotu false (aplikace je spuštěn jako služba):</span><span class="sxs-lookup"><span data-stu-id="1b8e9-148">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="1b8e9-149">Volání <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-149">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="1b8e9-150">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace Windows Service vrátí *C:\\WINDOWS\\system32* složky při <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-150">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="1b8e9-151">Další informace najdete v tématu [aktuálního adresáře a kořenový adresář obsahu](#current-directory-and-content-root) oddílu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-151">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="1b8e9-152">Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> a spusťte tak aplikaci jako službu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-152">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="1b8e9-153">Protože [poskytovatele konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínače se odebere z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> je obdrží.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-153">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="1b8e9-154">Zapsat do protokolu událostí Windows, přidejte zprostředkovatele protokolu událostí na <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-154">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="1b8e9-155">Nastavení úrovně protokolování s `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-155">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="1b8e9-156">Soubor nastavení produkčním prostředí ukázkovou aplikaci pro demonstrační účely a testování, nastaví úroveň protokolování na `Information`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-156">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="1b8e9-157">V produkčním prostředí, hodnota se obvykle nastavuje na `Error`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-157">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="1b8e9-158">Další informace naleznete v tématu <xref:fundamentals/logging/index#windows-eventlog-provider>.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-158">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="publish-the-app"></a><span data-ttu-id="1b8e9-159">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="1b8e9-159">Publish the app</span></span>

<span data-ttu-id="1b8e9-160">Publikování aplikace pomocí [dotnet publikovat](/dotnet/articles/core/tools/dotnet-publish), [profil publikování pro Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles), nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-160">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="1b8e9-161">Když pomocí sady Visual Studio, vyberte **FolderProfile** a nakonfigurovat **cílové umístění** před výběrem **publikovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-161">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="1b8e9-162">Publikování ukázkové aplikace pomocí nástrojů rozhraní příkazového řádku (CLI), spusťte [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu v příkazovém řádku ve složce projektu s předat konfiguraci vydané verze Windows [- c |--konfigurace ](/dotnet/core/tools/dotnet-publish#options) možnost.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-162">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command in a Windows command shell from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="1b8e9-163">Použití [-o |--výstup](/dotnet/core/tools/dotnet-publish#options) možnost s cestou k publikování do složky mimo aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-163">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="1b8e9-164">Publikování nasazení závisí na architektuře (chyba)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-164">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="1b8e9-165">V následujícím příkladu je aplikace publikována na *c:\\svc* složky:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-165">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="1b8e9-166">Publikování samostatná nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-166">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="1b8e9-167">V musí být zadán identifikátor RID `<RuntimeIdenfifier>` (nebo `<RuntimeIdentifiers>`) vlastnost souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-167">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="1b8e9-168">Zadejte modul runtime [- r |--runtime](/dotnet/core/tools/dotnet-publish#options) možnost `dotnet publish` příkazu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-168">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="1b8e9-169">V následujícím příkladu je aplikace publikována pro `win7-x64` modulu runtime *c:\\svc* složky:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-169">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

## <a name="create-a-user-account"></a><span data-ttu-id="1b8e9-170">Vytvoření uživatelského účtu</span><span class="sxs-lookup"><span data-stu-id="1b8e9-170">Create a user account</span></span>

<span data-ttu-id="1b8e9-171">Vytvořte účet uživatele pro používání služby [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutiny z správu příkazové okno prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-171">Create a user account for the service using the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell:</span></span>

```powershell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="1b8e9-172">Zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po zobrazení výzvy.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-172">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1b8e9-173">Pro ukázkovou aplikaci, vytvořte uživatelský účet s názvem `ServiceUser`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-173">For the sample app, create a user account with the name `ServiceUser`.</span></span>

```powershell
New-LocalUser -Name ServiceUser
```

<span data-ttu-id="1b8e9-174">Pokud `-AccountExpires` parametr zadaný [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) rutinu s vypršení <xref:System.DateTime>, účet platnost pasu nevyprší.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-174">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1b8e9-175">Další informace najdete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a [uživatelské účty služby](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-175">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1b8e9-176">Alternativní způsob správy uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-176">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1b8e9-177">Další informace najdete v tématu [přehled účtů spravované služby skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-177">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="set-permission-log-on-as-a-service"></a><span data-ttu-id="1b8e9-178">Nastavte oprávnění: Přihlaste se jako služba</span><span class="sxs-lookup"><span data-stu-id="1b8e9-178">Set permission: Log on as a service</span></span>

<span data-ttu-id="1b8e9-179">Udělit přístup, zápis a čtení a spouštění do složky aplikace pomocí [icacls](/windows-server/administration/windows-commands/icacls) příkazů pro správu příkazové okno Powershellu 6.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-179">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command an administrative PowerShell 6 command shell.</span></span>

```powershell
icacls "{PATH}" /grant "{USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS}" /t
```

* `{PATH}` <span data-ttu-id="1b8e9-180">&ndash; Cesta ke složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-180">&ndash; Path to the app's folder.</span></span>
* `{USER ACCOUNT}` <span data-ttu-id="1b8e9-181">&ndash; Uživatelský účet (SID).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-181">&ndash; The user account (SID).</span></span>
* `(OI)` <span data-ttu-id="1b8e9-182">&ndash; Příznak objekt dědit šíří oprávnění na podřízené soubory.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-182">&ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* `(CI)` <span data-ttu-id="1b8e9-183">&ndash; Příznak kontejneru dědit šíří oprávnění na podřízené složky.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-183">&ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* `{PERMISSION FLAGS}` <span data-ttu-id="1b8e9-184">&ndash; Nastaví její oprávnění přístupu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-184">&ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="1b8e9-185">Zápis (`W`)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-185">Write (`W`)</span></span>
  * <span data-ttu-id="1b8e9-186">Čtení (`R`)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-186">Read (`R`)</span></span>
  * <span data-ttu-id="1b8e9-187">Spuštění (`X`)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-187">Execute (`X`)</span></span>
  * <span data-ttu-id="1b8e9-188">Úplné (`F`)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-188">Full (`F`)</span></span>
  * <span data-ttu-id="1b8e9-189">Upravit (`M`)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-189">Modify (`M`)</span></span>
* `/t` <span data-ttu-id="1b8e9-190">&ndash; Rekurzivně se vztahují na existující podřízené složky a soubory.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-190">&ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="1b8e9-191">Pro publikování ukázkové aplikace *c:\\svc* složky a `ServiceUser` účet s oprávněními pro zápis a čtení a spouštění, použijte následující příkaz pro správu příkazové okno Powershellu 6.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-191">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command an administrative PowerShell 6 command shell.</span></span>

```powershell
icacls "c:\svc" /grant "ServiceUser:(OI)(CI)WRX" /t
```

<span data-ttu-id="1b8e9-192">Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-192">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

## <a name="create-the-service"></a><span data-ttu-id="1b8e9-193">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="1b8e9-193">Create the service</span></span>

<span data-ttu-id="1b8e9-194">Použití [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) skript Powershellu pro registraci služby.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-194">Use the [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) PowerShell script to register the service.</span></span> <span data-ttu-id="1b8e9-195">Z pro správu příkazové okno Powershellu 6 spusťte tento skript pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-195">From an administrative PowerShell 6 command shell, execute the script with the following command:</span></span>

```powershell
.\RegisterService.ps1 
    -Name {NAME} 
    -DisplayName "{DISPLAY NAME}" 
    -Description "{DESCRIPTION}" 
    -Exe "{PATH TO EXE}\{ASSEMBLY NAME}.exe" 
    -User {DOMAIN\USER}
```

<span data-ttu-id="1b8e9-196">V následujícím příkladu pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-196">In the following example for the sample app:</span></span>

* <span data-ttu-id="1b8e9-197">Služba má název **Moje_služba**.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-197">The service is named **MyService**.</span></span>
* <span data-ttu-id="1b8e9-198">Publikované služba se nachází v *c:\\svc* složky.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-198">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="1b8e9-199">Je název spustitelné aplikace *SampleApp.exe*.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-199">The app executable is named *SampleApp.exe*.</span></span>
* <span data-ttu-id="1b8e9-200">Je služba spuštěna pod `ServiceUser` účtu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-200">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="1b8e9-201">V následujícím ukázkovém příkazu, je název místního počítače `Desktop-PC`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-201">In the following example command, the local machine name is `Desktop-PC`.</span></span> <span data-ttu-id="1b8e9-202">Nahraďte `Desktop-PC` pomocí názvu počítače nebo domény pro váš systém.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-202">Replace `Desktop-PC` with the computer name or domain for your system.</span></span>

```powershell
.\RegisterService.ps1 
    -Name MyService 
    -DisplayName "My Cool Service" 
    -Description "This is the Sample App service." 
    -Exe "c:\svc\SampleApp.exe" 
    -User Desktop-PC\ServiceUser
```

## <a name="manage-the-service"></a><span data-ttu-id="1b8e9-203">Spravovat službu</span><span class="sxs-lookup"><span data-stu-id="1b8e9-203">Manage the service</span></span>

### <a name="start-the-service"></a><span data-ttu-id="1b8e9-204">Spustit službu</span><span class="sxs-lookup"><span data-stu-id="1b8e9-204">Start the service</span></span>

<span data-ttu-id="1b8e9-205">Spusťte službu pomocí `Start-Service -Name {NAME}` příkazu Powershellu 6.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-205">Start the service with the `Start-Service -Name {NAME}` PowerShell 6 command.</span></span>

<span data-ttu-id="1b8e9-206">Spustit službu ukázkové aplikace, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-206">To start the sample app service, use the following command:</span></span>

```powershell
Start-Service -Name MyService
```

<span data-ttu-id="1b8e9-207">Příkaz trvá několik sekund se spustit službu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-207">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="1b8e9-208">Zjistit stav služby</span><span class="sxs-lookup"><span data-stu-id="1b8e9-208">Determine the service status</span></span>

<span data-ttu-id="1b8e9-209">Chcete-li zkontrolovat stav služby, použijte `Get-Service -Name {NAME}` příkazu Powershellu 6.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-209">To check the status of the service, use the `Get-Service -Name {NAME}` PowerShell 6 command.</span></span> <span data-ttu-id="1b8e9-210">Stav je uveden jako jeden z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-210">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

<span data-ttu-id="1b8e9-211">Použijte následující příkaz a zkontrolujte stav služby app service vzorku:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-211">Use the following command to check the status of the sample app service:</span></span>

```powershell
Get-Service -Name MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="1b8e9-212">Procházet služba webové aplikace</span><span class="sxs-lookup"><span data-stu-id="1b8e9-212">Browse a web app service</span></span>

<span data-ttu-id="1b8e9-213">Pokud je služba v `RUNNING` stavu a pokud je služba webové aplikace, procházet aplikace, její cesta (ve výchozím nastavení, `http://localhost:5000`, který přesměruje `https://localhost:5001` při použití [HTTPS přesměrování Middleware](xref:security/enforcing-ssl)).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-213">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="1b8e9-214">Aplikační služba ukázkového procházet aplikace na adrese `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-214">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="1b8e9-215">Zastavit službu</span><span class="sxs-lookup"><span data-stu-id="1b8e9-215">Stop the service</span></span>

<span data-ttu-id="1b8e9-216">Zastavit službu s `Stop-Service -Name {NAME}` příkazu Powershellu 6.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-216">Stop the service with the `Stop-Service -Name {NAME}` Powershell 6 command.</span></span>

<span data-ttu-id="1b8e9-217">Následující příkaz zastaví aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-217">The following command stops the sample app service:</span></span>

```powershell
Stop-Service -Name MyService
```

### <a name="remove-the-service"></a><span data-ttu-id="1b8e9-218">Odebrat službu</span><span class="sxs-lookup"><span data-stu-id="1b8e9-218">Remove the service</span></span>

<span data-ttu-id="1b8e9-219">Po krátké prodlevě zastavit službu, službu s odebrat `Remove-Service -Name {NAME}` příkazu Powershellu 6.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-219">After a short delay to stop a service, remove the service with the `Remove-Service -Name {NAME}` Powershell 6 command.</span></span>

<span data-ttu-id="1b8e9-220">Postup kontroly stavu aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-220">Check the status of the sample app service:</span></span>

```powershell
Remove-Service -Name MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="1b8e9-221">Zpracování spuštění a zastavení událostí</span><span class="sxs-lookup"><span data-stu-id="1b8e9-221">Handle starting and stopping events</span></span>

<span data-ttu-id="1b8e9-222">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události, proveďte následující další změny:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-222">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="1b8e9-223">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting`, `OnStarted`, a `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-223">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="1b8e9-224">Vytvořit metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , který předá `CustomWebHostService` k <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-224">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="1b8e9-225">V `Program.Main`, zavolejte `RunAsCustomService` místo rozšiřující metoda <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-225">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="1b8e9-226">Pokud chcete zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, najdete vzorový kód uvedený v [převést projekt do služby Windows](#convert-a-project-into-a-windows-service) oddílu.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-226">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1b8e9-227">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="1b8e9-227">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1b8e9-228">Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-228">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1b8e9-229">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-229">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="1b8e9-230">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="1b8e9-230">Configure HTTPS</span></span>

<span data-ttu-id="1b8e9-231">Jak nakonfigurovat službu s koncovým bodem zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-231">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="1b8e9-232">Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-232">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="1b8e9-233">Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-233">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="1b8e9-234">Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-234">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1b8e9-235">Aktuální adresář a obsahu root</span><span class="sxs-lookup"><span data-stu-id="1b8e9-235">Current directory and content root</span></span>

<span data-ttu-id="1b8e9-236">Aktuální pracovní adresář vrátit voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu Windows je *C:\\WINDOWS\\system32* složky.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-236">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1b8e9-237">*System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="1b8e9-237">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1b8e9-238">Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředků a souborů s nastavením služby.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-238">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="1b8e9-239">Nastavení obsahu kořenovou cestu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="1b8e9-239">Set the content root path to the app's folder</span></span>

<span data-ttu-id="1b8e9-240"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Se stejnou cestu k dispozici na `binPath` argument při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-240">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="1b8e9-241">Namísto volání metody `GetCurrentDirectory` cest k souborům nastavení vytvoříte volání <xref:System.IO.Directory.SetCurrentDirectory*> cestu ke kořenové obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-241">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="1b8e9-242">V `Program.Main`, určit cestu ke složce spustitelný soubor služby a použijte cestu k vytvoření obsahu kořenové aplikace:</span><span class="sxs-lookup"><span data-stu-id="1b8e9-242">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1b8e9-243">Soubory služby Store na vhodné místo na disku</span><span class="sxs-lookup"><span data-stu-id="1b8e9-243">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="1b8e9-244">Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.</span><span class="sxs-lookup"><span data-stu-id="1b8e9-244">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b8e9-245">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1b8e9-245">Additional resources</span></span>

* <span data-ttu-id="1b8e9-246">[Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="1b8e9-246">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
