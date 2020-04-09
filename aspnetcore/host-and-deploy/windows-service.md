---
title: Hostování ASP.NET jádra ve službě Windows
author: rick-anderson
description: Přečtěte si, jak ve službě Windows service hostovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417576"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="9b88e-103">Hostování ASP.NET jádra ve službě Windows</span><span class="sxs-lookup"><span data-stu-id="9b88e-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b88e-104">Aplikaci ASP.NET Core lze hostovat ve Windows jako [službu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9b88e-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="9b88e-105">Při hostování jako služba Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="9b88e-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="9b88e-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9b88e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b88e-107">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9b88e-107">Prerequisites</span></span>

* [<span data-ttu-id="9b88e-108">ASP.NET jádra SDK 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="9b88e-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="9b88e-109">PowerShell 6.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="9b88e-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="9b88e-110">Šablona služby pracovního procesu</span><span class="sxs-lookup"><span data-stu-id="9b88e-110">Worker Service template</span></span>

<span data-ttu-id="9b88e-111">Šablona ASP.NET core worker service poskytuje výchozí bod pro zápis dlouho běžících aplikací služby.</span><span class="sxs-lookup"><span data-stu-id="9b88e-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="9b88e-112">Použití šablony jako základu pro aplikaci služby Windows:</span><span class="sxs-lookup"><span data-stu-id="9b88e-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="9b88e-113">Vytvořte aplikaci pracovní služby ze šablony .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b88e-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="9b88e-114">Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci Pracovní služba tak, aby mohla fungovat jako služba Windows.</span><span class="sxs-lookup"><span data-stu-id="9b88e-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="9b88e-115">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="9b88e-115">App configuration</span></span>

<span data-ttu-id="9b88e-116">Aplikace vyžaduje odkaz na balíček pro [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="9b88e-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="9b88e-117">`IHostBuilder.UseWindowsService`se nazývá při vytváření hostitele.</span><span class="sxs-lookup"><span data-stu-id="9b88e-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="9b88e-118">Pokud je aplikace spuštěná jako služba Windows, metoda:</span><span class="sxs-lookup"><span data-stu-id="9b88e-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="9b88e-119">Nastaví životnost `WindowsServiceLifetime`hostitele na .</span><span class="sxs-lookup"><span data-stu-id="9b88e-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="9b88e-120">Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="9b88e-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="9b88e-121">Další informace naleznete v části [Aktuální adresář a kořenový obsah.](#current-directory-and-content-root)</span><span class="sxs-lookup"><span data-stu-id="9b88e-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="9b88e-122">Umožňuje protokolování do protokolu událostí:</span><span class="sxs-lookup"><span data-stu-id="9b88e-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="9b88e-123">Název aplikace se používá jako výchozí zdrojový název.</span><span class="sxs-lookup"><span data-stu-id="9b88e-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="9b88e-124">Výchozí úroveň protokolu je *Upozornění* nebo vyšší pro aplikaci `CreateDefaultBuilder` založenou na šabloně ASP.NET Core, která volá k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="9b88e-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="9b88e-125">Přepište výchozí úroveň protokolu `Logging:EventLog:LogLevel:Default` pomocí klíče v nastavení aplikace *appsettings.json*/*appsettings.{ Prostředí}.json* nebo jiný zprostředkovatel konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="9b88e-126">Nové zdroje událostí mohou vytvářet pouze správci.</span><span class="sxs-lookup"><span data-stu-id="9b88e-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="9b88e-127">Pokud nelze vytvořit zdroj událostí pomocí názvu aplikace, je do zdroje *aplikace* zaznamenáno upozornění a protokoly událostí jsou zakázány.</span><span class="sxs-lookup"><span data-stu-id="9b88e-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="9b88e-128">V `CreateHostBuilder` *Program.cs*Program.cs :</span><span class="sxs-lookup"><span data-stu-id="9b88e-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="9b88e-129">K tomuto tématu se přivázala následující ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="9b88e-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="9b88e-130">Ukázka &ndash; pracovní služby na pozadí Ukázka newebové aplikace založená na [šabloně pracovní služby,](#worker-service-template) která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="9b88e-131">Ukázka ukázkové &ndash; ukázky webové aplikace Razor Pages, která běží jako služba Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="9b88e-132">Pokyny k MVC naleznete <xref:mvc/overview> v <xref:migration/22-to-30>článcích pod a .</span><span class="sxs-lookup"><span data-stu-id="9b88e-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="9b88e-133">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="9b88e-133">Deployment type</span></span>

<span data-ttu-id="9b88e-134">Informace a rady ohledně scénářů nasazení naleznete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="9b88e-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="9b88e-135">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="9b88e-135">SDK</span></span>

<span data-ttu-id="9b88e-136">Pro službu založenou na webových aplikacích, která používá razor pages nebo mvc architektury, zadejte web ovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="9b88e-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="9b88e-137">Pokud služba provádí pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte pracovní sdk v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="9b88e-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="9b88e-138">Nasazení závislé na rámci (FDD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="9b88e-139">Nasazení závislé na rozhraní (FDD) závisí na přítomnosti sdílené verze rozhraní .NET Core pro celý systém v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="9b88e-140">Při přijetí scénáře FDD podle pokynů v tomto článku sada SDK vytvoří spustitelný soubor (*.exe*), nazývaný *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="9b88e-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="9b88e-141">Pokud používáte [webovou sadku SDK](#sdk), není pro aplikaci služeb windows nutný soubor *web.config,* který se obvykle vytváří při publikování aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b88e-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="9b88e-142">Chcete-li zakázat vytváření souboru *web.config,* přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na . `true`</span><span class="sxs-lookup"><span data-stu-id="9b88e-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="9b88e-143">Samostatné nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="9b88e-144">Samostatné nasazení (SCD) nespoléhá na přítomnost sdíleného rozhraní v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="9b88e-145">Runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="9b88e-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="9b88e-146">Identifikátor [rid (Windows Runtime)](/dotnet/core/rid-catalog) je `<PropertyGroup>` součástí cílového rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9b88e-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="9b88e-147">Publikování pro více idů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="9b88e-148">Zadejte ridy v seznamu odděleného středníkem.</span><span class="sxs-lookup"><span data-stu-id="9b88e-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="9b88e-149">Použijte název [ \<vlastnosti RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="9b88e-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="9b88e-150">Další informace naleznete v tématu [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="9b88e-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="9b88e-151">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-151">Service user account</span></span>

<span data-ttu-id="9b88e-152">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShell 6 pro správu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="9b88e-153">V aktualizaci Windows 10 říjen 2018 (verze 1809/build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="9b88e-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-154">V os windows starší než Windows 10 říjen 2018 Update (verze 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="9b88e-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="9b88e-155">Po zobrazení výzvy zadejte [silné heslo.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)</span><span class="sxs-lookup"><span data-stu-id="9b88e-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="9b88e-156">Pokud `-AccountExpires` parametr není dodán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s <xref:System.DateTime>vypršením platnosti , účet nevyprší.</span><span class="sxs-lookup"><span data-stu-id="9b88e-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="9b88e-157">Další informace naleznete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="9b88e-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="9b88e-158">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravovaných služeb.</span><span class="sxs-lookup"><span data-stu-id="9b88e-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="9b88e-159">Další informace naleznete v [tématu Přehled účtů spravovaných služeb skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="9b88e-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="9b88e-160">Přihlásit se jako servisní práva</span><span class="sxs-lookup"><span data-stu-id="9b88e-160">Log on as a service rights</span></span>

<span data-ttu-id="9b88e-161">Chcete-li vytvořit *přihlásit jako* práva služby pro účet uživatele služby:</span><span class="sxs-lookup"><span data-stu-id="9b88e-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="9b88e-162">Spuštěním souboru *secpol.msc*otevřete editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9b88e-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="9b88e-163">Rozbalte uzel **Místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="9b88e-164">Otevřete **zásadu Přihlášení jako zásadu služby.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="9b88e-165">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="9b88e-166">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="9b88e-167">Do pole názvu`{DOMAIN OR COMPUTER NAME\USER}`objektu zadejte uživatelský účet ( ) a výběrem **možnosti OK** přidejte uživatele do zásady.</span><span class="sxs-lookup"><span data-stu-id="9b88e-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="9b88e-168">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-168">Select **Advanced**.</span></span> <span data-ttu-id="9b88e-169">Vyberte **Najít .**</span><span class="sxs-lookup"><span data-stu-id="9b88e-169">Select **Find Now**.</span></span> <span data-ttu-id="9b88e-170">Vyberte uživatelský účet ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-170">Select the user account from the list.</span></span> <span data-ttu-id="9b88e-171">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-171">Select **OK**.</span></span> <span data-ttu-id="9b88e-172">Chcete-li přidat uživatele do zásady, vyberte znovu **OK.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="9b88e-173">Chcete-li změny přijmout, vyberte **ok** nebo **Použít.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="9b88e-174">Vytvoření a správa služby Systému Windows</span><span class="sxs-lookup"><span data-stu-id="9b88e-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="9b88e-175">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-175">Create a service</span></span>

<span data-ttu-id="9b88e-176">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="9b88e-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="9b88e-177">Z řídicího prostředí powershellu 6 pro správu proveďte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9b88e-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="9b88e-178">`{EXE PATH}`&ndash; Cesta ke složce aplikace na hostiteli `d:\myservice`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="9b88e-179">Nezahrnejte spustitelný soubor aplikace do cesty.</span><span class="sxs-lookup"><span data-stu-id="9b88e-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="9b88e-180">Koncové lomítko není nutné.</span><span class="sxs-lookup"><span data-stu-id="9b88e-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="9b88e-181">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Uživatelský účet služby `Contoso\ServiceUser`(například ).</span><span class="sxs-lookup"><span data-stu-id="9b88e-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="9b88e-182">`{SERVICE NAME}`&ndash; Název služby (například `MyService`).</span><span class="sxs-lookup"><span data-stu-id="9b88e-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="9b88e-183">`{EXE FILE PATH}`&ndash; Cesta spustitelného souboru aplikace `d:\myservice\myservice.exe`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="9b88e-184">Zahrňte název souboru spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="9b88e-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="9b88e-185">`{DESCRIPTION}`&ndash; Popis služby (například `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="9b88e-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="9b88e-186">`{DISPLAY NAME}`&ndash; Zobrazovaný název služby `My Service`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="9b88e-187">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-187">Start a service</span></span>

<span data-ttu-id="9b88e-188">Spusťte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-189">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="9b88e-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="9b88e-190">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-190">Determine a service's status</span></span>

<span data-ttu-id="9b88e-191">Chcete-li zkontrolovat stav služby, použijte následující příkaz PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-192">Stav je vykázán jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="9b88e-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="9b88e-193">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-193">Stop a service</span></span>

<span data-ttu-id="9b88e-194">Zastavení služby pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="9b88e-195">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-195">Remove a service</span></span>

<span data-ttu-id="9b88e-196">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="9b88e-197">Scénáře proxy serveru a vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="9b88e-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="9b88e-198">Služby, které interagují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, mohou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9b88e-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="9b88e-199">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="9b88e-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="9b88e-200">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="9b88e-200">Configure endpoints</span></span>

<span data-ttu-id="9b88e-201">Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na .</span><span class="sxs-lookup"><span data-stu-id="9b88e-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="9b88e-202">Nakonfigurujte adresu `ASPNETCORE_URLS` URL a port nastavením proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="9b88e-203">Další přístupy ke konfiguraci adres URL a portů naleznete v příslušném článku serveru:</span><span class="sxs-lookup"><span data-stu-id="9b88e-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="9b88e-204">Předchozí pokyny zahrnuje podporu pro koncové body HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9b88e-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="9b88e-205">Nakonfigurujte například aplikaci pro protokol HTTPS při použití ověřování se službou systému Windows.</span><span class="sxs-lookup"><span data-stu-id="9b88e-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="9b88e-206">Použití ASP.NET certifikátu pro vývoj jádra HTTPS k zabezpečení koncového bodu služby není podporováno.</span><span class="sxs-lookup"><span data-stu-id="9b88e-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="9b88e-207">Aktuální adresář a kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="9b88e-207">Current directory and content root</span></span>

<span data-ttu-id="9b88e-208">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> služby Systému Windows je složka *C:\\Windows\\system32.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="9b88e-209">Složka *system32* není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="9b88e-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="9b88e-210">Pomocí jednoho z následujících přístupů můžete udržovat a přistupovat k datovým zdrojům a souborům nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="9b88e-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="9b88e-211">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="9b88e-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="9b88e-212">Použijte [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> vyhledejte prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="9b88e-213">Když aplikace běží jako <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> služba, <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> nastaví na [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="9b88e-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="9b88e-214">Výchozí soubory nastavení aplikace, *soubor appsettings.json* a *nastavení aplikace.{ Environment}.json*, se načítají z kořenového adresáře obsahu aplikace voláním [CreateDefaultBuilder během výstavby hostitele](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="9b88e-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="9b88e-215">U dalších souborů nastavení <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>načtených vývojářským kódem <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>v oblasti není třeba volat .</span><span class="sxs-lookup"><span data-stu-id="9b88e-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9b88e-216">V následujícím příkladu *custom_settings.json* soubor existuje v kořenovém adresáři obsahu aplikace a je načten bez explicitního nastavení základní cesty:</span><span class="sxs-lookup"><span data-stu-id="9b88e-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="9b88e-217">Nepokoušejte se <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu k prostředku, protože aplikace služby Windows vrátí jako aktuální adresář složku *C:\\Windows\\system32.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="9b88e-218">Uložení souborů služby na vhodném místě na disku</span><span class="sxs-lookup"><span data-stu-id="9b88e-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="9b88e-219">Při použití <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> složky obsahující soubory určete absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9b88e-220">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9b88e-220">Troubleshoot</span></span>

<span data-ttu-id="9b88e-221">Informace o řešení potíží <xref:test/troubleshoot>s aplikací služby Windows naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="9b88e-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="9b88e-222">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="9b88e-222">Common errors</span></span>

* <span data-ttu-id="9b88e-223">Stará nebo předběžná verze prostředí PowerShell se používá.</span><span class="sxs-lookup"><span data-stu-id="9b88e-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="9b88e-224">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="9b88e-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="9b88e-225">Výstup příkazu [dotnet build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporován.</span><span class="sxs-lookup"><span data-stu-id="9b88e-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="9b88e-226">Publikované datové zdroje se nacházejí v jedné z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="9b88e-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="9b88e-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="9b88e-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="9b88e-229">Služba není ve stavu SPUŠTĚNO.</span><span class="sxs-lookup"><span data-stu-id="9b88e-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="9b88e-230">Cesty k prostředkům, které aplikace používá (například certifikáty) jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="9b88e-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="9b88e-231">Základní cesta služby systému Windows je *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="9b88e-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="9b88e-232">Uživatel nemá *Přihlásit jako servisní* práva.</span><span class="sxs-lookup"><span data-stu-id="9b88e-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="9b88e-233">Heslo uživatele vypršelo nebo nesprávně předáno při `New-Service` provádění příkazu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="9b88e-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="9b88e-234">Aplikace vyžaduje ASP.NET ověřování jádra, ale není nakonfigurovánpro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9b88e-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="9b88e-235">Port url požadavku je v aplikaci nesprávný nebo není správně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="9b88e-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="9b88e-236">Protokoly událostí systému a aplikací</span><span class="sxs-lookup"><span data-stu-id="9b88e-236">System and Application Event Logs</span></span>

<span data-ttu-id="9b88e-237">Přístup k protokolům událostí systému a aplikací:</span><span class="sxs-lookup"><span data-stu-id="9b88e-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="9b88e-238">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="9b88e-239">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="9b88e-240">Výběrem **možnosti Systém** otevřete protokol systémových událostí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="9b88e-241">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="9b88e-242">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="9b88e-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="9b88e-243">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="9b88e-243">Run the app at a command prompt</span></span>

<span data-ttu-id="9b88e-244">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="9b88e-245">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="9b88e-246">Chcete-li protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="9b88e-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="9b88e-247">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="9b88e-247">Clear package caches</span></span>

<span data-ttu-id="9b88e-248">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="9b88e-249">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="9b88e-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="9b88e-250">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="9b88e-251">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="9b88e-252">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="9b88e-253">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="9b88e-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="9b88e-254">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="9b88e-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="9b88e-255">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="9b88e-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="9b88e-256">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="9b88e-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="9b88e-257">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="9b88e-257">Slow or hanging app</span></span>

<span data-ttu-id="9b88e-258">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="9b88e-259">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="9b88e-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="9b88e-260">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="9b88e-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="9b88e-261">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="9b88e-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="9b88e-262">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="9b88e-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="9b88e-263">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="9b88e-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="9b88e-264">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="9b88e-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="9b88e-265">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="9b88e-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="9b88e-266">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b88e-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="9b88e-267">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="9b88e-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="9b88e-268">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="9b88e-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="9b88e-269">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="9b88e-270">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="9b88e-270">Analyze the dump</span></span>

<span data-ttu-id="9b88e-271">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="9b88e-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="9b88e-272">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="9b88e-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b88e-273">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9b88e-273">Additional resources</span></span>

* <span data-ttu-id="9b88e-274">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="9b88e-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9b88e-275">Aplikaci ASP.NET Core lze hostovat ve Windows jako [službu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9b88e-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="9b88e-276">Při hostování jako služba Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="9b88e-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="9b88e-277">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9b88e-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b88e-278">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9b88e-278">Prerequisites</span></span>

* [<span data-ttu-id="9b88e-279">ASP.NET jádra SDK 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="9b88e-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="9b88e-280">PowerShell 6.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="9b88e-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="9b88e-281">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="9b88e-281">App configuration</span></span>

<span data-ttu-id="9b88e-282">Aplikace vyžaduje odkazy na balíčky pro [microsoft.aspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="9b88e-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="9b88e-283">Chcete-li testovat a ladit při spuštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="9b88e-284">Zkontrolujte, zda je připojen ladicí program nebo zda je k dispozici `--console` přepínač.</span><span class="sxs-lookup"><span data-stu-id="9b88e-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="9b88e-285">Pokud je některá podmínka pravdivá (aplikace není <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>spuštěna jako služba), volejte .</span><span class="sxs-lookup"><span data-stu-id="9b88e-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="9b88e-286">Pokud jsou podmínky nepravdivé (aplikace je spuštěna jako služba):</span><span class="sxs-lookup"><span data-stu-id="9b88e-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="9b88e-287">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k publikovanému místu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="9b88e-288">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace služby Windows vrátí *c:\\windows\\system32* složku, když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="9b88e-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="9b88e-289">Další informace naleznete v části [Aktuální adresář a kořenový obsah.](#current-directory-and-content-root)</span><span class="sxs-lookup"><span data-stu-id="9b88e-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="9b88e-290">Tento krok se provádí před `CreateWebHostBuilder`konfigurací aplikace v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="9b88e-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="9b88e-291">Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="9b88e-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="9b88e-292">Vzhledem k tomu, [že zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje `--console` dvojice název-hodnota pro <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> argumenty příkazového řádku, přepínač je odebrán z argumentů před přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="9b88e-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="9b88e-293">Chcete-li zapisovat do protokolu událostí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>systému Windows, přidejte do aplikace zprostředkovatele eventlogu .</span><span class="sxs-lookup"><span data-stu-id="9b88e-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="9b88e-294">Nastavte úroveň protokolování `Logging:LogLevel:Default` pomocí klíče v *nastavení aplikace. Soubor Production.json.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="9b88e-295">V následujícím příkladu z `RunAsCustomService` ukázkové aplikace <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> se volá místo pro zpracování událostí životnosti v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="9b88e-296">Další informace naleznete v části [Zpracování událostí spuštění a zastavení.](#handle-starting-and-stopping-events)</span><span class="sxs-lookup"><span data-stu-id="9b88e-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="9b88e-297">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="9b88e-297">Deployment type</span></span>

<span data-ttu-id="9b88e-298">Informace a rady ohledně scénářů nasazení naleznete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="9b88e-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="9b88e-299">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="9b88e-299">SDK</span></span>

<span data-ttu-id="9b88e-300">Pro službu založenou na webových aplikacích, která používá razor pages nebo mvc architektury, zadejte web ovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="9b88e-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="9b88e-301">Pokud služba provádí pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte pracovní sdk v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="9b88e-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="9b88e-302">Nasazení závislé na rámci (FDD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="9b88e-303">Nasazení závislé na rozhraní (FDD) závisí na přítomnosti sdílené verze rozhraní .NET Core pro celý systém v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="9b88e-304">Při přijetí scénáře FDD podle pokynů v tomto článku sada SDK vytvoří spustitelný soubor (*.exe*), nazývaný *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="9b88e-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="9b88e-305">Identifikátor [prostředí Windows Runtime (RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="9b88e-306">V následujícím příkladu je rid `win7-x64`nastaven na .</span><span class="sxs-lookup"><span data-stu-id="9b88e-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="9b88e-307">Vlastnost `<SelfContained>` je nastavena na `false`.</span><span class="sxs-lookup"><span data-stu-id="9b88e-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="9b88e-308">Tyto vlastnosti instruují sadu SDK ke generování spustitelného souboru (*EXE*) pro Systém Windows a aplikace, která závisí na sdíleném rozhraní .NET Core framework.</span><span class="sxs-lookup"><span data-stu-id="9b88e-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="9b88e-309">Soubor *web.config,* který se obvykle vytváří při publikování aplikace ASP.NET Core, není pro aplikaci služeb Windows nutný.</span><span class="sxs-lookup"><span data-stu-id="9b88e-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="9b88e-310">Chcete-li zakázat vytváření souboru *web.config,* přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na . `true`</span><span class="sxs-lookup"><span data-stu-id="9b88e-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="9b88e-311">Samostatné nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="9b88e-312">Samostatné nasazení (SCD) nespoléhá na přítomnost sdíleného rozhraní v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="9b88e-313">Runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="9b88e-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="9b88e-314">Identifikátor [rid (Windows Runtime)](/dotnet/core/rid-catalog) je `<PropertyGroup>` součástí cílového rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9b88e-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="9b88e-315">Publikování pro více idů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="9b88e-316">Zadejte ridy v seznamu odděleného středníkem.</span><span class="sxs-lookup"><span data-stu-id="9b88e-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="9b88e-317">Použijte název [ \<vlastnosti RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="9b88e-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="9b88e-318">Další informace naleznete v tématu [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="9b88e-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="9b88e-319">Vlastnost `<SelfContained>` je nastavena na `true`:</span><span class="sxs-lookup"><span data-stu-id="9b88e-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="9b88e-320">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-320">Service user account</span></span>

<span data-ttu-id="9b88e-321">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShell 6 pro správu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="9b88e-322">V aktualizaci Windows 10 říjen 2018 (verze 1809/build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="9b88e-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-323">V os windows starší než Windows 10 říjen 2018 Update (verze 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="9b88e-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="9b88e-324">Po zobrazení výzvy zadejte [silné heslo.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)</span><span class="sxs-lookup"><span data-stu-id="9b88e-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="9b88e-325">Pokud `-AccountExpires` parametr není dodán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s <xref:System.DateTime>vypršením platnosti , účet nevyprší.</span><span class="sxs-lookup"><span data-stu-id="9b88e-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="9b88e-326">Další informace naleznete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="9b88e-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="9b88e-327">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravovaných služeb.</span><span class="sxs-lookup"><span data-stu-id="9b88e-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="9b88e-328">Další informace naleznete v [tématu Přehled účtů spravovaných služeb skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="9b88e-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="9b88e-329">Přihlásit se jako servisní práva</span><span class="sxs-lookup"><span data-stu-id="9b88e-329">Log on as a service rights</span></span>

<span data-ttu-id="9b88e-330">Chcete-li vytvořit *přihlásit jako* práva služby pro účet uživatele služby:</span><span class="sxs-lookup"><span data-stu-id="9b88e-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="9b88e-331">Spuštěním souboru *secpol.msc*otevřete editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9b88e-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="9b88e-332">Rozbalte uzel **Místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="9b88e-333">Otevřete **zásadu Přihlášení jako zásadu služby.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="9b88e-334">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="9b88e-335">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="9b88e-336">Do pole názvu`{DOMAIN OR COMPUTER NAME\USER}`objektu zadejte uživatelský účet ( ) a výběrem **možnosti OK** přidejte uživatele do zásady.</span><span class="sxs-lookup"><span data-stu-id="9b88e-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="9b88e-337">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-337">Select **Advanced**.</span></span> <span data-ttu-id="9b88e-338">Vyberte **Najít .**</span><span class="sxs-lookup"><span data-stu-id="9b88e-338">Select **Find Now**.</span></span> <span data-ttu-id="9b88e-339">Vyberte uživatelský účet ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-339">Select the user account from the list.</span></span> <span data-ttu-id="9b88e-340">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-340">Select **OK**.</span></span> <span data-ttu-id="9b88e-341">Chcete-li přidat uživatele do zásady, vyberte znovu **OK.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="9b88e-342">Chcete-li změny přijmout, vyberte **ok** nebo **Použít.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="9b88e-343">Vytvoření a správa služby Systému Windows</span><span class="sxs-lookup"><span data-stu-id="9b88e-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="9b88e-344">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-344">Create a service</span></span>

<span data-ttu-id="9b88e-345">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="9b88e-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="9b88e-346">Z řídicího prostředí powershellu 6 pro správu proveďte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9b88e-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="9b88e-347">`{EXE PATH}`&ndash; Cesta ke složce aplikace na hostiteli `d:\myservice`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="9b88e-348">Nezahrnejte spustitelný soubor aplikace do cesty.</span><span class="sxs-lookup"><span data-stu-id="9b88e-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="9b88e-349">Koncové lomítko není nutné.</span><span class="sxs-lookup"><span data-stu-id="9b88e-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="9b88e-350">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Uživatelský účet služby `Contoso\ServiceUser`(například ).</span><span class="sxs-lookup"><span data-stu-id="9b88e-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="9b88e-351">`{SERVICE NAME}`&ndash; Název služby (například `MyService`).</span><span class="sxs-lookup"><span data-stu-id="9b88e-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="9b88e-352">`{EXE FILE PATH}`&ndash; Cesta spustitelného souboru aplikace `d:\myservice\myservice.exe`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="9b88e-353">Zahrňte název souboru spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="9b88e-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="9b88e-354">`{DESCRIPTION}`&ndash; Popis služby (například `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="9b88e-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="9b88e-355">`{DISPLAY NAME}`&ndash; Zobrazovaný název služby `My Service`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="9b88e-356">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-356">Start a service</span></span>

<span data-ttu-id="9b88e-357">Spusťte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-358">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="9b88e-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="9b88e-359">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-359">Determine a service's status</span></span>

<span data-ttu-id="9b88e-360">Chcete-li zkontrolovat stav služby, použijte následující příkaz PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-361">Stav je vykázán jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="9b88e-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="9b88e-362">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-362">Stop a service</span></span>

<span data-ttu-id="9b88e-363">Zastavení služby pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="9b88e-364">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-364">Remove a service</span></span>

<span data-ttu-id="9b88e-365">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="9b88e-366">Zpracování událostí spuštění a zastavení</span><span class="sxs-lookup"><span data-stu-id="9b88e-366">Handle starting and stopping events</span></span>

<span data-ttu-id="9b88e-367">Zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události:</span><span class="sxs-lookup"><span data-stu-id="9b88e-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="9b88e-368">Vytvořte třídu, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> která `OnStarting`je `OnStarted`odvozena z , a `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="9b88e-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="9b88e-369">Vytvořte metodu <xref:Microsoft.AspNetCore.Hosting.IWebHost> rozšíření, `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*>která předá do :</span><span class="sxs-lookup"><span data-stu-id="9b88e-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="9b88e-370">V `Program.Main`, `RunAsCustomService` volání metody <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>rozšíření namísto :</span><span class="sxs-lookup"><span data-stu-id="9b88e-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="9b88e-371">Chcete-li zobrazit <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main`umístění v , naleznete v ukázce kódu uvedené v části [Typ nasazení.](#deployment-type)</span><span class="sxs-lookup"><span data-stu-id="9b88e-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="9b88e-372">Scénáře proxy serveru a vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="9b88e-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="9b88e-373">Služby, které interagují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, mohou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9b88e-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="9b88e-374">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="9b88e-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="9b88e-375">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="9b88e-375">Configure endpoints</span></span>

<span data-ttu-id="9b88e-376">Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na .</span><span class="sxs-lookup"><span data-stu-id="9b88e-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="9b88e-377">Nakonfigurujte adresu `ASPNETCORE_URLS` URL a port nastavením proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="9b88e-378">Další přístupy ke konfiguraci adres URL a portů naleznete v příslušném článku serveru:</span><span class="sxs-lookup"><span data-stu-id="9b88e-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="9b88e-379">Předchozí pokyny zahrnuje podporu pro koncové body HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9b88e-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="9b88e-380">Nakonfigurujte například aplikaci pro protokol HTTPS při použití ověřování se službou systému Windows.</span><span class="sxs-lookup"><span data-stu-id="9b88e-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="9b88e-381">Použití ASP.NET certifikátu pro vývoj jádra HTTPS k zabezpečení koncového bodu služby není podporováno.</span><span class="sxs-lookup"><span data-stu-id="9b88e-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="9b88e-382">Aktuální adresář a kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="9b88e-382">Current directory and content root</span></span>

<span data-ttu-id="9b88e-383">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> služby Systému Windows je složka *C:\\Windows\\system32.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="9b88e-384">Složka *system32* není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="9b88e-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="9b88e-385">Pomocí jednoho z následujících přístupů můžete udržovat a přistupovat k datovým zdrojům a souborům nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="9b88e-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="9b88e-386">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="9b88e-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="9b88e-387">Je <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> stejná cesta k `binPath` dispozici argument při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="9b88e-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="9b88e-388">Místo volání `GetCurrentDirectory` k vytvoření cest k <xref:System.IO.Directory.SetCurrentDirectory*> souborům nastavení volejte s cestou ke [kořenu obsahu](xref:fundamentals/index#content-root)aplikace .</span><span class="sxs-lookup"><span data-stu-id="9b88e-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="9b88e-389">V `Program.Main`aplikaci určete cestu ke složce spustitelného souboru služby a pomocí této cesty vytvořte kořen obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="9b88e-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="9b88e-390">Uložení souborů služby na vhodném místě na disku</span><span class="sxs-lookup"><span data-stu-id="9b88e-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="9b88e-391">Při použití <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> složky obsahující soubory určete absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9b88e-392">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9b88e-392">Troubleshoot</span></span>

<span data-ttu-id="9b88e-393">Informace o řešení potíží <xref:test/troubleshoot>s aplikací služby Windows naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="9b88e-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="9b88e-394">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="9b88e-394">Common errors</span></span>

* <span data-ttu-id="9b88e-395">Stará nebo předběžná verze prostředí PowerShell se používá.</span><span class="sxs-lookup"><span data-stu-id="9b88e-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="9b88e-396">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="9b88e-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="9b88e-397">Výstup příkazu [dotnet build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporován.</span><span class="sxs-lookup"><span data-stu-id="9b88e-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="9b88e-398">Publikované datové zdroje se nacházejí v jedné z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="9b88e-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="9b88e-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="9b88e-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="9b88e-401">Služba není ve stavu SPUŠTĚNO.</span><span class="sxs-lookup"><span data-stu-id="9b88e-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="9b88e-402">Cesty k prostředkům, které aplikace používá (například certifikáty) jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="9b88e-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="9b88e-403">Základní cesta služby systému Windows je *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="9b88e-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="9b88e-404">Uživatel nemá *Přihlásit jako servisní* práva.</span><span class="sxs-lookup"><span data-stu-id="9b88e-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="9b88e-405">Heslo uživatele vypršelo nebo nesprávně předáno při `New-Service` provádění příkazu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="9b88e-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="9b88e-406">Aplikace vyžaduje ASP.NET ověřování jádra, ale není nakonfigurovánpro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9b88e-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="9b88e-407">Port url požadavku je v aplikaci nesprávný nebo není správně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="9b88e-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="9b88e-408">Protokoly událostí systému a aplikací</span><span class="sxs-lookup"><span data-stu-id="9b88e-408">System and Application Event Logs</span></span>

<span data-ttu-id="9b88e-409">Přístup k protokolům událostí systému a aplikací:</span><span class="sxs-lookup"><span data-stu-id="9b88e-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="9b88e-410">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="9b88e-411">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="9b88e-412">Výběrem **možnosti Systém** otevřete protokol systémových událostí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="9b88e-413">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="9b88e-414">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="9b88e-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="9b88e-415">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="9b88e-415">Run the app at a command prompt</span></span>

<span data-ttu-id="9b88e-416">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="9b88e-417">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="9b88e-418">Chcete-li protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="9b88e-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="9b88e-419">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="9b88e-419">Clear package caches</span></span>

<span data-ttu-id="9b88e-420">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="9b88e-421">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="9b88e-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="9b88e-422">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="9b88e-423">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="9b88e-424">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="9b88e-425">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="9b88e-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="9b88e-426">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="9b88e-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="9b88e-427">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="9b88e-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="9b88e-428">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="9b88e-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="9b88e-429">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="9b88e-429">Slow or hanging app</span></span>

<span data-ttu-id="9b88e-430">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="9b88e-431">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="9b88e-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="9b88e-432">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="9b88e-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="9b88e-433">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="9b88e-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="9b88e-434">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="9b88e-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="9b88e-435">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="9b88e-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="9b88e-436">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="9b88e-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="9b88e-437">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="9b88e-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="9b88e-438">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b88e-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="9b88e-439">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="9b88e-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="9b88e-440">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="9b88e-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="9b88e-441">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="9b88e-442">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="9b88e-442">Analyze the dump</span></span>

<span data-ttu-id="9b88e-443">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="9b88e-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="9b88e-444">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="9b88e-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b88e-445">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9b88e-445">Additional resources</span></span>

* <span data-ttu-id="9b88e-446">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="9b88e-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9b88e-447">Aplikaci ASP.NET Core lze hostovat ve Windows jako [službu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9b88e-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="9b88e-448">Při hostování jako služba Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="9b88e-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="9b88e-449">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9b88e-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b88e-450">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9b88e-450">Prerequisites</span></span>

* [<span data-ttu-id="9b88e-451">ASP.NET jádra SDK 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="9b88e-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="9b88e-452">PowerShell 6.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="9b88e-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="9b88e-453">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="9b88e-453">App configuration</span></span>

<span data-ttu-id="9b88e-454">Aplikace vyžaduje odkazy na balíčky pro [microsoft.aspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="9b88e-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="9b88e-455">Chcete-li testovat a ladit při spuštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="9b88e-456">Zkontrolujte, zda je připojen ladicí program nebo zda je k dispozici `--console` přepínač.</span><span class="sxs-lookup"><span data-stu-id="9b88e-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="9b88e-457">Pokud je některá podmínka pravdivá (aplikace není <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>spuštěna jako služba), volejte .</span><span class="sxs-lookup"><span data-stu-id="9b88e-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="9b88e-458">Pokud jsou podmínky nepravdivé (aplikace je spuštěna jako služba):</span><span class="sxs-lookup"><span data-stu-id="9b88e-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="9b88e-459">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k publikovanému místu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="9b88e-460">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> získat cestu, protože aplikace služby Windows vrátí *c:\\windows\\system32* složku, když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="9b88e-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="9b88e-461">Další informace naleznete v části [Aktuální adresář a kořenový obsah.](#current-directory-and-content-root)</span><span class="sxs-lookup"><span data-stu-id="9b88e-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="9b88e-462">Tento krok se provádí před `CreateWebHostBuilder`konfigurací aplikace v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="9b88e-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="9b88e-463">Volání <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="9b88e-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="9b88e-464">Vzhledem k tomu, [že zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje `--console` dvojice název-hodnota pro <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> argumenty příkazového řádku, přepínač je odebrán z argumentů před přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="9b88e-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="9b88e-465">Chcete-li zapisovat do protokolu událostí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>systému Windows, přidejte do aplikace zprostředkovatele eventlogu .</span><span class="sxs-lookup"><span data-stu-id="9b88e-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="9b88e-466">Nastavte úroveň protokolování `Logging:LogLevel:Default` pomocí klíče v *nastavení aplikace. Soubor Production.json.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="9b88e-467">V následujícím příkladu z `RunAsCustomService` ukázkové aplikace <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> se volá místo pro zpracování událostí životnosti v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="9b88e-468">Další informace naleznete v části [Zpracování událostí spuštění a zastavení.](#handle-starting-and-stopping-events)</span><span class="sxs-lookup"><span data-stu-id="9b88e-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="9b88e-469">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="9b88e-469">Deployment type</span></span>

<span data-ttu-id="9b88e-470">Informace a rady ohledně scénářů nasazení naleznete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="9b88e-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="9b88e-471">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="9b88e-471">SDK</span></span>

<span data-ttu-id="9b88e-472">Pro službu založenou na webových aplikacích, která používá razor pages nebo mvc architektury, zadejte web ovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="9b88e-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="9b88e-473">Pokud služba provádí pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte pracovní sdk v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="9b88e-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="9b88e-474">Nasazení závislé na rámci (FDD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="9b88e-475">Nasazení závislé na rozhraní (FDD) závisí na přítomnosti sdílené verze rozhraní .NET Core pro celý systém v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="9b88e-476">Při přijetí scénáře FDD podle pokynů v tomto článku sada SDK vytvoří spustitelný soubor (*.exe*), nazývaný *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="9b88e-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="9b88e-477">Identifikátor [prostředí Windows Runtime (RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="9b88e-478">V následujícím příkladu je rid `win7-x64`nastaven na .</span><span class="sxs-lookup"><span data-stu-id="9b88e-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="9b88e-479">Vlastnost `<SelfContained>` je nastavena na `false`.</span><span class="sxs-lookup"><span data-stu-id="9b88e-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="9b88e-480">Tyto vlastnosti instruují sadu SDK ke generování spustitelného souboru (*EXE*) pro Systém Windows a aplikace, která závisí na sdíleném rozhraní .NET Core framework.</span><span class="sxs-lookup"><span data-stu-id="9b88e-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="9b88e-481">Vlastnost `<UseAppHost>` je nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="9b88e-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="9b88e-482">Tato vlastnost poskytuje službě aktivační cestu (spustitelný *soubor, .exe)* pro FDD.</span><span class="sxs-lookup"><span data-stu-id="9b88e-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="9b88e-483">Soubor *web.config,* který se obvykle vytváří při publikování aplikace ASP.NET Core, není pro aplikaci služeb Windows nutný.</span><span class="sxs-lookup"><span data-stu-id="9b88e-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="9b88e-484">Chcete-li zakázat vytváření souboru *web.config,* přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na . `true`</span><span class="sxs-lookup"><span data-stu-id="9b88e-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="9b88e-485">Samostatné nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="9b88e-486">Samostatné nasazení (SCD) nespoléhá na přítomnost sdíleného rozhraní v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="9b88e-487">Runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="9b88e-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="9b88e-488">Identifikátor [rid (Windows Runtime)](/dotnet/core/rid-catalog) je `<PropertyGroup>` součástí cílového rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9b88e-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="9b88e-489">Publikování pro více idů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="9b88e-490">Zadejte ridy v seznamu odděleného středníkem.</span><span class="sxs-lookup"><span data-stu-id="9b88e-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="9b88e-491">Použijte název [ \<vlastnosti RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (množné číslo).</span><span class="sxs-lookup"><span data-stu-id="9b88e-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="9b88e-492">Další informace naleznete v tématu [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="9b88e-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="9b88e-493">Vlastnost `<SelfContained>` je nastavena na `true`:</span><span class="sxs-lookup"><span data-stu-id="9b88e-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="9b88e-494">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-494">Service user account</span></span>

<span data-ttu-id="9b88e-495">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShell 6 pro správu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="9b88e-496">V aktualizaci Windows 10 říjen 2018 (verze 1809/build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="9b88e-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-497">V os windows starší než Windows 10 říjen 2018 Update (verze 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="9b88e-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="9b88e-498">Po zobrazení výzvy zadejte [silné heslo.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)</span><span class="sxs-lookup"><span data-stu-id="9b88e-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="9b88e-499">Pokud `-AccountExpires` parametr není dodán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s <xref:System.DateTime>vypršením platnosti , účet nevyprší.</span><span class="sxs-lookup"><span data-stu-id="9b88e-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="9b88e-500">Další informace naleznete v tématu [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="9b88e-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="9b88e-501">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravovaných služeb.</span><span class="sxs-lookup"><span data-stu-id="9b88e-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="9b88e-502">Další informace naleznete v [tématu Přehled účtů spravovaných služeb skupiny](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="9b88e-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="9b88e-503">Přihlásit se jako servisní práva</span><span class="sxs-lookup"><span data-stu-id="9b88e-503">Log on as a service rights</span></span>

<span data-ttu-id="9b88e-504">Chcete-li vytvořit *přihlásit jako* práva služby pro účet uživatele služby:</span><span class="sxs-lookup"><span data-stu-id="9b88e-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="9b88e-505">Spuštěním souboru *secpol.msc*otevřete editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9b88e-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="9b88e-506">Rozbalte uzel **Místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="9b88e-507">Otevřete **zásadu Přihlášení jako zásadu služby.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="9b88e-508">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="9b88e-509">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="9b88e-510">Do pole názvu`{DOMAIN OR COMPUTER NAME\USER}`objektu zadejte uživatelský účet ( ) a výběrem **možnosti OK** přidejte uživatele do zásady.</span><span class="sxs-lookup"><span data-stu-id="9b88e-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="9b88e-511">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-511">Select **Advanced**.</span></span> <span data-ttu-id="9b88e-512">Vyberte **Najít .**</span><span class="sxs-lookup"><span data-stu-id="9b88e-512">Select **Find Now**.</span></span> <span data-ttu-id="9b88e-513">Vyberte uživatelský účet ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-513">Select the user account from the list.</span></span> <span data-ttu-id="9b88e-514">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="9b88e-514">Select **OK**.</span></span> <span data-ttu-id="9b88e-515">Chcete-li přidat uživatele do zásady, vyberte znovu **OK.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="9b88e-516">Chcete-li změny přijmout, vyberte **ok** nebo **Použít.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="9b88e-517">Vytvoření a správa služby Systému Windows</span><span class="sxs-lookup"><span data-stu-id="9b88e-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="9b88e-518">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-518">Create a service</span></span>

<span data-ttu-id="9b88e-519">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="9b88e-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="9b88e-520">Z řídicího prostředí powershellu 6 pro správu proveďte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="9b88e-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="9b88e-521">`{EXE PATH}`&ndash; Cesta ke složce aplikace na hostiteli `d:\myservice`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="9b88e-522">Nezahrnejte spustitelný soubor aplikace do cesty.</span><span class="sxs-lookup"><span data-stu-id="9b88e-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="9b88e-523">Koncové lomítko není nutné.</span><span class="sxs-lookup"><span data-stu-id="9b88e-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="9b88e-524">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Uživatelský účet služby `Contoso\ServiceUser`(například ).</span><span class="sxs-lookup"><span data-stu-id="9b88e-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="9b88e-525">`{SERVICE NAME}`&ndash; Název služby (například `MyService`).</span><span class="sxs-lookup"><span data-stu-id="9b88e-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="9b88e-526">`{EXE FILE PATH}`&ndash; Cesta spustitelného souboru aplikace `d:\myservice\myservice.exe`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="9b88e-527">Zahrňte název souboru spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="9b88e-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="9b88e-528">`{DESCRIPTION}`&ndash; Popis služby (například `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="9b88e-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="9b88e-529">`{DISPLAY NAME}`&ndash; Zobrazovaný název služby `My Service`(například).</span><span class="sxs-lookup"><span data-stu-id="9b88e-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="9b88e-530">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-530">Start a service</span></span>

<span data-ttu-id="9b88e-531">Spusťte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-532">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="9b88e-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="9b88e-533">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-533">Determine a service's status</span></span>

<span data-ttu-id="9b88e-534">Chcete-li zkontrolovat stav služby, použijte následující příkaz PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="9b88e-535">Stav je vykázán jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="9b88e-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="9b88e-536">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-536">Stop a service</span></span>

<span data-ttu-id="9b88e-537">Zastavení služby pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="9b88e-538">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="9b88e-538">Remove a service</span></span>

<span data-ttu-id="9b88e-539">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu Powershellu 6:</span><span class="sxs-lookup"><span data-stu-id="9b88e-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="9b88e-540">Zpracování událostí spuštění a zastavení</span><span class="sxs-lookup"><span data-stu-id="9b88e-540">Handle starting and stopping events</span></span>

<span data-ttu-id="9b88e-541">Zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> události:</span><span class="sxs-lookup"><span data-stu-id="9b88e-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="9b88e-542">Vytvořte třídu, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> která `OnStarting`je `OnStarted`odvozena z , a `OnStopping` metody:</span><span class="sxs-lookup"><span data-stu-id="9b88e-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="9b88e-543">Vytvořte metodu <xref:Microsoft.AspNetCore.Hosting.IWebHost> rozšíření, `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*>která předá do :</span><span class="sxs-lookup"><span data-stu-id="9b88e-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="9b88e-544">V `Program.Main`, `RunAsCustomService` volání metody <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>rozšíření namísto :</span><span class="sxs-lookup"><span data-stu-id="9b88e-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="9b88e-545">Chcete-li zobrazit <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main`umístění v , naleznete v ukázce kódu uvedené v části [Typ nasazení.](#deployment-type)</span><span class="sxs-lookup"><span data-stu-id="9b88e-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="9b88e-546">Scénáře proxy serveru a vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="9b88e-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="9b88e-547">Služby, které interagují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, mohou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9b88e-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="9b88e-548">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="9b88e-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="9b88e-549">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="9b88e-549">Configure endpoints</span></span>

<span data-ttu-id="9b88e-550">Ve výchozím nastavení se `http://localhost:5000`ASP.NET jádro váže na .</span><span class="sxs-lookup"><span data-stu-id="9b88e-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="9b88e-551">Nakonfigurujte adresu `ASPNETCORE_URLS` URL a port nastavením proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="9b88e-552">Další přístupy ke konfiguraci adres URL a portů naleznete v příslušném článku serveru:</span><span class="sxs-lookup"><span data-stu-id="9b88e-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="9b88e-553">Předchozí pokyny zahrnuje podporu pro koncové body HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9b88e-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="9b88e-554">Nakonfigurujte například aplikaci pro protokol HTTPS při použití ověřování se službou systému Windows.</span><span class="sxs-lookup"><span data-stu-id="9b88e-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="9b88e-555">Použití ASP.NET certifikátu pro vývoj jádra HTTPS k zabezpečení koncového bodu služby není podporováno.</span><span class="sxs-lookup"><span data-stu-id="9b88e-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="9b88e-556">Aktuální adresář a kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="9b88e-556">Current directory and content root</span></span>

<span data-ttu-id="9b88e-557">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> služby Systému Windows je složka *C:\\Windows\\system32.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="9b88e-558">Složka *system32* není vhodné umístění pro ukládání souborů služby (například soubory nastavení).</span><span class="sxs-lookup"><span data-stu-id="9b88e-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="9b88e-559">Pomocí jednoho z následujících přístupů můžete udržovat a přistupovat k datovým zdrojům a souborům nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="9b88e-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="9b88e-560">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="9b88e-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="9b88e-561">Je <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> stejná cesta k `binPath` dispozici argument při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="9b88e-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="9b88e-562">Místo volání `GetCurrentDirectory` k vytvoření cest k <xref:System.IO.Directory.SetCurrentDirectory*> souborům nastavení volejte s cestou ke [kořenu obsahu](xref:fundamentals/index#content-root)aplikace .</span><span class="sxs-lookup"><span data-stu-id="9b88e-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="9b88e-563">V `Program.Main`aplikaci určete cestu ke složce spustitelného souboru služby a pomocí této cesty vytvořte kořen obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="9b88e-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="9b88e-564">Uložení souborů služby na vhodném místě na disku</span><span class="sxs-lookup"><span data-stu-id="9b88e-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="9b88e-565">Při použití <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> složky obsahující soubory určete absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9b88e-566">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9b88e-566">Troubleshoot</span></span>

<span data-ttu-id="9b88e-567">Informace o řešení potíží <xref:test/troubleshoot>s aplikací služby Windows naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="9b88e-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="9b88e-568">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="9b88e-568">Common errors</span></span>

* <span data-ttu-id="9b88e-569">Stará nebo předběžná verze prostředí PowerShell se používá.</span><span class="sxs-lookup"><span data-stu-id="9b88e-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="9b88e-570">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="9b88e-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="9b88e-571">Výstup příkazu [dotnet build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporován.</span><span class="sxs-lookup"><span data-stu-id="9b88e-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="9b88e-572">Publikované datové zdroje se nacházejí v jedné z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="9b88e-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="9b88e-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="9b88e-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="9b88e-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="9b88e-575">Služba není ve stavu SPUŠTĚNO.</span><span class="sxs-lookup"><span data-stu-id="9b88e-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="9b88e-576">Cesty k prostředkům, které aplikace používá (například certifikáty) jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="9b88e-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="9b88e-577">Základní cesta služby systému Windows je *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="9b88e-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="9b88e-578">Uživatel nemá *Přihlásit jako servisní* práva.</span><span class="sxs-lookup"><span data-stu-id="9b88e-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="9b88e-579">Heslo uživatele vypršelo nebo nesprávně předáno při `New-Service` provádění příkazu PowerShell.</span><span class="sxs-lookup"><span data-stu-id="9b88e-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="9b88e-580">Aplikace vyžaduje ASP.NET ověřování jádra, ale není nakonfigurovánpro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9b88e-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="9b88e-581">Port url požadavku je v aplikaci nesprávný nebo není správně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="9b88e-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="9b88e-582">Protokoly událostí systému a aplikací</span><span class="sxs-lookup"><span data-stu-id="9b88e-582">System and Application Event Logs</span></span>

<span data-ttu-id="9b88e-583">Přístup k protokolům událostí systému a aplikací:</span><span class="sxs-lookup"><span data-stu-id="9b88e-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="9b88e-584">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="9b88e-585">V **Prohlížeči událostí**otevřete uzel **Protokoly systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="9b88e-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="9b88e-586">Výběrem **možnosti Systém** otevřete protokol systémových událostí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="9b88e-587">Výběrem **možnosti Aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="9b88e-588">Vyhledejte chyby spojené s aplikací se selháním.</span><span class="sxs-lookup"><span data-stu-id="9b88e-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="9b88e-589">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="9b88e-589">Run the app at a command prompt</span></span>

<span data-ttu-id="9b88e-590">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="9b88e-591">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="9b88e-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="9b88e-592">Chcete-li protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="9b88e-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="9b88e-593">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="9b88e-593">Clear package caches</span></span>

<span data-ttu-id="9b88e-594">Funkční aplikace může selhat ihned po upgradu sady .NET Core SDK ve vývojovém počítači nebo změně verze balíčku v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="9b88e-595">V některých případech nesouvislé balíčky může přerušit aplikaci při provádění hlavních upgradů.</span><span class="sxs-lookup"><span data-stu-id="9b88e-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="9b88e-596">Většinu těchto problémů lze opravit podle následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="9b88e-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="9b88e-597">Odstraňte složky *bin* a *obj.*</span><span class="sxs-lookup"><span data-stu-id="9b88e-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="9b88e-598">Vymazat mezipaměti balíčku spuštěním [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="9b88e-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="9b88e-599">Vymazání mezipaměti balíčků lze také provést pomocí nástroje [nuget.exe](https://www.nuget.org/downloads) a spuštěnípříkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="9b88e-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="9b88e-600">*nuget.exe* není přibalená instalace s operačním systémem Windows desktop a musí být získány odděleně od [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="9b88e-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="9b88e-601">Obnovte a znovu vytvořte projekt.</span><span class="sxs-lookup"><span data-stu-id="9b88e-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="9b88e-602">Před opětovnou nasazením aplikace odstraňte všechny soubory ve složce nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="9b88e-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="9b88e-603">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="9b88e-603">Slow or hanging app</span></span>

<span data-ttu-id="9b88e-604">*Výpis stavu systému* je snímek paměti systému a může pomoci určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b88e-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="9b88e-605">Aplikace dojde k chybě nebo narazí na výjimku</span><span class="sxs-lookup"><span data-stu-id="9b88e-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="9b88e-606">Získání a analýza výpisu z [programu Zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="9b88e-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="9b88e-607">Vytvořte složku, ve které `c:\dumps`budou uloženy soubory s výpisem stavu systému .</span><span class="sxs-lookup"><span data-stu-id="9b88e-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="9b88e-608">Spusťte [skript EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="9b88e-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="9b88e-609">Spusťte aplikaci za podmínek, které způsobují selhání dojít.</span><span class="sxs-lookup"><span data-stu-id="9b88e-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="9b88e-610">Po selhání spusťte [skript DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="9b88e-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="9b88e-611">Po zhroucení aplikace a výpis kolekce je kompletní, aplikace je povoleno ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="9b88e-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="9b88e-612">Skript PowerShell udává WER tak, aby shromažďoval až pět výpisů v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9b88e-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="9b88e-613">Výpisy stavu systému může zabírají velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="9b88e-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="9b88e-614">Aplikace přestane reagovat, selže při spuštění nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="9b88e-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="9b88e-615">Když aplikace *přestane reagovat* (přestane reagovat, ale nezhroutí se), selže při spuštění nebo běží normálně, najdete v [tématu Soubory výpisu uživatelského režimu: Výběr nejvhodnějšího nástroje](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pro výběr vhodného nástroje pro vytvoření výpisu.</span><span class="sxs-lookup"><span data-stu-id="9b88e-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="9b88e-616">Analyzovat výpis</span><span class="sxs-lookup"><span data-stu-id="9b88e-616">Analyze the dump</span></span>

<span data-ttu-id="9b88e-617">Výpis lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="9b88e-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="9b88e-618">Další informace naleznete [v tématu Analýza souboru s výpisem stavu uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="9b88e-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b88e-619">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9b88e-619">Additional resources</span></span>

* <span data-ttu-id="9b88e-620">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu SNI)</span><span class="sxs-lookup"><span data-stu-id="9b88e-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
