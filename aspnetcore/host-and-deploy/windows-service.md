---
title: ASP.NET Core hostitele ve službě systému Windows
author: rick-anderson
description: Naučte se hostovat aplikaci ASP.NET Core ve službě systému Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/windows-service
ms.openlocfilehash: 7740774cad33418489fc1d94240574167f84fae6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015358"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="6ebe4-103">ASP.NET Core hostitele ve službě systému Windows</span><span class="sxs-lookup"><span data-stu-id="6ebe4-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ebe4-104">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="6ebe4-105">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="6ebe4-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ebe4-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ebe4-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6ebe4-107">Prerequisites</span></span>

* [<span data-ttu-id="6ebe4-108">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ebe4-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="6ebe4-109">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ebe4-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="6ebe4-110">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="6ebe4-110">Worker Service template</span></span>

<span data-ttu-id="6ebe4-111">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="6ebe4-112">Použití šablony jako základu pro aplikaci služby systému Windows:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="6ebe4-113">Vytvořte aplikaci pracovní služby ze šablony .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="6ebe4-114">Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="6ebe4-115">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="6ebe4-115">App configuration</span></span>

<span data-ttu-id="6ebe4-116">Aplikace vyžaduje odkaz na balíček pro [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="6ebe4-117">`IHostBuilder.UseWindowsService`je volána při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="6ebe4-118">Pokud je aplikace spuštěná jako služba systému Windows, metoda:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="6ebe4-119">Nastaví dobu života hostitele na `WindowsServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="6ebe4-120">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="6ebe4-121">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="6ebe4-122">Povolí protokolování do protokolu událostí:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="6ebe4-123">Název aplikace se používá jako výchozí název zdroje.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="6ebe4-124">Výchozí úroveň protokolování je *Upozornění* nebo vyšší pro aplikaci na základě šablony ASP.NET Core, která volá `CreateDefaultBuilder` k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="6ebe4-125">Přepište výchozí úroveň protokolu `Logging:EventLog:LogLevel:Default` klíčem v *appsettings.jsv* / *appSettings. { Environment}. JSON* nebo jiný poskytovatel konfigurace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="6ebe4-126">Pouze správci mohou vytvářet nové zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="6ebe4-127">Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="6ebe4-128">V `CreateHostBuilder` *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="6ebe4-129">Toto téma doprovází následující ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="6ebe4-130">Ukázka služby na pozadí Worker: Ukázka newebové aplikace založená na [šabloně pracovní služby pracovního procesu](#worker-service-template) , která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="6ebe4-131">Ukázka Web App Service: Razor Webová aplikace stránky, která se spouští jako služba systému Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="6ebe4-132">Pokyny pro MVC najdete v článcích v tématu <xref:mvc/overview> a <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="6ebe4-133">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="6ebe4-133">Deployment type</span></span>

<span data-ttu-id="6ebe4-134">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="6ebe4-135">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="6ebe4-135">SDK</span></span>

<span data-ttu-id="6ebe4-136">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="6ebe4-137">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="6ebe4-138">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="6ebe4-139">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="6ebe4-140">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="6ebe4-141">Pokud používáte [webovou sadu SDK](#sdk), soubor *web.config* , který se obvykle vyrábí při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="6ebe4-142">Chcete-li zakázat vytváření souboru *web.config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="6ebe4-143">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="6ebe4-144">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="6ebe4-145">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="6ebe4-146">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="6ebe4-147">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="6ebe4-148">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="6ebe4-149">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="6ebe4-150">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="6ebe4-151">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-151">Service user account</span></span>

<span data-ttu-id="6ebe4-152">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="6ebe4-153">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-154">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="6ebe4-155">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="6ebe4-156">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="6ebe4-157">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="6ebe4-158">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="6ebe4-159">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="6ebe4-160">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-160">Log on as a service rights</span></span>

<span data-ttu-id="6ebe4-161">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="6ebe4-162">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="6ebe4-163">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="6ebe4-164">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="6ebe4-165">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="6ebe4-166">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="6ebe4-167">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="6ebe4-168">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-168">Select **Advanced**.</span></span> <span data-ttu-id="6ebe4-169">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-169">Select **Find Now**.</span></span> <span data-ttu-id="6ebe4-170">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-170">Select the user account from the list.</span></span> <span data-ttu-id="6ebe4-171">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-171">Select **OK**.</span></span> <span data-ttu-id="6ebe4-172">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="6ebe4-173">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="6ebe4-174">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="6ebe4-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="6ebe4-175">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-175">Create a service</span></span>

<span data-ttu-id="6ebe4-176">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="6ebe4-177">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="6ebe4-178">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="6ebe4-179">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="6ebe4-180">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="6ebe4-181">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="6ebe4-182">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="6ebe4-183">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="6ebe4-184">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="6ebe4-185">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="6ebe4-186">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="6ebe4-187">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-187">Start a service</span></span>

<span data-ttu-id="6ebe4-188">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-189">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="6ebe4-190">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-190">Determine a service's status</span></span>

<span data-ttu-id="6ebe4-191">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-192">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="6ebe4-193">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-193">Stop a service</span></span>

<span data-ttu-id="6ebe4-194">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="6ebe4-195">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-195">Remove a service</span></span>

<span data-ttu-id="6ebe4-196">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6ebe4-197">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="6ebe4-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6ebe4-198">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="6ebe4-199">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="6ebe4-200">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="6ebe4-200">Configure endpoints</span></span>

<span data-ttu-id="6ebe4-201">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="6ebe4-202">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="6ebe4-203">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="6ebe4-204">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="6ebe4-205">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="6ebe4-206">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="6ebe4-207">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="6ebe4-207">Current directory and content root</span></span>

<span data-ttu-id="6ebe4-208">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="6ebe4-209">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="6ebe4-210">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="6ebe4-211">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="6ebe4-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="6ebe4-212">K vyhledání prostředků aplikace použijte [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="6ebe4-213">Když aplikace běží jako služba, nastaví na <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="6ebe4-214">Soubory výchozích nastavení aplikace *appsettings.jsv* a *appSettings. { Environment}. JSON*jsou načteny z kořenu obsahu aplikace voláním [CreateDefaultBuilder během vytváření hostitele](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="6ebe4-215">Pro jiné soubory nastavení načtené vývojářským kódem v nástroji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> není nutné volat <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="6ebe4-216">V následujícím příkladu *custom_settings.jsv* souboru existuje v kořenu obsahu aplikace a je načten bez explicitního nastavení základní cesty:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="6ebe4-217">Nepokoušejte se použít <xref:System.IO.Directory.GetCurrentDirectory*> k získání cesty prostředku, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* jako svůj aktuální adresář.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="6ebe4-218">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="6ebe4-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="6ebe4-219"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6ebe4-220">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="6ebe4-220">Troubleshoot</span></span>

<span data-ttu-id="6ebe4-221">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="6ebe4-222">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-222">Common errors</span></span>

* <span data-ttu-id="6ebe4-223">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="6ebe4-224">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="6ebe4-225">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="6ebe4-226">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="6ebe4-227">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="6ebe4-228">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="6ebe4-229">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="6ebe4-230">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="6ebe4-231">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="6ebe4-232">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="6ebe4-233">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="6ebe4-234">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="6ebe4-235">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="6ebe4-236">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="6ebe4-236">System and Application Event Logs</span></span>

<span data-ttu-id="6ebe4-237">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="6ebe4-238">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="6ebe4-239">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="6ebe4-240">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="6ebe4-241">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="6ebe4-242">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="6ebe4-243">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="6ebe4-243">Run the app at a command prompt</span></span>

<span data-ttu-id="6ebe4-244">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="6ebe4-245">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="6ebe4-246">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="6ebe4-247">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="6ebe4-247">Clear package caches</span></span>

<span data-ttu-id="6ebe4-248">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="6ebe4-249">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="6ebe4-250">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="6ebe4-251">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="6ebe4-252">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="6ebe4-253">Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="6ebe4-254">*nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="6ebe4-255">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="6ebe4-256">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="6ebe4-257">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="6ebe4-257">Slow or hanging app</span></span>

<span data-ttu-id="6ebe4-258">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="6ebe4-259">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="6ebe4-260">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="6ebe4-261">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="6ebe4-262">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="6ebe4-263">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="6ebe4-264">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="6ebe4-265">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="6ebe4-266">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="6ebe4-267">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="6ebe4-268">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="6ebe4-269">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="6ebe4-270">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="6ebe4-270">Analyze the dump</span></span>

<span data-ttu-id="6ebe4-271">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="6ebe4-272">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ebe4-273">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6ebe4-273">Additional resources</span></span>

* <span data-ttu-id="6ebe4-274">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6ebe4-275">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="6ebe4-276">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="6ebe4-277">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ebe4-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ebe4-278">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6ebe4-278">Prerequisites</span></span>

* [<span data-ttu-id="6ebe4-279">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ebe4-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="6ebe4-280">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ebe4-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="6ebe4-281">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="6ebe4-281">App configuration</span></span>

<span data-ttu-id="6ebe4-282">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="6ebe4-283">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="6ebe4-284">Zkontrolujte, zda je ladicí program připojen nebo zda `--console` je k dispozici přepínač.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="6ebe4-285">Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="6ebe4-286">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="6ebe4-287">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="6ebe4-288">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="6ebe4-289">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="6ebe4-290">Tento krok se provádí před konfigurací aplikace v nástroji `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="6ebe4-291">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="6ebe4-292">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je odebrán z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="6ebe4-293">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="6ebe4-294">Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appsettings.Production.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="6ebe4-295">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro zpracování událostí životního cyklu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="6ebe4-296">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="6ebe4-297">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="6ebe4-297">Deployment type</span></span>

<span data-ttu-id="6ebe4-298">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="6ebe4-299">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="6ebe4-299">SDK</span></span>

<span data-ttu-id="6ebe4-300">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="6ebe4-301">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="6ebe4-302">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="6ebe4-303">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="6ebe4-304">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="6ebe4-305">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="6ebe4-306">V následujícím příkladu je identifikátor RID nastaven na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="6ebe4-307">Vlastnost `<SelfContained>` je nastavena na hodnotu `false`.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="6ebe4-308">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor (*. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="6ebe4-309">Soubor *web.config* , který se obvykle vyrábí při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="6ebe4-310">Chcete-li zakázat vytváření souboru *web.config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="6ebe4-311">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="6ebe4-312">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="6ebe4-313">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="6ebe4-314">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="6ebe4-315">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="6ebe4-316">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="6ebe4-317">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="6ebe4-318">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="6ebe4-319">`<SelfContained>`Vlastnost je nastavena na hodnotu `true` :</span><span class="sxs-lookup"><span data-stu-id="6ebe4-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="6ebe4-320">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-320">Service user account</span></span>

<span data-ttu-id="6ebe4-321">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="6ebe4-322">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-323">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="6ebe4-324">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="6ebe4-325">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="6ebe4-326">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="6ebe4-327">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="6ebe4-328">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="6ebe4-329">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-329">Log on as a service rights</span></span>

<span data-ttu-id="6ebe4-330">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="6ebe4-331">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="6ebe4-332">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="6ebe4-333">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="6ebe4-334">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="6ebe4-335">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="6ebe4-336">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="6ebe4-337">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-337">Select **Advanced**.</span></span> <span data-ttu-id="6ebe4-338">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-338">Select **Find Now**.</span></span> <span data-ttu-id="6ebe4-339">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-339">Select the user account from the list.</span></span> <span data-ttu-id="6ebe4-340">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-340">Select **OK**.</span></span> <span data-ttu-id="6ebe4-341">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="6ebe4-342">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="6ebe4-343">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="6ebe4-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="6ebe4-344">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-344">Create a service</span></span>

<span data-ttu-id="6ebe4-345">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="6ebe4-346">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="6ebe4-347">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="6ebe4-348">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="6ebe4-349">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="6ebe4-350">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="6ebe4-351">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="6ebe4-352">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="6ebe4-353">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="6ebe4-354">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="6ebe4-355">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="6ebe4-356">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-356">Start a service</span></span>

<span data-ttu-id="6ebe4-357">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-358">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="6ebe4-359">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-359">Determine a service's status</span></span>

<span data-ttu-id="6ebe4-360">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-361">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="6ebe4-362">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-362">Stop a service</span></span>

<span data-ttu-id="6ebe4-363">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="6ebe4-364">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-364">Remove a service</span></span>

<span data-ttu-id="6ebe4-365">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="6ebe4-366">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="6ebe4-366">Handle starting and stopping events</span></span>

<span data-ttu-id="6ebe4-367">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> událostí, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="6ebe4-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="6ebe4-368">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting` `OnStarted` `OnStopping` metodami, a:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="6ebe4-369">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která předá `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="6ebe4-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="6ebe4-370">V `Program.Main` volejte `RunAsCustomService` metodu rozšíření místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="6ebe4-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="6ebe4-371">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v nástroji `Program.Main` , podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6ebe4-372">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="6ebe4-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6ebe4-373">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="6ebe4-374">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="6ebe4-375">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="6ebe4-375">Configure endpoints</span></span>

<span data-ttu-id="6ebe4-376">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="6ebe4-377">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="6ebe4-378">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="6ebe4-379">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="6ebe4-380">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="6ebe4-381">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="6ebe4-382">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="6ebe4-382">Current directory and content root</span></span>

<span data-ttu-id="6ebe4-383">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="6ebe4-384">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="6ebe4-385">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="6ebe4-386">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="6ebe4-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="6ebe4-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="6ebe4-388">Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="6ebe4-389">V nástroji `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="6ebe4-390">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="6ebe4-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="6ebe4-391"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6ebe4-392">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="6ebe4-392">Troubleshoot</span></span>

<span data-ttu-id="6ebe4-393">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="6ebe4-394">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-394">Common errors</span></span>

* <span data-ttu-id="6ebe4-395">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="6ebe4-396">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="6ebe4-397">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="6ebe4-398">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="6ebe4-399">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="6ebe4-400">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="6ebe4-401">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="6ebe4-402">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="6ebe4-403">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="6ebe4-404">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="6ebe4-405">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="6ebe4-406">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="6ebe4-407">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="6ebe4-408">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="6ebe4-408">System and Application Event Logs</span></span>

<span data-ttu-id="6ebe4-409">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="6ebe4-410">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="6ebe4-411">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="6ebe4-412">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="6ebe4-413">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="6ebe4-414">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="6ebe4-415">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="6ebe4-415">Run the app at a command prompt</span></span>

<span data-ttu-id="6ebe4-416">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="6ebe4-417">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="6ebe4-418">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="6ebe4-419">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="6ebe4-419">Clear package caches</span></span>

<span data-ttu-id="6ebe4-420">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="6ebe4-421">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="6ebe4-422">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="6ebe4-423">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="6ebe4-424">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="6ebe4-425">Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="6ebe4-426">*nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="6ebe4-427">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="6ebe4-428">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="6ebe4-429">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="6ebe4-429">Slow or hanging app</span></span>

<span data-ttu-id="6ebe4-430">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="6ebe4-431">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="6ebe4-432">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="6ebe4-433">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="6ebe4-434">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="6ebe4-435">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="6ebe4-436">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="6ebe4-437">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="6ebe4-438">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="6ebe4-439">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="6ebe4-440">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="6ebe4-441">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="6ebe4-442">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="6ebe4-442">Analyze the dump</span></span>

<span data-ttu-id="6ebe4-443">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="6ebe4-444">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ebe4-445">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6ebe4-445">Additional resources</span></span>

* <span data-ttu-id="6ebe4-446">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6ebe4-447">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="6ebe4-448">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="6ebe4-449">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ebe4-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ebe4-450">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6ebe4-450">Prerequisites</span></span>

* [<span data-ttu-id="6ebe4-451">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ebe4-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="6ebe4-452">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6ebe4-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="6ebe4-453">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="6ebe4-453">App configuration</span></span>

<span data-ttu-id="6ebe4-454">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="6ebe4-455">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="6ebe4-456">Zkontrolujte, zda je ladicí program připojen nebo zda `--console` je k dispozici přepínač.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="6ebe4-457">Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="6ebe4-458">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="6ebe4-459">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="6ebe4-460">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="6ebe4-461">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="6ebe4-462">Tento krok se provádí před konfigurací aplikace v nástroji `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="6ebe4-463">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="6ebe4-464">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je odebrán z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="6ebe4-465">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="6ebe4-466">Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appsettings.Production.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="6ebe4-467">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro zpracování událostí životního cyklu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="6ebe4-468">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="6ebe4-469">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="6ebe4-469">Deployment type</span></span>

<span data-ttu-id="6ebe4-470">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="6ebe4-471">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="6ebe4-471">SDK</span></span>

<span data-ttu-id="6ebe4-472">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="6ebe4-473">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="6ebe4-474">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="6ebe4-475">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="6ebe4-476">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="6ebe4-477">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="6ebe4-478">V následujícím příkladu je identifikátor RID nastaven na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="6ebe4-479">Vlastnost `<SelfContained>` je nastavena na hodnotu `false`.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="6ebe4-480">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor (*. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="6ebe4-481">Vlastnost `<UseAppHost>` je nastavena na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="6ebe4-482">Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="6ebe4-483">Soubor *web.config* , který se obvykle vyrábí při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="6ebe4-484">Chcete-li zakázat vytváření souboru *web.config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="6ebe4-485">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="6ebe4-486">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="6ebe4-487">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="6ebe4-488">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="6ebe4-489">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="6ebe4-490">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="6ebe4-491">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="6ebe4-492">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="6ebe4-493">`<SelfContained>`Vlastnost je nastavena na hodnotu `true` :</span><span class="sxs-lookup"><span data-stu-id="6ebe4-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="6ebe4-494">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-494">Service user account</span></span>

<span data-ttu-id="6ebe4-495">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="6ebe4-496">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-497">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="6ebe4-498">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="6ebe4-499">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="6ebe4-500">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="6ebe4-501">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="6ebe4-502">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="6ebe4-503">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-503">Log on as a service rights</span></span>

<span data-ttu-id="6ebe4-504">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="6ebe4-505">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="6ebe4-506">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="6ebe4-507">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="6ebe4-508">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="6ebe4-509">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="6ebe4-510">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="6ebe4-511">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-511">Select **Advanced**.</span></span> <span data-ttu-id="6ebe4-512">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-512">Select **Find Now**.</span></span> <span data-ttu-id="6ebe4-513">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-513">Select the user account from the list.</span></span> <span data-ttu-id="6ebe4-514">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-514">Select **OK**.</span></span> <span data-ttu-id="6ebe4-515">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="6ebe4-516">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="6ebe4-517">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="6ebe4-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="6ebe4-518">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-518">Create a service</span></span>

<span data-ttu-id="6ebe4-519">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="6ebe4-520">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="6ebe4-521">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="6ebe4-522">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="6ebe4-523">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="6ebe4-524">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="6ebe4-525">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="6ebe4-526">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="6ebe4-527">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="6ebe4-528">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="6ebe4-529">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="6ebe4-530">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-530">Start a service</span></span>

<span data-ttu-id="6ebe4-531">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-532">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="6ebe4-533">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-533">Determine a service's status</span></span>

<span data-ttu-id="6ebe4-534">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="6ebe4-535">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="6ebe4-536">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-536">Stop a service</span></span>

<span data-ttu-id="6ebe4-537">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="6ebe4-538">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-538">Remove a service</span></span>

<span data-ttu-id="6ebe4-539">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="6ebe4-540">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="6ebe4-540">Handle starting and stopping events</span></span>

<span data-ttu-id="6ebe4-541">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> událostí, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="6ebe4-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="6ebe4-542">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting` `OnStarted` `OnStopping` metodami, a:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="6ebe4-543">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která předá `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="6ebe4-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="6ebe4-544">V `Program.Main` volejte `RunAsCustomService` metodu rozšíření místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="6ebe4-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="6ebe4-545">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v nástroji `Program.Main` , podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6ebe4-546">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="6ebe4-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6ebe4-547">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="6ebe4-548">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="6ebe4-549">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="6ebe4-549">Configure endpoints</span></span>

<span data-ttu-id="6ebe4-550">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="6ebe4-551">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="6ebe4-552">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="6ebe4-553">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="6ebe4-554">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="6ebe4-555">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="6ebe4-556">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="6ebe4-556">Current directory and content root</span></span>

<span data-ttu-id="6ebe4-557">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="6ebe4-558">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="6ebe4-559">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="6ebe4-560">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="6ebe4-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="6ebe4-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="6ebe4-562">Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="6ebe4-563">V nástroji `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="6ebe4-564">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="6ebe4-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="6ebe4-565"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6ebe4-566">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="6ebe4-566">Troubleshoot</span></span>

<span data-ttu-id="6ebe4-567">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="6ebe4-568">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="6ebe4-568">Common errors</span></span>

* <span data-ttu-id="6ebe4-569">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="6ebe4-570">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="6ebe4-571">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="6ebe4-572">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="6ebe4-573">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="6ebe4-574">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="6ebe4-575">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="6ebe4-576">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="6ebe4-577">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="6ebe4-578">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="6ebe4-579">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="6ebe4-580">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="6ebe4-581">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="6ebe4-582">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="6ebe4-582">System and Application Event Logs</span></span>

<span data-ttu-id="6ebe4-583">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="6ebe4-584">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="6ebe4-585">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="6ebe4-586">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="6ebe4-587">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="6ebe4-588">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="6ebe4-589">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="6ebe4-589">Run the app at a command prompt</span></span>

<span data-ttu-id="6ebe4-590">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="6ebe4-591">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="6ebe4-592">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="6ebe4-593">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="6ebe4-593">Clear package caches</span></span>

<span data-ttu-id="6ebe4-594">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="6ebe4-595">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="6ebe4-596">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="6ebe4-597">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="6ebe4-598">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="6ebe4-599">Mazání mezipamětí balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a provedení příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="6ebe4-600">*nuget.exe* není sada odinstalována s desktopovým operačním systémem Windows a je nutné ji získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="6ebe4-601">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="6ebe4-602">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="6ebe4-603">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="6ebe4-603">Slow or hanging app</span></span>

<span data-ttu-id="6ebe4-604">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="6ebe4-605">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="6ebe4-606">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="6ebe4-607">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="6ebe4-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="6ebe4-608">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="6ebe4-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="6ebe4-609">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="6ebe4-610">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="6ebe4-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="6ebe4-611">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="6ebe4-612">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="6ebe4-613">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="6ebe4-614">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="6ebe4-615">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="6ebe4-616">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="6ebe4-616">Analyze the dump</span></span>

<span data-ttu-id="6ebe4-617">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="6ebe4-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="6ebe4-618">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="6ebe4-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ebe4-619">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6ebe4-619">Additional resources</span></span>

* <span data-ttu-id="6ebe4-620">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="6ebe4-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
