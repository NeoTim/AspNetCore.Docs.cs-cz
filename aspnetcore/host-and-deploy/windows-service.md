---
title: Hostitele ASP.NET Core ve službě Windows
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core ve službě Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/01/2018
uid: host-and-deploy/windows-service
ms.openlocfilehash: f53c303dc63e092f08e933fea79eb805523cde9b
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861391"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="c15a8-103">Hostitele ASP.NET Core ve službě Windows</span><span class="sxs-lookup"><span data-stu-id="c15a8-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="c15a8-104">Podle [Luke Latham](https://github.com/guardrex) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="c15a8-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="c15a8-105">Na Windows, jako je možné hostovat aplikace ASP.NET Core [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="c15a8-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="c15a8-106">Pokud hostovaný jako služba Windows, aplikace se automaticky spustí po restartování počítače.</span><span class="sxs-lookup"><span data-stu-id="c15a8-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="c15a8-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c15a8-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="deployment-type"></a><span data-ttu-id="c15a8-108">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="c15a8-108">Deployment type</span></span>

<span data-ttu-id="c15a8-109">Můžete vytvořit buď závisí na architektuře nebo samostatná Windows nasazení služby.</span><span class="sxs-lookup"><span data-stu-id="c15a8-109">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="c15a8-110">Informace a Rady, scénáře nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="c15a8-110">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="c15a8-111">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="c15a8-111">Framework-dependent deployment</span></span>

<span data-ttu-id="c15a8-112">Nasazení závisí na architektuře (chyba) spoléhá na přítomnost sdílené systémová verzi .NET Core v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="c15a8-112">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="c15a8-113">Při použití scénář disketové jednotky s aplikací ASP.NET Core Windows Service SDK vytvoří spustitelný soubor (*\*.exe*), označované jako *spustitelného souboru závisí na architektuře*.</span><span class="sxs-lookup"><span data-stu-id="c15a8-113">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="c15a8-114">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="c15a8-114">Self-contained deployment</span></span>

<span data-ttu-id="c15a8-115">Samostatná nasazení (SCD) nemusí spoléhat na přítomnost sdílené komponenty v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="c15a8-115">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="c15a8-116">Modul runtime a závislostí aplikace jsou nasazené v aplikaci k hostování systému.</span><span class="sxs-lookup"><span data-stu-id="c15a8-116">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="c15a8-117">Převést projekt do služby Windows</span><span class="sxs-lookup"><span data-stu-id="c15a8-117">Convert a project into a Windows Service</span></span>

<span data-ttu-id="c15a8-118">Do existujícího projektu ASP.NET Core a spusťte tak aplikaci jako službu, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="c15a8-118">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="c15a8-119">Aktualizace souboru projektu</span><span class="sxs-lookup"><span data-stu-id="c15a8-119">Project file updates</span></span>

<span data-ttu-id="c15a8-120">Podle podle vaší volby [typ nasazení](#deployment-type), aktualizujte soubor projektu:</span><span class="sxs-lookup"><span data-stu-id="c15a8-120">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="c15a8-121">Nasazení závisí na architektuře (chyba)</span><span class="sxs-lookup"><span data-stu-id="c15a8-121">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="c15a8-122">Přidat Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) k `<PropertyGroup>` , která obsahuje cílové rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c15a8-122">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="c15a8-123">Přidat `<SelfContained>` nastavenou na `false`.</span><span class="sxs-lookup"><span data-stu-id="c15a8-123">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="c15a8-124">Zakázat vytváření *web.config* souboru tak, že přidáte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="c15a8-124">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="c15a8-125">Samostatná nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="c15a8-125">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="c15a8-126">Ověřte existenci Windows [identifikátor modulu Runtime (RID)](/dotnet/core/rid-catalog) nebo přidání identifikátorů RID pro `<PropertyGroup>` , která obsahuje cílové rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c15a8-126">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="c15a8-127">Zakázat vytváření *web.config* souboru tak, že přidáte `<IsTransformWebConfigDisabled>` nastavenou na `true`.</span><span class="sxs-lookup"><span data-stu-id="c15a8-127">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="c15a8-128">Chcete-li publikovat pro více identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="c15a8-128">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="c15a8-129">Zadejte identifikátory RID v seznam oddělený středníkem.</span><span class="sxs-lookup"><span data-stu-id="c15a8-129">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="c15a8-130">Použijte název vlastnosti `<RuntimeIdentifiers>` (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="c15a8-130">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="c15a8-131">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="c15a8-131">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="c15a8-132">Přidat odkaz na balíček pro [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="c15a8-132">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="c15a8-133">Pokud chcete povolit protokolování protokolu událostí Windows, přidejte odkaz na balíček pro [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="c15a8-133">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="c15a8-134">Další informace najdete v tématu [zpracování, spouštění a zastavování události](#handle-starting-and-stopping-events) oddílu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-134">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="c15a8-135">Aktualizace Program.Main</span><span class="sxs-lookup"><span data-stu-id="c15a8-135">Program.Main updates</span></span>

<span data-ttu-id="c15a8-136">Proveďte následující změny v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c15a8-136">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="c15a8-137">K testování a ladění, když se provozují mimo službu, přidání kódu k určení, jestli aplikace běží jako službu nebo konzolové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c15a8-137">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="c15a8-138">Kontrola, pokud je připojen ladicí program nebo `--console` argument příkazového řádku je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c15a8-138">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="c15a8-139">Pokud je některá podmínka pravdivá (aplikace není spuštěna jako služba), volání <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> na webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="c15a8-139">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="c15a8-140">Pokud jsou podmínky hodnotu false (aplikace je spuštěn jako služba):</span><span class="sxs-lookup"><span data-stu-id="c15a8-140">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="c15a8-141">Volání <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="c15a8-141">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> and use a path to the app's published location.</span></span> <span data-ttu-id="c15a8-142">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace Windows Service vrátí *C:\\WINDOWS\\system32* složky při `GetCurrentDirectory` je volána.</span><span class="sxs-lookup"><span data-stu-id="c15a8-142">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when `GetCurrentDirectory` is called.</span></span> <span data-ttu-id="c15a8-143">Další informace najdete v tématu [aktuálního adresáře a kořenový adresář obsahu](#current-directory-and-content-root) oddílu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-143">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="c15a8-144">Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> a spusťte tak aplikaci jako službu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-144">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="c15a8-145">Protože [poskytovatele konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název hodnota pro argumenty příkazového řádku, `--console` přepínače se odebere z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> je obdrží.</span><span class="sxs-lookup"><span data-stu-id="c15a8-145">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="c15a8-146">Zapsat do protokolu událostí Windows, přidejte zprostředkovatele protokolu událostí na <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="c15a8-146">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="c15a8-147">Nastavení úrovně protokolování s `Logging:LogLevel:Default` klíče v *appsettings. Production.JSON* souboru.</span><span class="sxs-lookup"><span data-stu-id="c15a8-147">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="c15a8-148">Soubor nastavení produkčním prostředí ukázkovou aplikaci pro demonstrační účely a testování, nastaví úroveň protokolování na `Information`.</span><span class="sxs-lookup"><span data-stu-id="c15a8-148">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="c15a8-149">V produkčním prostředí, hodnota se obvykle nastavuje na `Error`.</span><span class="sxs-lookup"><span data-stu-id="c15a8-149">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="c15a8-150">Další informace naleznete v tématu <xref:fundamentals/logging/index#windows-eventlog-provider>.</span><span class="sxs-lookup"><span data-stu-id="c15a8-150">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a><span data-ttu-id="c15a8-151">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="c15a8-151">Publish the app</span></span>

<span data-ttu-id="c15a8-152">Publikování aplikace pomocí [dotnet publikovat](/dotnet/articles/core/tools/dotnet-publish), [profil publikování pro Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles), nebo Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c15a8-152">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="c15a8-153">Když pomocí sady Visual Studio, vyberte **FolderProfile** a nakonfigurovat **cílové umístění** před výběrem **publikovat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="c15a8-153">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="c15a8-154">Chcete-li publikovat ukázkovou aplikaci pomocí nástrojů rozhraní příkazového řádku (CLI), spusťte [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu na příkazovém řádku ve složce projektu s předat konfiguraci vydané verze [- c |--konfigurace](/dotnet/core/tools/dotnet-publish#options)možnost.</span><span class="sxs-lookup"><span data-stu-id="c15a8-154">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="c15a8-155">Použití [-o |--výstup](/dotnet/core/tools/dotnet-publish#options) možnost s cestou k publikování do složky mimo aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c15a8-155">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

#### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="c15a8-156">Publikování nasazení závisí na architektuře (chyba)</span><span class="sxs-lookup"><span data-stu-id="c15a8-156">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="c15a8-157">V následujícím příkladu je aplikace publikována na *c:\\svc* složky:</span><span class="sxs-lookup"><span data-stu-id="c15a8-157">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="c15a8-158">Publikování samostatná nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="c15a8-158">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="c15a8-159">V musí být zadán identifikátor RID `<RuntimeIdenfifier>` (nebo `<RuntimeIdentifiers>`) vlastnost souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-159">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="c15a8-160">Zadejte modul runtime [- r |--runtime](/dotnet/core/tools/dotnet-publish#options) možnost `dotnet publish` příkazu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-160">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="c15a8-161">V následujícím příkladu je aplikace publikována pro `win7-x64` modulu runtime *c:\\svc* složky:</span><span class="sxs-lookup"><span data-stu-id="c15a8-161">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a><span data-ttu-id="c15a8-162">Vytvoření uživatelského účtu</span><span class="sxs-lookup"><span data-stu-id="c15a8-162">Create a user account</span></span>

<span data-ttu-id="c15a8-163">Vytvořte účet uživatele pro používání služby `net user` příkaz:</span><span class="sxs-lookup"><span data-stu-id="c15a8-163">Create a user account for the service using the `net user` command:</span></span>

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

<span data-ttu-id="c15a8-164">Pro ukázkovou aplikaci, vytvořte uživatelský účet s názvem `ServiceUser` a heslo.</span><span class="sxs-lookup"><span data-stu-id="c15a8-164">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="c15a8-165">V následujícím příkazu nahraďte `{PASSWORD}` s [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span><span class="sxs-lookup"><span data-stu-id="c15a8-165">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

```console
net user ServiceUser {PASSWORD} /add
```

<span data-ttu-id="c15a8-166">Pokud potřebujete přidat uživatele do skupiny, použijte `net localgroup` příkaz, kde `{GROUP}` je název skupiny:</span><span class="sxs-lookup"><span data-stu-id="c15a8-166">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

<span data-ttu-id="c15a8-167">Další informace najdete v tématu [uživatelské účty služby](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="c15a8-167">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

### <a name="set-permissions"></a><span data-ttu-id="c15a8-168">Nastavení oprávnění</span><span class="sxs-lookup"><span data-stu-id="c15a8-168">Set permissions</span></span>

<span data-ttu-id="c15a8-169">Udělit přístup, zápis a čtení a spouštění do složky aplikace pomocí [icacls](/windows-server/administration/windows-commands/icacls) příkaz:</span><span class="sxs-lookup"><span data-stu-id="c15a8-169">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command:</span></span>

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* <span data-ttu-id="c15a8-170">`{PATH}` &ndash; Cesta ke složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="c15a8-170">`{PATH}` &ndash; Path to the app's folder.</span></span>
* <span data-ttu-id="c15a8-171">`{USER ACCOUNT}` &ndash; Uživatelský účet (SID).</span><span class="sxs-lookup"><span data-stu-id="c15a8-171">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
* <span data-ttu-id="c15a8-172">`(OI)` &ndash; Příznak objekt dědit šíří oprávnění na podřízené soubory.</span><span class="sxs-lookup"><span data-stu-id="c15a8-172">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* <span data-ttu-id="c15a8-173">`(CI)` &ndash; Příznak kontejneru dědit šíří oprávnění na podřízené složky.</span><span class="sxs-lookup"><span data-stu-id="c15a8-173">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* <span data-ttu-id="c15a8-174">`{PERMISSION FLAGS}` &ndash; Nastaví její oprávnění přístupu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-174">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="c15a8-175">Zápis (`W`)</span><span class="sxs-lookup"><span data-stu-id="c15a8-175">Write (`W`)</span></span>
  * <span data-ttu-id="c15a8-176">Čtení (`R`)</span><span class="sxs-lookup"><span data-stu-id="c15a8-176">Read (`R`)</span></span>
  * <span data-ttu-id="c15a8-177">Spuštění (`X`)</span><span class="sxs-lookup"><span data-stu-id="c15a8-177">Execute (`X`)</span></span>
  * <span data-ttu-id="c15a8-178">Úplné (`F`)</span><span class="sxs-lookup"><span data-stu-id="c15a8-178">Full (`F`)</span></span>
  * <span data-ttu-id="c15a8-179">Upravit (`M`)</span><span class="sxs-lookup"><span data-stu-id="c15a8-179">Modify (`M`)</span></span>
* <span data-ttu-id="c15a8-180">`/t` &ndash; Rekurzivně se vztahují na existující podřízené složky a soubory.</span><span class="sxs-lookup"><span data-stu-id="c15a8-180">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="c15a8-181">Pro publikování ukázkové aplikace *c:\\svc* složky a `ServiceUser` účet s oprávněními pro zápis a čtení a spouštění, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c15a8-181">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

<span data-ttu-id="c15a8-182">Další informace najdete v tématu [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="c15a8-182">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

## <a name="manage-the-service"></a><span data-ttu-id="c15a8-183">Spravovat službu</span><span class="sxs-lookup"><span data-stu-id="c15a8-183">Manage the service</span></span>

### <a name="create-the-service"></a><span data-ttu-id="c15a8-184">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="c15a8-184">Create the service</span></span>

<span data-ttu-id="c15a8-185">Použití [sc.exe](https://technet.microsoft.com/library/bb490995) nástroj příkazového řádku vytvořte službu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-185">Use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create the service.</span></span> <span data-ttu-id="c15a8-186">`binPath` Hodnota je cesta ke spustitelnému souboru aplikace, která zahrnuje název spustitelného souboru.</span><span class="sxs-lookup"><span data-stu-id="c15a8-186">The `binPath` value is the path to the app's executable, which includes the executable file name.</span></span> <span data-ttu-id="c15a8-187">**Mezera mezi znaménko rovná se a znak pro uvození každého parametru a hodnota je povinný.**</span><span class="sxs-lookup"><span data-stu-id="c15a8-187">**The space between the equal sign and the quote character of each parameter and value is required.**</span></span>

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* <span data-ttu-id="c15a8-188">`{SERVICE NAME}` &ndash; Název, který chcete přiřadit ke službě v [správce řízení služeb](/windows/desktop/services/service-control-manager).</span><span class="sxs-lookup"><span data-stu-id="c15a8-188">`{SERVICE NAME}` &ndash; The name to assign to the service in [Service Control Manager](/windows/desktop/services/service-control-manager).</span></span>
* <span data-ttu-id="c15a8-189">`{PATH}` &ndash; Cesta ke spustitelnému souboru služby.</span><span class="sxs-lookup"><span data-stu-id="c15a8-189">`{PATH}` &ndash; The path to the service executable.</span></span>
* <span data-ttu-id="c15a8-190">`{DOMAIN}` &ndash; Doména počítače připojené k doméně.</span><span class="sxs-lookup"><span data-stu-id="c15a8-190">`{DOMAIN}` &ndash; The domain of a domain-joined machine.</span></span> <span data-ttu-id="c15a8-191">Pokud počítač není připojený k doméně, názvu místního počítače.</span><span class="sxs-lookup"><span data-stu-id="c15a8-191">If the machine isn't domain-joined, the local machine name.</span></span>
* <span data-ttu-id="c15a8-192">`{USER ACCOUNT}` &ndash; Uživatelský účet, pod kterým je služba spuštěna.</span><span class="sxs-lookup"><span data-stu-id="c15a8-192">`{USER ACCOUNT}` &ndash; The user account under which the service runs.</span></span>
* <span data-ttu-id="c15a8-193">`{PASSWORD}` &ndash; Heslo k uživatelskému účtu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-193">`{PASSWORD}` &ndash; The user account password.</span></span>

> [!WARNING]
> <span data-ttu-id="c15a8-194">Proveďte **není** vynechat, nechte `obj` parametru.</span><span class="sxs-lookup"><span data-stu-id="c15a8-194">Do **not** omit the `obj` parameter.</span></span> <span data-ttu-id="c15a8-195">Výchozí hodnota pro `obj` je [účet LocalSystem](/windows/desktop/services/localsystem-account) účtu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-195">The default value for `obj` is the [LocalSystem account](/windows/desktop/services/localsystem-account) account.</span></span> <span data-ttu-id="c15a8-196">Spuštěná služba v rámci `LocalSystem` účet představuje významné bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="c15a8-196">Running a service under the `LocalSystem` account presents a significant security risk.</span></span> <span data-ttu-id="c15a8-197">Vždy spuštění služby pomocí uživatelského účtu, který má omezená oprávnění.</span><span class="sxs-lookup"><span data-stu-id="c15a8-197">Always run a service with a user account that has restricted privileges.</span></span>

<span data-ttu-id="c15a8-198">V následujícím příkladu pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c15a8-198">In the following example for the sample app:</span></span>

* <span data-ttu-id="c15a8-199">Služba má název **Moje_služba**.</span><span class="sxs-lookup"><span data-stu-id="c15a8-199">The service is named **MyService**.</span></span>
* <span data-ttu-id="c15a8-200">Publikované služba se nachází v *c:\\svc* složky.</span><span class="sxs-lookup"><span data-stu-id="c15a8-200">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="c15a8-201">Je název spustitelné aplikace *SampleApp.exe*.</span><span class="sxs-lookup"><span data-stu-id="c15a8-201">The app executable is named *SampleApp.exe*.</span></span> <span data-ttu-id="c15a8-202">Uzavřete `binPath` hodnotu do dvojitých uvozovek (").</span><span class="sxs-lookup"><span data-stu-id="c15a8-202">Enclose the `binPath` value in double quotation marks (").</span></span>
* <span data-ttu-id="c15a8-203">Je služba spuštěna pod `ServiceUser` účtu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-203">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="c15a8-204">Nahraďte `{DOMAIN}` s účtem uživatele domény nebo názvu místního počítače.</span><span class="sxs-lookup"><span data-stu-id="c15a8-204">Replace `{DOMAIN}` with the user account's domain or local machine name.</span></span> <span data-ttu-id="c15a8-205">Uzavřete `obj` hodnotu do dvojitých uvozovek (").</span><span class="sxs-lookup"><span data-stu-id="c15a8-205">Enclose the `obj` value in double quotation marks (").</span></span> <span data-ttu-id="c15a8-206">Příklad: Pokud je hostující systém místní počítač s názvem `MairaPC`, nastavte `obj` k `"MairaPC\ServiceUser"`.</span><span class="sxs-lookup"><span data-stu-id="c15a8-206">Example: If the hosting system is a local machine named `MairaPC`, set `obj` to `"MairaPC\ServiceUser"`.</span></span>
* <span data-ttu-id="c15a8-207">Nahraďte `{PASSWORD}` s heslem uživatelského účtu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-207">Replace `{PASSWORD}` with the user account's password.</span></span> <span data-ttu-id="c15a8-208">Uzavřete `password` hodnotu do dvojitých uvozovek (").</span><span class="sxs-lookup"><span data-stu-id="c15a8-208">Enclose the `password` value in double quotation marks (").</span></span>

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> <span data-ttu-id="c15a8-209">Ujistěte se, že mezery mezi symboly rovná parametrů a hodnot parametrů jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c15a8-209">Make sure that the spaces between the parameters' equal signs and the parameters' values are present.</span></span>

### <a name="start-the-service"></a><span data-ttu-id="c15a8-210">Spustit službu</span><span class="sxs-lookup"><span data-stu-id="c15a8-210">Start the service</span></span>

<span data-ttu-id="c15a8-211">Spusťte službu pomocí `sc start {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-211">Start the service with the `sc start {SERVICE NAME}` command.</span></span>

<span data-ttu-id="c15a8-212">Spustit službu ukázkové aplikace, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c15a8-212">To start the sample app service, use the following command:</span></span>

```console
sc start MyService
```

<span data-ttu-id="c15a8-213">Příkaz trvá několik sekund se spustit službu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-213">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="c15a8-214">Zjistit stav služby</span><span class="sxs-lookup"><span data-stu-id="c15a8-214">Determine the service status</span></span>

<span data-ttu-id="c15a8-215">Chcete-li zkontrolovat stav služby, použijte `sc query {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-215">To check the status of the service, use the `sc query {SERVICE NAME}` command.</span></span> <span data-ttu-id="c15a8-216">Stav je uveden jako jeden z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="c15a8-216">The status is reported as one of the following values:</span></span>

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

<span data-ttu-id="c15a8-217">Použijte následující příkaz a zkontrolujte stav služby app service vzorku:</span><span class="sxs-lookup"><span data-stu-id="c15a8-217">Use the following command to check the status of the sample app service:</span></span>

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="c15a8-218">Procházet služba webové aplikace</span><span class="sxs-lookup"><span data-stu-id="c15a8-218">Browse a web app service</span></span>

<span data-ttu-id="c15a8-219">Pokud je služba v `RUNNING` stavu a pokud je služba webové aplikace, procházet aplikace, její cesta (ve výchozím nastavení, `http://localhost:5000`, který přesměruje `https://localhost:5001` při použití [HTTPS přesměrování Middleware](xref:security/enforcing-ssl)).</span><span class="sxs-lookup"><span data-stu-id="c15a8-219">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="c15a8-220">Aplikační služba ukázkového procházet aplikace na adrese `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="c15a8-220">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="c15a8-221">Zastavit službu</span><span class="sxs-lookup"><span data-stu-id="c15a8-221">Stop the service</span></span>

<span data-ttu-id="c15a8-222">Zastavit službu s `sc stop {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-222">Stop the service with the `sc stop {SERVICE NAME}` command.</span></span>

<span data-ttu-id="c15a8-223">Následující příkaz zastaví aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="c15a8-223">The following command stops the sample app service:</span></span>

```console
sc stop MyService
```

### <a name="delete-the-service"></a><span data-ttu-id="c15a8-224">Odstranit službu</span><span class="sxs-lookup"><span data-stu-id="c15a8-224">Delete the service</span></span>

<span data-ttu-id="c15a8-225">Po krátké prodlevě zastavit službu, odinstalujte službu s `sc delete {SERVICE NAME}` příkazu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-225">After a short delay to stop a service, uninstall the service with the `sc delete {SERVICE NAME}` command.</span></span>

<span data-ttu-id="c15a8-226">Postup kontroly stavu aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="c15a8-226">Check the status of the sample app service:</span></span>

```console
sc query MyService
```

<span data-ttu-id="c15a8-227">Pokud je aplikační služba ukázkového v `STOPPED` stavu, použijte následující příkaz pro odinstalaci aplikační služba ukázkového:</span><span class="sxs-lookup"><span data-stu-id="c15a8-227">When the sample app service is in the `STOPPED` state, use the following command to uninstall the sample app service:</span></span>

```console
sc delete MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="c15a8-228">Zpracování spuštění a zastavení událostí</span><span class="sxs-lookup"><span data-stu-id="c15a8-228">Handle starting and stopping events</span></span>

<span data-ttu-id="c15a8-229">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události, proveďte následující další změny:</span><span class="sxs-lookup"><span data-stu-id="c15a8-229">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="c15a8-230">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting`, `OnStarted`, a `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="c15a8-230">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="c15a8-231">Vytvořit metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , který předá `CustomWebHostService` k <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="c15a8-231">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="c15a8-232">V `Program.Main`, zavolejte `RunAsCustomService` místo rozšiřující metoda <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="c15a8-232">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="c15a8-233">Pokud chcete zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v `Program.Main`, najdete vzorový kód uvedený v [převést projekt do služby Windows](#convert-a-project-into-a-windows-service) oddílu.</span><span class="sxs-lookup"><span data-stu-id="c15a8-233">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c15a8-234">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="c15a8-234">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c15a8-235">Služby, které interakci s žádostí z Internetu nebo podnikové síti a jsou za proxy nebo nástroj pro vyrovnávání zatížení může vyžadovat dodatečnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="c15a8-235">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="c15a8-236">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c15a8-236">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="c15a8-237">Konfigurace HTTPS</span><span class="sxs-lookup"><span data-stu-id="c15a8-237">Configure HTTPS</span></span>

<span data-ttu-id="c15a8-238">Jak nakonfigurovat službu s koncovým bodem zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="c15a8-238">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="c15a8-239">Vytvořte certifikát X.509, který pro hostování systému získání certifikátů vaší platformě a mechanismy nasazení.</span><span class="sxs-lookup"><span data-stu-id="c15a8-239">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="c15a8-240">Zadejte [konfigurace koncového bodu HTTPS serveru Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) mohl certifikát používat.</span><span class="sxs-lookup"><span data-stu-id="c15a8-240">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="c15a8-241">Použití certifikátu vývoj pro ASP.NET Core HTTPS k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="c15a8-241">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="c15a8-242">Aktuální adresář a obsahu root</span><span class="sxs-lookup"><span data-stu-id="c15a8-242">Current directory and content root</span></span>

<span data-ttu-id="c15a8-243">Aktuální pracovní adresář vrátit voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu Windows je *C:\\WINDOWS\\system32* složky.</span><span class="sxs-lookup"><span data-stu-id="c15a8-243">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="c15a8-244">*System32* složka není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="c15a8-244">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="c15a8-245">Použijte jednu z následujících dvou přístupů k zajištění údržby a přístup k prostředků a souborů s nastavením služby.</span><span class="sxs-lookup"><span data-stu-id="c15a8-245">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="c15a8-246">Nastavení obsahu kořenovou cestu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="c15a8-246">Set the content root path to the app's folder</span></span>

<span data-ttu-id="c15a8-247"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Se stejnou cestu k dispozici na `binPath` argument při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="c15a8-247">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="c15a8-248">Namísto volání metody `GetCurrentDirectory` cest k souborům nastavení vytvoříte volání <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> cestu ke kořenové obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="c15a8-248">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> with the path to the app's content root.</span></span>

<span data-ttu-id="c15a8-249">V `Program.Main`, určit cestu ke složce spustitelný soubor služby a použijte cestu k vytvoření obsahu kořenové aplikace:</span><span class="sxs-lookup"><span data-stu-id="c15a8-249">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);

CreateWebHostBuilder(args)
    .UseContentRoot(pathToContentRoot)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="c15a8-250">Soubory služby Store na vhodné místo na disku</span><span class="sxs-lookup"><span data-stu-id="c15a8-250">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="c15a8-251">Zadejte absolutní cestu s <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory.</span><span class="sxs-lookup"><span data-stu-id="c15a8-251">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c15a8-252">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c15a8-252">Additional resources</span></span>

* <span data-ttu-id="c15a8-253">[Konfigurace koncového bodu kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (včetně konfigurace protokolu HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="c15a8-253">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
