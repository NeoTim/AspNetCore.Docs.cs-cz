---
title: ASP.NET Core hostitele ve službě systému Windows
author: rick-anderson
description: Naučte se hostovat aplikaci ASP.NET Core ve službě systému Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/windows-service
ms.openlocfilehash: af0c3836362233e41a79e72bd28b4a331e9763bc
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106478"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="d86f6-103">ASP.NET Core hostitele ve službě systému Windows</span><span class="sxs-lookup"><span data-stu-id="d86f6-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d86f6-104">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="d86f6-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d86f6-105">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="d86f6-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d86f6-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d86f6-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d86f6-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d86f6-107">Prerequisites</span></span>

* [<span data-ttu-id="d86f6-108">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d86f6-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d86f6-109">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d86f6-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="d86f6-110">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="d86f6-110">Worker Service template</span></span>

<span data-ttu-id="d86f6-111">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="d86f6-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="d86f6-112">Použití šablony jako základu pro aplikaci služby systému Windows:</span><span class="sxs-lookup"><span data-stu-id="d86f6-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="d86f6-113">Vytvořte aplikaci pracovní služby ze šablony .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d86f6-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="d86f6-114">Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d86f6-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="d86f6-115">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="d86f6-115">App configuration</span></span>

<span data-ttu-id="d86f6-116">Aplikace vyžaduje odkaz na balíček pro [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="d86f6-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="d86f6-117">`IHostBuilder.UseWindowsService`je volána při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="d86f6-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="d86f6-118">Pokud je aplikace spuštěná jako služba systému Windows, metoda:</span><span class="sxs-lookup"><span data-stu-id="d86f6-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="d86f6-119">Nastaví dobu života hostitele na `WindowsServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="d86f6-120">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="d86f6-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="d86f6-121">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="d86f6-122">Povolí protokolování do protokolu událostí:</span><span class="sxs-lookup"><span data-stu-id="d86f6-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="d86f6-123">Název aplikace se používá jako výchozí název zdroje.</span><span class="sxs-lookup"><span data-stu-id="d86f6-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="d86f6-124">Výchozí úroveň protokolování je *Upozornění* nebo vyšší pro aplikaci na základě šablony ASP.NET Core, která volá `CreateDefaultBuilder` k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="d86f6-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="d86f6-125">Přepište výchozí úroveň protokolu pomocí `Logging:EventLog:LogLevel:Default` klíče v souboru *appSettings. JSON* / *appSettings. { Environment}. JSON* nebo jiný poskytovatel konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="d86f6-126">Pouze správci mohou vytvářet nové zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="d86f6-127">Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="d86f6-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="d86f6-128">V `CreateHostBuilder` *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d86f6-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="d86f6-129">Toto téma doprovází následující ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="d86f6-130">Ukázka služby na pozadí Worker: Ukázka newebové aplikace založená na [šabloně pracovní služby pracovního procesu](#worker-service-template) , která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="d86f6-131">Ukázka Web App Service: Razor Webová aplikace stránky, která se spouští jako služba systému Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="d86f6-132">Pokyny pro MVC najdete v článcích v tématu <xref:mvc/overview> a <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="d86f6-133">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="d86f6-133">Deployment type</span></span>

<span data-ttu-id="d86f6-134">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d86f6-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d86f6-135">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="d86f6-135">SDK</span></span>

<span data-ttu-id="d86f6-136">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d86f6-137">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d86f6-138">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d86f6-139">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d86f6-140">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="d86f6-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d86f6-141">Pokud používáte [webovou sadu SDK](#sdk), soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci služby systému Windows zapotřebí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d86f6-142">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d86f6-143">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d86f6-144">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d86f6-145">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d86f6-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d86f6-146">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d86f6-147">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="d86f6-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d86f6-148">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="d86f6-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d86f6-149">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d86f6-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d86f6-150">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d86f6-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="d86f6-151">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-151">Service user account</span></span>

<span data-ttu-id="d86f6-152">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="d86f6-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d86f6-153">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="d86f6-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-154">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d86f6-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d86f6-155">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d86f6-156">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d86f6-157">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="d86f6-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d86f6-158">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d86f6-159">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d86f6-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d86f6-160">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-160">Log on as a service rights</span></span>

<span data-ttu-id="d86f6-161">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="d86f6-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d86f6-162">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d86f6-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d86f6-163">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d86f6-164">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d86f6-165">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d86f6-166">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d86f6-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d86f6-167">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="d86f6-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d86f6-168">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-168">Select **Advanced**.</span></span> <span data-ttu-id="d86f6-169">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-169">Select **Find Now**.</span></span> <span data-ttu-id="d86f6-170">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="d86f6-170">Select the user account from the list.</span></span> <span data-ttu-id="d86f6-171">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-171">Select **OK**.</span></span> <span data-ttu-id="d86f6-172">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="d86f6-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d86f6-173">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d86f6-174">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="d86f6-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d86f6-175">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-175">Create a service</span></span>

<span data-ttu-id="d86f6-176">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d86f6-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d86f6-177">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d86f6-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d86f6-178">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d86f6-179">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d86f6-180">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="d86f6-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d86f6-181">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d86f6-182">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d86f6-183">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d86f6-184">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="d86f6-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d86f6-185">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d86f6-186">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d86f6-187">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-187">Start a service</span></span>

<span data-ttu-id="d86f6-188">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-189">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="d86f6-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d86f6-190">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-190">Determine a service's status</span></span>

<span data-ttu-id="d86f6-191">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-192">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="d86f6-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d86f6-193">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-193">Stop a service</span></span>

<span data-ttu-id="d86f6-194">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d86f6-195">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-195">Remove a service</span></span>

<span data-ttu-id="d86f6-196">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d86f6-197">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d86f6-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d86f6-198">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d86f6-199">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d86f6-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d86f6-200">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="d86f6-200">Configure endpoints</span></span>

<span data-ttu-id="d86f6-201">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d86f6-202">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d86f6-203">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="d86f6-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d86f6-204">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d86f6-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d86f6-205">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d86f6-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d86f6-206">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="d86f6-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d86f6-207">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="d86f6-207">Current directory and content root</span></span>

<span data-ttu-id="d86f6-208">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d86f6-209">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="d86f6-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d86f6-210">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="d86f6-211">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="d86f6-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="d86f6-212">K vyhledání prostředků aplikace použijte [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="d86f6-213">Když aplikace běží jako služba, nastaví na <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="d86f6-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="d86f6-214">Soubory výchozích nastavení aplikace, *appSettings. JSON* a *appSettings. { Environment}. JSON*jsou načteny z kořenu obsahu aplikace voláním [CreateDefaultBuilder během vytváření hostitele](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="d86f6-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="d86f6-215">Pro jiné soubory nastavení načtené vývojářským kódem v nástroji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> není nutné volat <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="d86f6-216">V následujícím příkladu soubor *custom_settings. JSON* existuje v kořenu obsahu aplikace a je načtený bez explicitního nastavení základní cesty:</span><span class="sxs-lookup"><span data-stu-id="d86f6-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="d86f6-217">Nepokoušejte se použít <xref:System.IO.Directory.GetCurrentDirectory*> k získání cesty prostředku, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* jako svůj aktuální adresář.</span><span class="sxs-lookup"><span data-stu-id="d86f6-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d86f6-218">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="d86f6-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d86f6-219"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="d86f6-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d86f6-220">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="d86f6-220">Troubleshoot</span></span>

<span data-ttu-id="d86f6-221">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d86f6-222">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="d86f6-222">Common errors</span></span>

* <span data-ttu-id="d86f6-223">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="d86f6-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d86f6-224">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d86f6-225">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="d86f6-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d86f6-226">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="d86f6-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d86f6-227">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d86f6-228">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d86f6-229">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="d86f6-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d86f6-230">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="d86f6-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d86f6-231">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="d86f6-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d86f6-232">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d86f6-233">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d86f6-234">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d86f6-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d86f6-235">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="d86f6-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d86f6-236">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="d86f6-236">System and Application Event Logs</span></span>

<span data-ttu-id="d86f6-237">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d86f6-238">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d86f6-239">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d86f6-240">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d86f6-241">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d86f6-242">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="d86f6-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d86f6-243">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="d86f6-243">Run the app at a command prompt</span></span>

<span data-ttu-id="d86f6-244">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d86f6-245">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d86f6-246">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d86f6-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d86f6-247">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="d86f6-247">Clear package caches</span></span>

<span data-ttu-id="d86f6-248">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d86f6-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d86f6-249">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d86f6-250">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="d86f6-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d86f6-251">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d86f6-252">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="d86f6-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d86f6-253">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d86f6-254">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d86f6-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d86f6-255">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="d86f6-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d86f6-256">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="d86f6-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d86f6-257">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="d86f6-257">Slow or hanging app</span></span>

<span data-ttu-id="d86f6-258">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d86f6-259">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="d86f6-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="d86f6-260">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d86f6-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d86f6-261">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d86f6-262">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d86f6-263">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="d86f6-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d86f6-264">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d86f6-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d86f6-265">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d86f6-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d86f6-266">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d86f6-267">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="d86f6-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d86f6-268">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="d86f6-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d86f6-269">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="d86f6-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d86f6-270">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="d86f6-270">Analyze the dump</span></span>

<span data-ttu-id="d86f6-271">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="d86f6-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d86f6-272">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d86f6-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d86f6-273">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d86f6-273">Additional resources</span></span>

* <span data-ttu-id="d86f6-274">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="d86f6-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d86f6-275">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="d86f6-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d86f6-276">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="d86f6-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d86f6-277">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d86f6-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d86f6-278">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d86f6-278">Prerequisites</span></span>

* [<span data-ttu-id="d86f6-279">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d86f6-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d86f6-280">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d86f6-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d86f6-281">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="d86f6-281">App configuration</span></span>

<span data-ttu-id="d86f6-282">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="d86f6-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d86f6-283">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d86f6-284">Zkontrolujte, zda je ladicí program připojen nebo zda `--console` je k dispozici přepínač.</span><span class="sxs-lookup"><span data-stu-id="d86f6-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d86f6-285">Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d86f6-286">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="d86f6-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d86f6-287">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d86f6-288">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="d86f6-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d86f6-289">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d86f6-290">Tento krok se provádí před konfigurací aplikace v nástroji `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d86f6-291">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d86f6-292">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je odebrán z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="d86f6-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d86f6-293">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d86f6-294">Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d86f6-295">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro zpracování událostí životního cyklu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d86f6-296">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d86f6-297">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="d86f6-297">Deployment type</span></span>

<span data-ttu-id="d86f6-298">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d86f6-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d86f6-299">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="d86f6-299">SDK</span></span>

<span data-ttu-id="d86f6-300">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d86f6-301">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d86f6-302">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d86f6-303">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d86f6-304">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="d86f6-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d86f6-305">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="d86f6-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d86f6-306">V následujícím příkladu je identifikátor RID nastaven na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d86f6-307">`<SelfContained>`Vlastnost je nastavena na hodnotu `false` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d86f6-308">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor (*. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d86f6-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d86f6-309">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="d86f6-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d86f6-310">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d86f6-311">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d86f6-312">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d86f6-313">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d86f6-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d86f6-314">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d86f6-315">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="d86f6-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d86f6-316">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="d86f6-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d86f6-317">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d86f6-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d86f6-318">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d86f6-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d86f6-319">`<SelfContained>`Vlastnost je nastavena na hodnotu `true` :</span><span class="sxs-lookup"><span data-stu-id="d86f6-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d86f6-320">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-320">Service user account</span></span>

<span data-ttu-id="d86f6-321">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="d86f6-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d86f6-322">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="d86f6-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-323">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d86f6-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d86f6-324">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d86f6-325">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d86f6-326">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="d86f6-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d86f6-327">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d86f6-328">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d86f6-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d86f6-329">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-329">Log on as a service rights</span></span>

<span data-ttu-id="d86f6-330">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="d86f6-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d86f6-331">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d86f6-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d86f6-332">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d86f6-333">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d86f6-334">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d86f6-335">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d86f6-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d86f6-336">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="d86f6-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d86f6-337">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-337">Select **Advanced**.</span></span> <span data-ttu-id="d86f6-338">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-338">Select **Find Now**.</span></span> <span data-ttu-id="d86f6-339">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="d86f6-339">Select the user account from the list.</span></span> <span data-ttu-id="d86f6-340">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-340">Select **OK**.</span></span> <span data-ttu-id="d86f6-341">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="d86f6-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d86f6-342">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d86f6-343">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="d86f6-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d86f6-344">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-344">Create a service</span></span>

<span data-ttu-id="d86f6-345">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d86f6-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d86f6-346">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d86f6-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d86f6-347">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d86f6-348">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d86f6-349">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="d86f6-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d86f6-350">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d86f6-351">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d86f6-352">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d86f6-353">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="d86f6-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d86f6-354">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d86f6-355">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d86f6-356">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-356">Start a service</span></span>

<span data-ttu-id="d86f6-357">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-358">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="d86f6-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d86f6-359">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-359">Determine a service's status</span></span>

<span data-ttu-id="d86f6-360">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-361">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="d86f6-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d86f6-362">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-362">Stop a service</span></span>

<span data-ttu-id="d86f6-363">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d86f6-364">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-364">Remove a service</span></span>

<span data-ttu-id="d86f6-365">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d86f6-366">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="d86f6-366">Handle starting and stopping events</span></span>

<span data-ttu-id="d86f6-367">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> událostí, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="d86f6-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d86f6-368">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting` `OnStarted` `OnStopping` metodami, a:</span><span class="sxs-lookup"><span data-stu-id="d86f6-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d86f6-369">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která předá `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="d86f6-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d86f6-370">V `Program.Main` volejte `RunAsCustomService` metodu rozšíření místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="d86f6-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d86f6-371">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v nástroji `Program.Main` , podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d86f6-372">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d86f6-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d86f6-373">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d86f6-374">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d86f6-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d86f6-375">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="d86f6-375">Configure endpoints</span></span>

<span data-ttu-id="d86f6-376">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d86f6-377">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d86f6-378">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="d86f6-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d86f6-379">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d86f6-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d86f6-380">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d86f6-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d86f6-381">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="d86f6-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d86f6-382">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="d86f6-382">Current directory and content root</span></span>

<span data-ttu-id="d86f6-383">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d86f6-384">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="d86f6-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d86f6-385">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d86f6-386">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="d86f6-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d86f6-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d86f6-388">Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d86f6-389">V nástroji `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d86f6-390">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="d86f6-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d86f6-391"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="d86f6-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d86f6-392">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="d86f6-392">Troubleshoot</span></span>

<span data-ttu-id="d86f6-393">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d86f6-394">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="d86f6-394">Common errors</span></span>

* <span data-ttu-id="d86f6-395">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="d86f6-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d86f6-396">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d86f6-397">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="d86f6-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d86f6-398">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="d86f6-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d86f6-399">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d86f6-400">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d86f6-401">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="d86f6-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d86f6-402">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="d86f6-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d86f6-403">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="d86f6-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d86f6-404">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d86f6-405">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d86f6-406">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d86f6-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d86f6-407">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="d86f6-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d86f6-408">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="d86f6-408">System and Application Event Logs</span></span>

<span data-ttu-id="d86f6-409">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d86f6-410">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d86f6-411">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d86f6-412">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d86f6-413">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d86f6-414">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="d86f6-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d86f6-415">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="d86f6-415">Run the app at a command prompt</span></span>

<span data-ttu-id="d86f6-416">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d86f6-417">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d86f6-418">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d86f6-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d86f6-419">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="d86f6-419">Clear package caches</span></span>

<span data-ttu-id="d86f6-420">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d86f6-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d86f6-421">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d86f6-422">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="d86f6-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d86f6-423">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d86f6-424">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="d86f6-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d86f6-425">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d86f6-426">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d86f6-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d86f6-427">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="d86f6-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d86f6-428">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="d86f6-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d86f6-429">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="d86f6-429">Slow or hanging app</span></span>

<span data-ttu-id="d86f6-430">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d86f6-431">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="d86f6-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="d86f6-432">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d86f6-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d86f6-433">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d86f6-434">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d86f6-435">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="d86f6-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d86f6-436">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d86f6-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d86f6-437">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d86f6-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d86f6-438">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d86f6-439">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="d86f6-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d86f6-440">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="d86f6-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d86f6-441">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="d86f6-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d86f6-442">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="d86f6-442">Analyze the dump</span></span>

<span data-ttu-id="d86f6-443">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="d86f6-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d86f6-444">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d86f6-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d86f6-445">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d86f6-445">Additional resources</span></span>

* <span data-ttu-id="d86f6-446">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="d86f6-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d86f6-447">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="d86f6-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d86f6-448">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="d86f6-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d86f6-449">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d86f6-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d86f6-450">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d86f6-450">Prerequisites</span></span>

* [<span data-ttu-id="d86f6-451">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d86f6-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d86f6-452">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d86f6-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d86f6-453">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="d86f6-453">App configuration</span></span>

<span data-ttu-id="d86f6-454">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="d86f6-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d86f6-455">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d86f6-456">Zkontrolujte, zda je ladicí program připojen nebo zda `--console` je k dispozici přepínač.</span><span class="sxs-lookup"><span data-stu-id="d86f6-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d86f6-457">Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d86f6-458">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="d86f6-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d86f6-459">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d86f6-460">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="d86f6-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d86f6-461">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d86f6-462">Tento krok se provádí před konfigurací aplikace v nástroji `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d86f6-463">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d86f6-464">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je odebrán z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="d86f6-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d86f6-465">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d86f6-466">Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d86f6-467">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro zpracování událostí životního cyklu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d86f6-468">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d86f6-469">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="d86f6-469">Deployment type</span></span>

<span data-ttu-id="d86f6-470">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d86f6-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d86f6-471">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="d86f6-471">SDK</span></span>

<span data-ttu-id="d86f6-472">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d86f6-473">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d86f6-474">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d86f6-475">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d86f6-476">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="d86f6-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d86f6-477">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="d86f6-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d86f6-478">V následujícím příkladu je identifikátor RID nastaven na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d86f6-479">`<SelfContained>`Vlastnost je nastavena na hodnotu `false` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d86f6-480">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor (*. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d86f6-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d86f6-481">`<UseAppHost>`Vlastnost je nastavena na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="d86f6-482">Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.</span><span class="sxs-lookup"><span data-stu-id="d86f6-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="d86f6-483">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="d86f6-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d86f6-484">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d86f6-485">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d86f6-486">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d86f6-487">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d86f6-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d86f6-488">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="d86f6-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d86f6-489">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="d86f6-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d86f6-490">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="d86f6-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d86f6-491">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d86f6-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d86f6-492">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d86f6-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d86f6-493">`<SelfContained>`Vlastnost je nastavena na hodnotu `true` :</span><span class="sxs-lookup"><span data-stu-id="d86f6-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d86f6-494">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-494">Service user account</span></span>

<span data-ttu-id="d86f6-495">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="d86f6-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d86f6-496">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="d86f6-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-497">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d86f6-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d86f6-498">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d86f6-499">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d86f6-500">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="d86f6-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d86f6-501">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d86f6-502">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d86f6-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d86f6-503">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-503">Log on as a service rights</span></span>

<span data-ttu-id="d86f6-504">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="d86f6-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d86f6-505">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d86f6-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d86f6-506">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d86f6-507">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d86f6-508">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d86f6-509">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d86f6-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d86f6-510">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="d86f6-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d86f6-511">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-511">Select **Advanced**.</span></span> <span data-ttu-id="d86f6-512">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-512">Select **Find Now**.</span></span> <span data-ttu-id="d86f6-513">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="d86f6-513">Select the user account from the list.</span></span> <span data-ttu-id="d86f6-514">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d86f6-514">Select **OK**.</span></span> <span data-ttu-id="d86f6-515">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="d86f6-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d86f6-516">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d86f6-517">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="d86f6-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d86f6-518">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-518">Create a service</span></span>

<span data-ttu-id="d86f6-519">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d86f6-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d86f6-520">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d86f6-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d86f6-521">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d86f6-522">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d86f6-523">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="d86f6-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d86f6-524">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d86f6-525">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d86f6-526">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d86f6-527">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="d86f6-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d86f6-528">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d86f6-529">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d86f6-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d86f6-530">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-530">Start a service</span></span>

<span data-ttu-id="d86f6-531">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-532">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="d86f6-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d86f6-533">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-533">Determine a service's status</span></span>

<span data-ttu-id="d86f6-534">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d86f6-535">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="d86f6-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d86f6-536">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-536">Stop a service</span></span>

<span data-ttu-id="d86f6-537">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d86f6-538">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="d86f6-538">Remove a service</span></span>

<span data-ttu-id="d86f6-539">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d86f6-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d86f6-540">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="d86f6-540">Handle starting and stopping events</span></span>

<span data-ttu-id="d86f6-541">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> událostí, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="d86f6-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d86f6-542">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting` `OnStarted` `OnStopping` metodami, a:</span><span class="sxs-lookup"><span data-stu-id="d86f6-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d86f6-543">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která předá `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="d86f6-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d86f6-544">V `Program.Main` volejte `RunAsCustomService` metodu rozšíření místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="d86f6-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d86f6-545">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v nástroji `Program.Main` , podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d86f6-546">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d86f6-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d86f6-547">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d86f6-548">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d86f6-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d86f6-549">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="d86f6-549">Configure endpoints</span></span>

<span data-ttu-id="d86f6-550">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d86f6-551">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d86f6-552">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="d86f6-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d86f6-553">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d86f6-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d86f6-554">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d86f6-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d86f6-555">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="d86f6-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d86f6-556">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="d86f6-556">Current directory and content root</span></span>

<span data-ttu-id="d86f6-557">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d86f6-558">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="d86f6-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d86f6-559">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d86f6-560">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="d86f6-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d86f6-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="d86f6-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d86f6-562">Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d86f6-563">V nástroji `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d86f6-564">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="d86f6-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d86f6-565"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="d86f6-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d86f6-566">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="d86f6-566">Troubleshoot</span></span>

<span data-ttu-id="d86f6-567">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d86f6-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d86f6-568">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="d86f6-568">Common errors</span></span>

* <span data-ttu-id="d86f6-569">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="d86f6-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d86f6-570">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d86f6-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d86f6-571">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="d86f6-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d86f6-572">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="d86f6-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d86f6-573">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d86f6-574">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d86f6-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d86f6-575">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="d86f6-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d86f6-576">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="d86f6-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d86f6-577">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="d86f6-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d86f6-578">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d86f6-579">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d86f6-580">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d86f6-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d86f6-581">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="d86f6-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d86f6-582">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="d86f6-582">System and Application Event Logs</span></span>

<span data-ttu-id="d86f6-583">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d86f6-584">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d86f6-585">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="d86f6-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d86f6-586">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d86f6-587">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d86f6-588">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="d86f6-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d86f6-589">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="d86f6-589">Run the app at a command prompt</span></span>

<span data-ttu-id="d86f6-590">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="d86f6-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d86f6-591">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d86f6-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d86f6-592">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d86f6-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d86f6-593">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="d86f6-593">Clear package caches</span></span>

<span data-ttu-id="d86f6-594">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d86f6-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d86f6-595">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d86f6-596">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="d86f6-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d86f6-597">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="d86f6-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d86f6-598">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="d86f6-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d86f6-599">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d86f6-600">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d86f6-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d86f6-601">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="d86f6-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d86f6-602">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="d86f6-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d86f6-603">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="d86f6-603">Slow or hanging app</span></span>

<span data-ttu-id="d86f6-604">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="d86f6-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d86f6-605">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="d86f6-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="d86f6-606">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d86f6-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d86f6-607">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="d86f6-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d86f6-608">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="d86f6-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d86f6-609">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="d86f6-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d86f6-610">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d86f6-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d86f6-611">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d86f6-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d86f6-612">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d86f6-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d86f6-613">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="d86f6-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d86f6-614">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="d86f6-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d86f6-615">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="d86f6-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d86f6-616">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="d86f6-616">Analyze the dump</span></span>

<span data-ttu-id="d86f6-617">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="d86f6-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d86f6-618">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d86f6-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d86f6-619">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d86f6-619">Additional resources</span></span>

* <span data-ttu-id="d86f6-620">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="d86f6-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
