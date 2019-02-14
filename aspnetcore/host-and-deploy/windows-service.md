---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/13/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 081a631c9c3e74c01e15f4b0b272d650c162bd20
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248248"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="fcea5-103">Hostitele ASP.NET Core ve službě Windows</span><span class="sxs-lookup"><span data-stu-id="fcea5-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="fcea5-104">Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="fcea5-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="fcea5-105">Na Windows, jako je možné hostovat aplikace ASP.NET Core [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fcea5-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="fcea5-106">Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování počítače.</span><span class="sxs-lookup"><span data-stu-id="fcea5-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="fcea5-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fcea5-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="deployment-type"></a><span data-ttu-id="fcea5-108">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="fcea5-108">Deployment type</span></span>

<span data-ttu-id="fcea5-109">Můžete vytvořit buď závisí na architektuře nebo samostatná Windows nasazení služby.</span><span class="sxs-lookup"><span data-stu-id="fcea5-109">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="fcea5-110">Informace a Rady, scénáře nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="fcea5-110">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="fcea5-111">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="fcea5-111">Framework-dependent deployment</span></span>

<span data-ttu-id="fcea5-112">Nasazení závisí na architektuře (chyba) spoléhá na přítomnost sdílené systémová verzi .NET Core v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="fcea5-112">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="fcea5-113">Při použití scénář disketové jednotky s aplikací ASP.NET Core Windows Service SDK vytvoří spustitelný soubor (*\*.exe*), označované jako *spustitelného souboru závisí na architektuře*.</span><span class="sxs-lookup"><span data-stu-id="fcea5-113">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="fcea5-114">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="fcea5-114">Self-contained deployment</span></span>

<span data-ttu-id="fcea5-115">Samostatná nasazení (SCD) nemusí spoléhat na přítomnost sdílené komponenty v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="fcea5-115">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="fcea5-116">Modul runtime a závislostí aplikace jsou nasazené v aplikaci k hostování systému.</span><span class="sxs-lookup"><span data-stu-id="fcea5-116">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="fcea5-117">Převést projekt do služby Windows</span><span class="sxs-lookup"><span data-stu-id="fcea5-117">Convert a project into a Windows Service</span></span>

<span data-ttu-id="fcea5-118">Do existujícího projektu ASP.NET Core a spusťte tak aplikaci jako službu, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="fcea5-118">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="fcea5-119">Aktualizace souboru projektu</span><span class="sxs-lookup"><span data-stu-id="fcea5-119">Project file updates</span></span>

<span data-ttu-id="fcea5-120">Podle podle vaší volby [typ nasazení](#deployment-type), aktualizujte soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="fcea5-120">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="fcea5-121">Nasazení závisí na architektuře (chyba)</span><span class="sxs-lookup"><span data-stu-id="fcea5-121">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="fcea5-122">Přidat Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) k `<PropertyGroup>` , která obsahuje cílové rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fcea5-122">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="fcea5-123">V následujícím příkladu RID nastavena `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-123">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="fcea5-124">Přidat `<SelfContained>` nastavenou na `false`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-124">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="fcea5-125">Tyto vlastnosti dáte pokyn, aby sada SDK pro generování spustitelného souboru (*.exe*) souborů pro Windows.</span><span class="sxs-lookup"><span data-stu-id="fcea5-125">These properties instruct the SDK to generate an executable (*.exe*) file for Windows.</span></span>

<span data-ttu-id="fcea5-126">A *web.config* soubor, který je obvykle vytvořen při publikování aplikace ASP.NET Core, není nutné pro aplikaci služby Windows.</span><span class="sxs-lookup"><span data-stu-id="fcea5-126">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fcea5-127">Chcete-li zakázat vytváření *web.config* přidejte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-127">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="fcea5-128">Přidat `<UseAppHost>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-128">Add the `<UseAppHost>` property set to `true`.</span></span> <span data-ttu-id="fcea5-129">Tato vlastnost poskytuje službu s cestou aktivace (spustitelný soubor, *.exe*) pro disketové jednotky.</span><span class="sxs-lookup"><span data-stu-id="fcea5-129">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="fcea5-130">Samostatná nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="fcea5-130">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="fcea5-131">Ověřte existenci Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) nebo přidání identifikátorů RID pro `<PropertyGroup>` , která obsahuje cílové rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fcea5-131">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="fcea5-132">Zakázat vytváření *web.config* souboru tak, že přidáte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-132">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="fcea5-133">Chcete-li publikovat pro více identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="fcea5-133">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="fcea5-134">Zadejte identifikátory RID v seznam oddělený středníkem.</span><span class="sxs-lookup"><span data-stu-id="fcea5-134">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="fcea5-135">Použijte název vlastnosti `<RuntimeIdentifiers>` (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="fcea5-135">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="fcea5-136">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="fcea5-136">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="fcea5-137">Přidat odkaz na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="fcea5-137">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="fcea5-138">Pokud chcete povolit protokolování protokolu událostí Windows, přidejte odkaz na balíček pro [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="fcea5-138">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="fcea5-139">Další informace najdete v tématu [zpracování, spouštění a zastavování události](#handle-starting-and-stopping-events) oddílu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-139">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="fcea5-140">Aktualizace Program.Main</span><span class="sxs-lookup"><span data-stu-id="fcea5-140">Program.Main updates</span></span>

<span data-ttu-id="fcea5-141">Proveďte následující změny v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fcea5-141">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="fcea5-142">K testování a ladění, když se provozují mimo službu, přidání kódu k určení, jestli aplikace běží jako službu nebo konzolové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fcea5-142">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="fcea5-143">Kontrola, pokud je připojen ladicí program nebo `--console` argument příkazového řádku je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="fcea5-143">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="fcea5-144">Pokud je některá podmínka pravdivá (aplikace není spuštěna jako služba), volání <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> na webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="fcea5-144">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="fcea5-145">Pokud jsou podmínky hodnotu false (aplikace je spuštěn jako služba):</span><span class="sxs-lookup"><span data-stu-id="fcea5-145">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="fcea5-146">Volání <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="fcea5-146">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="fcea5-147">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace Windows Service vrátí *C:\\WINDOWS\\system32* složky při <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="fcea5-147">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="fcea5-148">Další informace najdete v tématu [aktuálního adresáře a kořenový adresář obsahu](#current-directory-and-content-root) oddílu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-148">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="fcea5-149">Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> a spusťte tak aplikaci jako službu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-149">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="fcea5-150">Protože [poskytovatele konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínače se odebere z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> je obdrží.</span><span class="sxs-lookup"><span data-stu-id="fcea5-150">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="fcea5-151">Zapsat do protokolu událostí Windows, přidejte zprostředkovatele protokolu událostí na <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="fcea5-151">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="fcea5-152">Nastavení úrovně protokolování s `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.</span><span class="sxs-lookup"><span data-stu-id="fcea5-152">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="fcea5-153">Soubor nastavení produkčním prostředí ukázkovou aplikaci pro demonstrační účely a testování, nastaví úroveň protokolování na `Information`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-153">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="fcea5-154">V produkčním prostředí, hodnota se obvykle nastavuje na `Error`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-154">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="fcea5-155">Další informace naleznete v tématu <xref:fundamentals/logging/index#windows-eventlog-provider>.</span><span class="sxs-lookup"><span data-stu-id="fcea5-155">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a><span data-ttu-id="fcea5-156">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="fcea5-156">Publish the app</span></span>

<span data-ttu-id="fcea5-157">Publikování aplikace pomocí [dotnet publikovat](/dotnet/articles/core/tools/dotnet-publish), [profil publikování pro Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles), nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fcea5-157">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="fcea5-158">Když pomocí sady Visual Studio, vyberte **FolderProfile** a nakonfigurovat **cílové umístění** před výběrem **publikovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fcea5-158">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="fcea5-159">Chcete-li publikovat ukázkovou aplikaci pomocí nástrojů rozhraní příkazového řádku (CLI), spusťte [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu na příkazovém řádku ve složce projektu s předat konfiguraci vydané verze [- c |--konfigurace](/dotnet/core/tools/dotnet-publish#options)možnost.</span><span class="sxs-lookup"><span data-stu-id="fcea5-159">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="fcea5-160">Použití [-o |--výstup](/dotnet/core/tools/dotnet-publish#options) možnost s cestou k publikování do složky mimo aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fcea5-160">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

#### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="fcea5-161">Publikování nasazení závisí na architektuře (chyba)</span><span class="sxs-lookup"><span data-stu-id="fcea5-161">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="fcea5-162">V následujícím příkladu je aplikace publikována na *c:\\svc* složky:</span><span class="sxs-lookup"><span data-stu-id="fcea5-162">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="fcea5-163">Publikování samostatná nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="fcea5-163">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="fcea5-164">V musí být zadán identifikátor RID `<RuntimeIdenfifier>` (nebo `<RuntimeIdentifiers>`) vlastnost souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-164">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="fcea5-165">Zadejte modul runtime [- r |--runtime](/dotnet/core/tools/dotnet-publish#options) možnost `dotnet publish` příkazu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-165">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="fcea5-166">V následujícím příkladu je aplikace publikována pro `win7-x64` modulu runtime *c:\\svc* složky:</span><span class="sxs-lookup"><span data-stu-id="fcea5-166">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a><span data-ttu-id="fcea5-167">Vytvoření uživatelského účtu</span><span class="sxs-lookup"><span data-stu-id="fcea5-167">Create a user account</span></span>

<span data-ttu-id="fcea5-168">Vytvoření uživatelského účtu pro službu pomocí `net user` příkaz správu příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="fcea5-168">Create a user account for the service using the `net user` command from an administrative command shell:</span></span>

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

<span data-ttu-id="fcea5-169">Vypršení platnosti hesla výchozí je šest týdnů.</span><span class="sxs-lookup"><span data-stu-id="fcea5-169">The default password expiration is six weeks.</span></span>

<span data-ttu-id="fcea5-170">Pro ukázkovou aplikaci, vytvořte uživatelský účet s názvem `ServiceUser` a heslo.</span><span class="sxs-lookup"><span data-stu-id="fcea5-170">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="fcea5-171">V následujícím příkazu nahraďte `{PASSWORD}` s [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span><span class="sxs-lookup"><span data-stu-id="fcea5-171">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

```console
net user ServiceUser {PASSWORD} /add
```

<span data-ttu-id="fcea5-172">Pokud potřebujete přidat uživatele do skupiny, použijte `net localgroup` příkaz, kde `{GROUP}` je název skupiny:</span><span class="sxs-lookup"><span data-stu-id="fcea5-172">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

<span data-ttu-id="fcea5-173">Další informace najdete v tématu [uživatelské účty služby](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="fcea5-173">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="fcea5-174">Alternativní způsob správy uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="fcea5-174">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="fcea5-175">Další informace najdete v tématu [přehled účtů spravované služby skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="fcea5-175">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

### <a name="set-permissions"></a><span data-ttu-id="fcea5-176">Nastavení oprávnění</span><span class="sxs-lookup"><span data-stu-id="fcea5-176">Set permissions</span></span>

#### <a name="access-to-the-app-folder"></a><span data-ttu-id="fcea5-177">Přístup ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="fcea5-177">Access to the app folder</span></span>

<span data-ttu-id="fcea5-178">Udělit přístup, zápis a čtení a spouštění do složky aplikace pomocí [icacls](/windows-server/administration/windows-commands/icacls) příkaz správu příkazové prostředí:</span><span class="sxs-lookup"><span data-stu-id="fcea5-178">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command from an administrative command shell:</span></span>

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* <span data-ttu-id="fcea5-179">`{PATH}` &ndash; Cesta ke složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="fcea5-179">`{PATH}` &ndash; Path to the app's folder.</span></span>
* <span data-ttu-id="fcea5-180">`{USER ACCOUNT}` &ndash; Uživatelský účet (SID).</span><span class="sxs-lookup"><span data-stu-id="fcea5-180">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
* <span data-ttu-id="fcea5-181">`(OI)` &ndash; Příznak objekt dědit šíří oprávnění na podřízené soubory.</span><span class="sxs-lookup"><span data-stu-id="fcea5-181">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* <span data-ttu-id="fcea5-182">`(CI)` &ndash; Příznak kontejneru dědit šíří oprávnění na podřízené složky.</span><span class="sxs-lookup"><span data-stu-id="fcea5-182">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* <span data-ttu-id="fcea5-183">`{PERMISSION FLAGS}` &ndash; Nastaví její oprávnění přístupu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-183">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="fcea5-184">Zápis (`W`)</span><span class="sxs-lookup"><span data-stu-id="fcea5-184">Write (`W`)</span></span>
  * <span data-ttu-id="fcea5-185">Čtení (`R`)</span><span class="sxs-lookup"><span data-stu-id="fcea5-185">Read (`R`)</span></span>
  * <span data-ttu-id="fcea5-186">Spuštění (`X`)</span><span class="sxs-lookup"><span data-stu-id="fcea5-186">Execute (`X`)</span></span>
  * <span data-ttu-id="fcea5-187">Úplné (`F`)</span><span class="sxs-lookup"><span data-stu-id="fcea5-187">Full (`F`)</span></span>
  * <span data-ttu-id="fcea5-188">Upravit (`M`)</span><span class="sxs-lookup"><span data-stu-id="fcea5-188">Modify (`M`)</span></span>
* <span data-ttu-id="fcea5-189">`/t` &ndash; Rekurzivně se vztahují na existující podřízené složky a soubory.</span><span class="sxs-lookup"><span data-stu-id="fcea5-189">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="fcea5-190">Pro publikování ukázkové aplikace *c:\\svc* složky a `ServiceUser` účet s oprávněními pro zápis a čtení a spouštění, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fcea5-190">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

<span data-ttu-id="fcea5-191">Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="fcea5-191">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

#### <a name="log-on-as-a-service"></a><span data-ttu-id="fcea5-192">Přihlaste se jako služba</span><span class="sxs-lookup"><span data-stu-id="fcea5-192">Log on as a service</span></span>

<span data-ttu-id="fcea5-193">Udělit [přihlásit jako službu](/windows/security/threat-protection/security-policy-settings/log-on-as-a-service) oprávnění pro uživatelský účet:</span><span class="sxs-lookup"><span data-stu-id="fcea5-193">To grant the [Log on as a service](/windows/security/threat-protection/security-policy-settings/log-on-as-a-service) privilege to the user account:</span></span>

1. <span data-ttu-id="fcea5-194">Vyhledejte **přiřazení uživatelských práv** zásad v konzole místní zásady zabezpečení nebo konzolu Editor místních zásad skupiny.</span><span class="sxs-lookup"><span data-stu-id="fcea5-194">Locate the **User Rights Assignment** policies in either the Local Security Policy console or Local Group Policy Editor console.</span></span> <span data-ttu-id="fcea5-195">Pokyny najdete v tématu: [Konfigurovat nastavení zásad zabezpečení](/windows/security/threat-protection/security-policy-settings/how-to-configure-security-policy-settings).</span><span class="sxs-lookup"><span data-stu-id="fcea5-195">For instructions, see: [Configure security policy settings](/windows/security/threat-protection/security-policy-settings/how-to-configure-security-policy-settings).</span></span>
1. <span data-ttu-id="fcea5-196">Vyhledejte `Log on as a service` zásad.</span><span class="sxs-lookup"><span data-stu-id="fcea5-196">Locate the `Log on as a service` policy.</span></span> <span data-ttu-id="fcea5-197">Dvakrát klikněte na zásady tak, aby ho otevřete.</span><span class="sxs-lookup"><span data-stu-id="fcea5-197">Double-click the policy to open it.</span></span>
1. <span data-ttu-id="fcea5-198">Vyberte **přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="fcea5-198">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="fcea5-199">Vyberte **Upřesnit** a vyberte **najít**.</span><span class="sxs-lookup"><span data-stu-id="fcea5-199">Select **Advanced** and select **Find Now**.</span></span>
1. <span data-ttu-id="fcea5-200">Vyberte uživatelský účet vytvořený v [vytvoření uživatelského účtu](#create-a-user-account) výše v části.</span><span class="sxs-lookup"><span data-stu-id="fcea5-200">Select the user account created in the [Create a user account](#create-a-user-account) section earlier.</span></span> <span data-ttu-id="fcea5-201">Vyberte **OK** potvrďte výběr.</span><span class="sxs-lookup"><span data-stu-id="fcea5-201">Select **OK** to accept the selection.</span></span>
1. <span data-ttu-id="fcea5-202">Vyberte **OK** po potvrzení, že je správný název objektu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-202">Select **OK** after confirming that the object name is correct.</span></span>
1. <span data-ttu-id="fcea5-203">Vyberte **Použít**.</span><span class="sxs-lookup"><span data-stu-id="fcea5-203">Select **Apply**.</span></span> <span data-ttu-id="fcea5-204">Vyberte **OK** zavřete okno zásady.</span><span class="sxs-lookup"><span data-stu-id="fcea5-204">Select **OK** to close the policy window.</span></span>

## <a name="manage-the-service"></a><span data-ttu-id="fcea5-205">Spravovat službu</span><span class="sxs-lookup"><span data-stu-id="fcea5-205">Manage the service</span></span>

### <a name="create-the-service"></a><span data-ttu-id="fcea5-206">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="fcea5-206">Create the service</span></span>

<span data-ttu-id="fcea5-207">Použití [sc.exe](https://technet.microsoft.com/library/bb490995) vytvoříte službu z správu příkazové okno nástroje příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fcea5-207">Use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create the service from an administrative command shell.</span></span> <span data-ttu-id="fcea5-208">`binPath` Hodnota je cesta ke spustitelnému souboru aplikace, která zahrnuje název spustitelného souboru.</span><span class="sxs-lookup"><span data-stu-id="fcea5-208">The `binPath` value is the path to the app's executable, which includes the executable file name.</span></span> <span data-ttu-id="fcea5-209">**Mezera mezi znaménko rovná se a znak pro uvození každého parametru a hodnota je povinný.**</span><span class="sxs-lookup"><span data-stu-id="fcea5-209">**The space between the equal sign and the quote character of each parameter and value is required.**</span></span>

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* <span data-ttu-id="fcea5-210">`{SERVICE NAME}` &ndash; Název, který chcete přiřadit ke službě v [správce řízení služeb](/windows/desktop/services/service-control-manager).</span><span class="sxs-lookup"><span data-stu-id="fcea5-210">`{SERVICE NAME}` &ndash; The name to assign to the service in [Service Control Manager](/windows/desktop/services/service-control-manager).</span></span>
* <span data-ttu-id="fcea5-211">`{PATH}` &ndash; Cesta ke spustitelnému souboru služby.</span><span class="sxs-lookup"><span data-stu-id="fcea5-211">`{PATH}` &ndash; The path to the service executable.</span></span>
* <span data-ttu-id="fcea5-212">`{DOMAIN}` &ndash; Doména počítače připojené k doméně.</span><span class="sxs-lookup"><span data-stu-id="fcea5-212">`{DOMAIN}` &ndash; The domain of a domain-joined machine.</span></span> <span data-ttu-id="fcea5-213">Pokud počítač není připojený k doméně, použijte název místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fcea5-213">If the machine isn't domain-joined, use the local machine name.</span></span>
* <span data-ttu-id="fcea5-214">`{USER ACCOUNT}` &ndash; Uživatelský účet, pod kterým je služba spuštěna.</span><span class="sxs-lookup"><span data-stu-id="fcea5-214">`{USER ACCOUNT}` &ndash; The user account under which the service runs.</span></span>
* <span data-ttu-id="fcea5-215">`{PASSWORD}` &ndash; Heslo k uživatelskému účtu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-215">`{PASSWORD}` &ndash; The user account password.</span></span>

> [!WARNING]
> <span data-ttu-id="fcea5-216">Proveďte **není** vynechat, nechte `obj` parametru.</span><span class="sxs-lookup"><span data-stu-id="fcea5-216">Do **not** omit the `obj` parameter.</span></span> <span data-ttu-id="fcea5-217">Výchozí hodnota pro `obj` je [účet LocalSystem](/windows/desktop/services/localsystem-account) účtu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-217">The default value for `obj` is the [LocalSystem account](/windows/desktop/services/localsystem-account) account.</span></span> <span data-ttu-id="fcea5-218">Spuštěná služba v rámci `LocalSystem` účet představuje významné bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="fcea5-218">Running a service under the `LocalSystem` account presents a significant security risk.</span></span> <span data-ttu-id="fcea5-219">Vždy spuštění služby pomocí uživatelského účtu, který má omezená oprávnění.</span><span class="sxs-lookup"><span data-stu-id="fcea5-219">Always run a service with a user account that has restricted privileges.</span></span>

<span data-ttu-id="fcea5-220">V následujícím příkladu pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fcea5-220">In the following example for the sample app:</span></span>

* <span data-ttu-id="fcea5-221">Služba má název **Moje_služba**.</span><span class="sxs-lookup"><span data-stu-id="fcea5-221">The service is named **MyService**.</span></span>
* <span data-ttu-id="fcea5-222">Publikované služba se nachází v *c:\\svc* složky.</span><span class="sxs-lookup"><span data-stu-id="fcea5-222">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="fcea5-223">Je název spustitelné aplikace *SampleApp.exe*.</span><span class="sxs-lookup"><span data-stu-id="fcea5-223">The app executable is named *SampleApp.exe*.</span></span> <span data-ttu-id="fcea5-224">Uzavřete `binPath` hodnotu do dvojitých uvozovek (").</span><span class="sxs-lookup"><span data-stu-id="fcea5-224">Enclose the `binPath` value in double quotation marks (").</span></span>
* <span data-ttu-id="fcea5-225">Je služba spuštěna pod `ServiceUser` účtu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-225">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="fcea5-226">Nahraďte `{DOMAIN}` s účtem uživatele domény nebo názvu místního počítače.</span><span class="sxs-lookup"><span data-stu-id="fcea5-226">Replace `{DOMAIN}` with the user account's domain or local machine name.</span></span> <span data-ttu-id="fcea5-227">Uzavřete `obj` hodnotu do dvojitých uvozovek (").</span><span class="sxs-lookup"><span data-stu-id="fcea5-227">Enclose the `obj` value in double quotation marks (").</span></span> <span data-ttu-id="fcea5-228">Příklad: Pokud je hostující systém místní počítač s názvem `MairaPC`, nastavte `obj` k `"MairaPC\ServiceUser"`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-228">Example: If the hosting system is a local machine named `MairaPC`, set `obj` to `"MairaPC\ServiceUser"`.</span></span>
* <span data-ttu-id="fcea5-229">Nahraďte `{PASSWORD}` s heslem uživatelského účtu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-229">Replace `{PASSWORD}` with the user account's password.</span></span> <span data-ttu-id="fcea5-230">Uzavřete `password` hodnotu do dvojitých uvozovek (").</span><span class="sxs-lookup"><span data-stu-id="fcea5-230">Enclose the `password` value in double quotation marks (").</span></span>

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> <span data-ttu-id="fcea5-231">Ujistěte se, že mezery mezi symboly rovná parametrů a hodnot parametrů jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="fcea5-231">Make sure that the spaces between the parameters' equal signs and the parameters' values are present.</span></span>

### <a name="start-the-service"></a><span data-ttu-id="fcea5-232">Spustit službu</span><span class="sxs-lookup"><span data-stu-id="fcea5-232">Start the service</span></span>

<span data-ttu-id="fcea5-233">Spusťte službu pomocí `sc start {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-233">Start the service with the `sc start {SERVICE NAME}` command.</span></span>

<span data-ttu-id="fcea5-234">Spustit službu ukázkové aplikace, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="fcea5-234">To start the sample app service, use the following command:</span></span>

```console
sc start MyService
```

<span data-ttu-id="fcea5-235">Příkaz trvá několik sekund se spustit službu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-235">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="fcea5-236">Zjistit stav služby</span><span class="sxs-lookup"><span data-stu-id="fcea5-236">Determine the service status</span></span>

<span data-ttu-id="fcea5-237">Chcete-li zkontrolovat stav služby, použijte `sc query {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-237">To check the status of the service, use the `sc query {SERVICE NAME}` command.</span></span> <span data-ttu-id="fcea5-238">Stav je uveden jako jeden z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="fcea5-238">The status is reported as one of the following values:</span></span>

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

<span data-ttu-id="fcea5-239">Použijte následující příkaz a zkontrolujte stav služby app service vzorku:</span><span class="sxs-lookup"><span data-stu-id="fcea5-239">Use the following command to check the status of the sample app service:</span></span>

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="fcea5-240">Procházet služba webové aplikace</span><span class="sxs-lookup"><span data-stu-id="fcea5-240">Browse a web app service</span></span>

<span data-ttu-id="fcea5-241">Pokud je služba v `RUNNING` stavu a pokud je služba webové aplikace, procházet aplikace, její cesta (ve výchozím nastavení, `http://localhost:5000`, který přesměruje `https://localhost:5001` při použití [HTTPS přesměrování Middleware](xref:security/enforcing-ssl)).</span><span class="sxs-lookup"><span data-stu-id="fcea5-241">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="fcea5-242">Aplikační služba ukázkového procházet aplikace na adrese `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="fcea5-242">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="fcea5-243">Zastavit službu</span><span class="sxs-lookup"><span data-stu-id="fcea5-243">Stop the service</span></span>

<span data-ttu-id="fcea5-244">Zastavit službu s `sc stop {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-244">Stop the service with the `sc stop {SERVICE NAME}` command.</span></span>

<span data-ttu-id="fcea5-245">Následující příkaz zastaví aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="fcea5-245">The following command stops the sample app service:</span></span>

```console
sc stop MyService
```

### <a name="delete-the-service"></a><span data-ttu-id="fcea5-246">Odstranit službu</span><span class="sxs-lookup"><span data-stu-id="fcea5-246">Delete the service</span></span>

<span data-ttu-id="fcea5-247">Po krátké prodlevě zastavit službu, odinstalujte službu s `sc delete {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-247">After a short delay to stop a service, uninstall the service with the `sc delete {SERVICE NAME}` command.</span></span>

<span data-ttu-id="fcea5-248">Postup kontroly stavu aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="fcea5-248">Check the status of the sample app service:</span></span>

```console
sc query MyService
```

<span data-ttu-id="fcea5-249">Pokud je aplikační služba ukázkového v `STOPPED` stavu, použijte následující příkaz pro odinstalaci aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="fcea5-249">When the sample app service is in the `STOPPED` state, use the following command to uninstall the sample app service:</span></span>

```console
sc delete MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="fcea5-250">Zpracování spuštění a zastavení událostí</span><span class="sxs-lookup"><span data-stu-id="fcea5-250">Handle starting and stopping events</span></span>

<span data-ttu-id="fcea5-251">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události, proveďte následující další změny:</span><span class="sxs-lookup"><span data-stu-id="fcea5-251">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="fcea5-252">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting`, `OnStarted`, a `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="fcea5-252">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="fcea5-253">Vytvořit metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , který předá `CustomWebHostService` k <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="fcea5-253">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="fcea5-254">V `Program.Main`, zavolejte `RunAsCustomService` místo rozšiřující metoda <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="fcea5-254">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="fcea5-255">Pokud chcete zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, najdete vzorový kód uvedený v [převést projekt do služby Windows](#convert-a-project-into-a-windows-service) oddílu.</span><span class="sxs-lookup"><span data-stu-id="fcea5-255">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fcea5-256">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="fcea5-256">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fcea5-257">Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="fcea5-257">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="fcea5-258">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="fcea5-258">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="fcea5-259">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="fcea5-259">Configure HTTPS</span></span>

<span data-ttu-id="fcea5-260">Jak nakonfigurovat službu s koncovým bodem zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="fcea5-260">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="fcea5-261">Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.</span><span class="sxs-lookup"><span data-stu-id="fcea5-261">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="fcea5-262">Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.</span><span class="sxs-lookup"><span data-stu-id="fcea5-262">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="fcea5-263">Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="fcea5-263">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="fcea5-264">Aktuální adresář a obsahu root</span><span class="sxs-lookup"><span data-stu-id="fcea5-264">Current directory and content root</span></span>

<span data-ttu-id="fcea5-265">Aktuální pracovní adresář vrátit voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu Windows je *C:\\WINDOWS\\system32* složky.</span><span class="sxs-lookup"><span data-stu-id="fcea5-265">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="fcea5-266">*System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="fcea5-266">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="fcea5-267">Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředků a souborů s nastavením služby.</span><span class="sxs-lookup"><span data-stu-id="fcea5-267">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="fcea5-268">Nastavení obsahu kořenovou cestu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="fcea5-268">Set the content root path to the app's folder</span></span>

<span data-ttu-id="fcea5-269"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Se stejnou cestu k dispozici na `binPath` argument při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="fcea5-269">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="fcea5-270">Namísto volání metody `GetCurrentDirectory` cest k souborům nastavení vytvoříte volání <xref:System.IO.Directory.SetCurrentDirectory*> cestu ke kořenové obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fcea5-270">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="fcea5-271">V `Program.Main`, určit cestu ke složce spustitelný soubor služby a použijte cestu k vytvoření obsahu kořenové aplikace:</span><span class="sxs-lookup"><span data-stu-id="fcea5-271">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="fcea5-272">Soubory služby Store na vhodné místo na disku</span><span class="sxs-lookup"><span data-stu-id="fcea5-272">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="fcea5-273">Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.</span><span class="sxs-lookup"><span data-stu-id="fcea5-273">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fcea5-274">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fcea5-274">Additional resources</span></span>

* <span data-ttu-id="fcea5-275">[Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="fcea5-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
