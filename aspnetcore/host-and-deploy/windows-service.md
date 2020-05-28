---
<span data-ttu-id="d8b33-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8b33-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8b33-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8b33-102">'Blazor'</span></span>
- <span data-ttu-id="d8b33-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8b33-103">'Identity'</span></span>
- <span data-ttu-id="d8b33-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8b33-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8b33-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8b33-105">'Razor'</span></span>
- <span data-ttu-id="d8b33-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8b33-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="d8b33-107">ASP.NET Core hostitele ve službě systému Windows</span><span class="sxs-lookup"><span data-stu-id="d8b33-107">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8b33-108">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="d8b33-108">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d8b33-109">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="d8b33-109">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d8b33-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8b33-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8b33-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d8b33-111">Prerequisites</span></span>

* [<span data-ttu-id="d8b33-112">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d8b33-112">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d8b33-113">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d8b33-113">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="d8b33-114">Šablona služby pracovní proces</span><span class="sxs-lookup"><span data-stu-id="d8b33-114">Worker Service template</span></span>

<span data-ttu-id="d8b33-115">Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb.</span><span class="sxs-lookup"><span data-stu-id="d8b33-115">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="d8b33-116">Použití šablony jako základu pro aplikaci služby systému Windows:</span><span class="sxs-lookup"><span data-stu-id="d8b33-116">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="d8b33-117">Vytvořte aplikaci pracovní služby ze šablony .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8b33-117">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="d8b33-118">Podle pokynů v části [Konfigurace aplikace](#app-configuration) aktualizujte aplikaci pracovní služby pracovních procesů tak, aby mohla běžet jako služba systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d8b33-118">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="d8b33-119">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="d8b33-119">App configuration</span></span>

<span data-ttu-id="d8b33-120">Aplikace vyžaduje odkaz na balíček pro [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="d8b33-120">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="d8b33-121">`IHostBuilder.UseWindowsService`je volána při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="d8b33-121">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="d8b33-122">Pokud je aplikace spuštěná jako služba systému Windows, metoda:</span><span class="sxs-lookup"><span data-stu-id="d8b33-122">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="d8b33-123">Nastaví dobu života hostitele na `WindowsServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-123">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="d8b33-124">Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="d8b33-124">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="d8b33-125">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-125">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="d8b33-126">Povolí protokolování do protokolu událostí:</span><span class="sxs-lookup"><span data-stu-id="d8b33-126">Enables logging to the event log:</span></span>
  * <span data-ttu-id="d8b33-127">Název aplikace se používá jako výchozí název zdroje.</span><span class="sxs-lookup"><span data-stu-id="d8b33-127">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="d8b33-128">Výchozí úroveň protokolování je *Upozornění* nebo vyšší pro aplikaci na základě šablony ASP.NET Core, která volá `CreateDefaultBuilder` k sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="d8b33-128">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="d8b33-129">Přepište výchozí úroveň protokolu pomocí `Logging:EventLog:LogLevel:Default` klíče v souboru *appSettings. JSON* / *appSettings. { Environment}. JSON* nebo jiný poskytovatel konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-129">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="d8b33-130">Pouze správci mohou vytvářet nové zdroje událostí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-130">Only administrators can create new event sources.</span></span> <span data-ttu-id="d8b33-131">Když zdroj události nejde vytvořit pomocí názvu aplikace, zaprotokoluje se upozornění na zdroj *aplikace* a protokoly událostí jsou zakázané.</span><span class="sxs-lookup"><span data-stu-id="d8b33-131">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="d8b33-132">V `CreateHostBuilder` *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8b33-132">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="d8b33-133">Toto téma doprovází následující ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-133">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="d8b33-134">Ukázka služby na pozadí Worker: Ukázka newebové aplikace založená na [šabloně pracovní služby pracovního procesu](#worker-service-template) , která používá [hostované služby](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-134">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="d8b33-135">Ukázka Web App Service: Razor Webová aplikace stránky, která se spouští jako služba systému Windows s [hostovanými službami](xref:fundamentals/host/hosted-services) pro úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-135">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="d8b33-136">Pokyny pro MVC najdete v článcích v tématu <xref:mvc/overview> a <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-136">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="d8b33-137">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="d8b33-137">Deployment type</span></span>

<span data-ttu-id="d8b33-138">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d8b33-138">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d8b33-139">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="d8b33-139">SDK</span></span>

<span data-ttu-id="d8b33-140">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-140">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d8b33-141">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-141">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d8b33-142">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-142">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d8b33-143">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-143">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d8b33-144">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="d8b33-144">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d8b33-145">Pokud používáte [webovou sadu SDK](#sdk), soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci služby systému Windows zapotřebí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-145">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d8b33-146">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-146">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d8b33-147">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-147">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d8b33-148">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-148">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d8b33-149">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8b33-149">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d8b33-150">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-150">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d8b33-151">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="d8b33-151">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d8b33-152">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="d8b33-152">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d8b33-153">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d8b33-153">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d8b33-154">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d8b33-154">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="d8b33-155">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-155">Service user account</span></span>

<span data-ttu-id="d8b33-156">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="d8b33-156">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d8b33-157">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="d8b33-157">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-158">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d8b33-158">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d8b33-159">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-159">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d8b33-160">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-160">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d8b33-161">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="d8b33-161">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d8b33-162">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-162">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d8b33-163">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d8b33-163">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d8b33-164">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-164">Log on as a service rights</span></span>

<span data-ttu-id="d8b33-165">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="d8b33-165">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d8b33-166">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d8b33-166">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d8b33-167">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-167">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d8b33-168">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-168">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d8b33-169">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-169">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d8b33-170">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d8b33-170">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d8b33-171">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="d8b33-171">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d8b33-172">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-172">Select **Advanced**.</span></span> <span data-ttu-id="d8b33-173">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-173">Select **Find Now**.</span></span> <span data-ttu-id="d8b33-174">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="d8b33-174">Select the user account from the list.</span></span> <span data-ttu-id="d8b33-175">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-175">Select **OK**.</span></span> <span data-ttu-id="d8b33-176">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="d8b33-176">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d8b33-177">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-177">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d8b33-178">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="d8b33-178">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d8b33-179">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-179">Create a service</span></span>

<span data-ttu-id="d8b33-180">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d8b33-180">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d8b33-181">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d8b33-181">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d8b33-182">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-182">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d8b33-183">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-183">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d8b33-184">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="d8b33-184">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d8b33-185">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-185">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d8b33-186">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-186">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d8b33-187">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-187">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d8b33-188">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="d8b33-188">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d8b33-189">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-189">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d8b33-190">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-190">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d8b33-191">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-191">Start a service</span></span>

<span data-ttu-id="d8b33-192">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-192">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-193">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="d8b33-193">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d8b33-194">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-194">Determine a service's status</span></span>

<span data-ttu-id="d8b33-195">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-195">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-196">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="d8b33-196">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d8b33-197">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-197">Stop a service</span></span>

<span data-ttu-id="d8b33-198">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-198">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d8b33-199">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-199">Remove a service</span></span>

<span data-ttu-id="d8b33-200">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-200">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d8b33-201">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d8b33-201">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d8b33-202">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-202">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d8b33-203">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d8b33-203">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d8b33-204">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="d8b33-204">Configure endpoints</span></span>

<span data-ttu-id="d8b33-205">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-205">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d8b33-206">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-206">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d8b33-207">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="d8b33-207">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d8b33-208">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d8b33-208">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d8b33-209">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d8b33-209">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d8b33-210">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="d8b33-210">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d8b33-211">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="d8b33-211">Current directory and content root</span></span>

<span data-ttu-id="d8b33-212">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-212">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d8b33-213">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="d8b33-213">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d8b33-214">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-214">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="d8b33-215">Použití ContentRootPath nebo ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="d8b33-215">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="d8b33-216">K vyhledání prostředků aplikace použijte [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) nebo <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="d8b33-217">Když aplikace běží jako služba, nastaví na <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="d8b33-217">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="d8b33-218">Soubory výchozích nastavení aplikace, *appSettings. JSON* a *appSettings. { Environment}. JSON*jsou načteny z kořenu obsahu aplikace voláním [CreateDefaultBuilder během vytváření hostitele](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="d8b33-218">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="d8b33-219">Pro jiné soubory nastavení načtené vývojářským kódem v nástroji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> není nutné volat <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-219">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="d8b33-220">V následujícím příkladu soubor *custom_settings. JSON* existuje v kořenu obsahu aplikace a je načtený bez explicitního nastavení základní cesty:</span><span class="sxs-lookup"><span data-stu-id="d8b33-220">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="d8b33-221">Nepokoušejte se použít <xref:System.IO.Directory.GetCurrentDirectory*> k získání cesty prostředku, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* jako svůj aktuální adresář.</span><span class="sxs-lookup"><span data-stu-id="d8b33-221">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d8b33-222">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="d8b33-222">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d8b33-223"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="d8b33-223">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d8b33-224">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="d8b33-224">Troubleshoot</span></span>

<span data-ttu-id="d8b33-225">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-225">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d8b33-226">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="d8b33-226">Common errors</span></span>

* <span data-ttu-id="d8b33-227">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="d8b33-227">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d8b33-228">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-228">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d8b33-229">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="d8b33-229">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d8b33-230">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="d8b33-230">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d8b33-231">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-231">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d8b33-232">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d8b33-233">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="d8b33-233">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d8b33-234">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="d8b33-234">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d8b33-235">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="d8b33-235">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d8b33-236">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-236">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d8b33-237">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-237">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d8b33-238">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d8b33-238">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d8b33-239">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="d8b33-239">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d8b33-240">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="d8b33-240">System and Application Event Logs</span></span>

<span data-ttu-id="d8b33-241">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-241">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d8b33-242">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-242">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d8b33-243">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-243">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d8b33-244">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-244">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d8b33-245">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-245">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d8b33-246">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8b33-246">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d8b33-247">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="d8b33-247">Run the app at a command prompt</span></span>

<span data-ttu-id="d8b33-248">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-248">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d8b33-249">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-249">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d8b33-250">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d8b33-250">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d8b33-251">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="d8b33-251">Clear package caches</span></span>

<span data-ttu-id="d8b33-252">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d8b33-252">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d8b33-253">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-253">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d8b33-254">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="d8b33-254">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d8b33-255">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-255">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d8b33-256">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="d8b33-256">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d8b33-257">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-257">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d8b33-258">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d8b33-258">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d8b33-259">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="d8b33-259">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d8b33-260">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="d8b33-260">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d8b33-261">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="d8b33-261">Slow or hanging app</span></span>

<span data-ttu-id="d8b33-262">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-262">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d8b33-263">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="d8b33-263">App crashes or encounters an exception</span></span>

<span data-ttu-id="d8b33-264">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d8b33-264">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d8b33-265">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-265">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d8b33-266">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-266">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d8b33-267">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="d8b33-267">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d8b33-268">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d8b33-268">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d8b33-269">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d8b33-269">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d8b33-270">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-270">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d8b33-271">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="d8b33-271">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d8b33-272">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="d8b33-272">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d8b33-273">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="d8b33-273">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d8b33-274">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="d8b33-274">Analyze the dump</span></span>

<span data-ttu-id="d8b33-275">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="d8b33-275">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d8b33-276">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d8b33-276">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8b33-277">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d8b33-277">Additional resources</span></span>

* <span data-ttu-id="d8b33-278">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="d8b33-278">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d8b33-279">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="d8b33-279">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d8b33-280">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="d8b33-280">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d8b33-281">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8b33-281">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8b33-282">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d8b33-282">Prerequisites</span></span>

* [<span data-ttu-id="d8b33-283">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d8b33-283">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d8b33-284">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d8b33-284">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d8b33-285">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="d8b33-285">App configuration</span></span>

<span data-ttu-id="d8b33-286">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="d8b33-286">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d8b33-287">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-287">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d8b33-288">Zkontrolujte, zda je ladicí program připojen nebo zda `--console` je k dispozici přepínač.</span><span class="sxs-lookup"><span data-stu-id="d8b33-288">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d8b33-289">Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-289">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d8b33-290">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="d8b33-290">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d8b33-291">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-291">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d8b33-292">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="d8b33-292">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d8b33-293">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-293">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d8b33-294">Tento krok se provádí před konfigurací aplikace v nástroji `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-294">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d8b33-295">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-295">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d8b33-296">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je odebrán z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="d8b33-296">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d8b33-297">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-297">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d8b33-298">Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-298">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d8b33-299">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro zpracování událostí životního cyklu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-299">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d8b33-300">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-300">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d8b33-301">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="d8b33-301">Deployment type</span></span>

<span data-ttu-id="d8b33-302">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d8b33-302">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d8b33-303">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="d8b33-303">SDK</span></span>

<span data-ttu-id="d8b33-304">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-304">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d8b33-305">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-305">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d8b33-306">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-306">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d8b33-307">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-307">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d8b33-308">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="d8b33-308">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d8b33-309">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="d8b33-309">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d8b33-310">V následujícím příkladu je identifikátor RID nastaven na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-310">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d8b33-311">`<SelfContained>`Vlastnost je nastavena na hodnotu `false` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-311">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d8b33-312">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor (*. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8b33-312">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d8b33-313">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="d8b33-313">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d8b33-314">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-314">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d8b33-315">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-315">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d8b33-316">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-316">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d8b33-317">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8b33-317">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d8b33-318">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-318">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d8b33-319">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="d8b33-319">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d8b33-320">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="d8b33-320">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d8b33-321">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d8b33-321">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d8b33-322">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d8b33-322">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d8b33-323">`<SelfContained>`Vlastnost je nastavena na hodnotu `true` :</span><span class="sxs-lookup"><span data-stu-id="d8b33-323">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d8b33-324">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-324">Service user account</span></span>

<span data-ttu-id="d8b33-325">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="d8b33-325">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d8b33-326">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="d8b33-326">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-327">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d8b33-327">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d8b33-328">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-328">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d8b33-329">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-329">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d8b33-330">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="d8b33-330">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d8b33-331">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-331">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d8b33-332">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d8b33-332">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d8b33-333">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-333">Log on as a service rights</span></span>

<span data-ttu-id="d8b33-334">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="d8b33-334">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d8b33-335">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d8b33-335">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d8b33-336">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-336">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d8b33-337">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-337">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d8b33-338">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-338">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d8b33-339">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d8b33-339">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d8b33-340">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="d8b33-340">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d8b33-341">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-341">Select **Advanced**.</span></span> <span data-ttu-id="d8b33-342">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-342">Select **Find Now**.</span></span> <span data-ttu-id="d8b33-343">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="d8b33-343">Select the user account from the list.</span></span> <span data-ttu-id="d8b33-344">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-344">Select **OK**.</span></span> <span data-ttu-id="d8b33-345">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="d8b33-345">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d8b33-346">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-346">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d8b33-347">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="d8b33-347">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d8b33-348">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-348">Create a service</span></span>

<span data-ttu-id="d8b33-349">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d8b33-349">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d8b33-350">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d8b33-350">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d8b33-351">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-351">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d8b33-352">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-352">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d8b33-353">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="d8b33-353">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d8b33-354">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-354">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d8b33-355">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-355">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d8b33-356">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-356">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d8b33-357">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="d8b33-357">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d8b33-358">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-358">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d8b33-359">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-359">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d8b33-360">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-360">Start a service</span></span>

<span data-ttu-id="d8b33-361">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-361">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-362">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="d8b33-362">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d8b33-363">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-363">Determine a service's status</span></span>

<span data-ttu-id="d8b33-364">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-364">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-365">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="d8b33-365">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d8b33-366">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-366">Stop a service</span></span>

<span data-ttu-id="d8b33-367">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-367">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d8b33-368">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-368">Remove a service</span></span>

<span data-ttu-id="d8b33-369">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-369">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d8b33-370">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="d8b33-370">Handle starting and stopping events</span></span>

<span data-ttu-id="d8b33-371">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> událostí, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="d8b33-371">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d8b33-372">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting` `OnStarted` `OnStopping` metodami, a:</span><span class="sxs-lookup"><span data-stu-id="d8b33-372">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d8b33-373">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která předá `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="d8b33-373">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d8b33-374">V `Program.Main` volejte `RunAsCustomService` metodu rozšíření místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="d8b33-374">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d8b33-375">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v nástroji `Program.Main` , podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-375">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d8b33-376">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d8b33-376">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d8b33-377">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-377">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d8b33-378">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d8b33-378">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d8b33-379">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="d8b33-379">Configure endpoints</span></span>

<span data-ttu-id="d8b33-380">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-380">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d8b33-381">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-381">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d8b33-382">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="d8b33-382">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d8b33-383">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d8b33-383">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d8b33-384">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d8b33-384">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d8b33-385">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="d8b33-385">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d8b33-386">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="d8b33-386">Current directory and content root</span></span>

<span data-ttu-id="d8b33-387">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-387">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d8b33-388">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="d8b33-388">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d8b33-389">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-389">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d8b33-390">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="d8b33-390">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d8b33-391"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-391">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d8b33-392">Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-392">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d8b33-393">V nástroji `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-393">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d8b33-394">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="d8b33-394">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d8b33-395"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="d8b33-395">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d8b33-396">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="d8b33-396">Troubleshoot</span></span>

<span data-ttu-id="d8b33-397">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-397">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d8b33-398">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="d8b33-398">Common errors</span></span>

* <span data-ttu-id="d8b33-399">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="d8b33-399">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d8b33-400">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-400">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d8b33-401">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="d8b33-401">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d8b33-402">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="d8b33-402">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d8b33-403">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-403">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d8b33-404">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d8b33-405">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="d8b33-405">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d8b33-406">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="d8b33-406">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d8b33-407">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="d8b33-407">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d8b33-408">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-408">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d8b33-409">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-409">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d8b33-410">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d8b33-410">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d8b33-411">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="d8b33-411">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d8b33-412">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="d8b33-412">System and Application Event Logs</span></span>

<span data-ttu-id="d8b33-413">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-413">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d8b33-414">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-414">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d8b33-415">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-415">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d8b33-416">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-416">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d8b33-417">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-417">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d8b33-418">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8b33-418">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d8b33-419">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="d8b33-419">Run the app at a command prompt</span></span>

<span data-ttu-id="d8b33-420">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-420">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d8b33-421">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-421">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d8b33-422">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d8b33-422">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d8b33-423">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="d8b33-423">Clear package caches</span></span>

<span data-ttu-id="d8b33-424">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d8b33-424">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d8b33-425">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-425">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d8b33-426">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="d8b33-426">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d8b33-427">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-427">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d8b33-428">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="d8b33-428">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d8b33-429">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-429">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d8b33-430">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d8b33-430">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d8b33-431">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="d8b33-431">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d8b33-432">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="d8b33-432">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d8b33-433">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="d8b33-433">Slow or hanging app</span></span>

<span data-ttu-id="d8b33-434">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-434">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d8b33-435">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="d8b33-435">App crashes or encounters an exception</span></span>

<span data-ttu-id="d8b33-436">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d8b33-436">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d8b33-437">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-437">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d8b33-438">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-438">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d8b33-439">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="d8b33-439">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d8b33-440">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d8b33-440">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d8b33-441">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d8b33-441">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d8b33-442">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-442">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d8b33-443">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="d8b33-443">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d8b33-444">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="d8b33-444">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d8b33-445">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="d8b33-445">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d8b33-446">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="d8b33-446">Analyze the dump</span></span>

<span data-ttu-id="d8b33-447">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="d8b33-447">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d8b33-448">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d8b33-448">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8b33-449">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d8b33-449">Additional resources</span></span>

* <span data-ttu-id="d8b33-450">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="d8b33-450">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d8b33-451">Aplikace ASP.NET Core může být hostována ve Windows jako [služba systému Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez použití služby IIS.</span><span class="sxs-lookup"><span data-stu-id="d8b33-451">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d8b33-452">Po hostování jako služby systému Windows se aplikace automaticky spustí po restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="d8b33-452">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d8b33-453">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8b33-453">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8b33-454">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d8b33-454">Prerequisites</span></span>

* [<span data-ttu-id="d8b33-455">ASP.NET Core SDK 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d8b33-455">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d8b33-456">PowerShell 6,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d8b33-456">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d8b33-457">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="d8b33-457">App configuration</span></span>

<span data-ttu-id="d8b33-458">Aplikace vyžaduje odkazy na balíčky [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) a [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="d8b33-458">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d8b33-459">Pokud chcete testovat a ladit spouštění mimo službu, přidejte kód, který určí, jestli je aplikace spuštěná jako služba nebo Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-459">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d8b33-460">Zkontrolujte, zda je ladicí program připojen nebo zda `--console` je k dispozici přepínač.</span><span class="sxs-lookup"><span data-stu-id="d8b33-460">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d8b33-461">Pokud je jedna podmínka pravdivá (aplikace není spuštěna jako služba), zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-461">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d8b33-462">Pokud jsou podmínky nepravdivé (aplikace je spuštěná jako služba):</span><span class="sxs-lookup"><span data-stu-id="d8b33-462">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d8b33-463">Zavolejte <xref:System.IO.Directory.SetCurrentDirectory*> a použijte cestu k umístění publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-463">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d8b33-464">Nevolejte <xref:System.IO.Directory.GetCurrentDirectory*> pro získání cesty, protože aplikace služby systému Windows vrátí složku *C: \\ Windows \\ system32* , když <xref:System.IO.Directory.GetCurrentDirectory*> je volána.</span><span class="sxs-lookup"><span data-stu-id="d8b33-464">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d8b33-465">Další informace najdete v oddílu [aktuální adresář a kořenový adresář obsahu](#current-directory-and-content-root) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-465">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d8b33-466">Tento krok se provádí před konfigurací aplikace v nástroji `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-466">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d8b33-467">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ke spuštění aplikace jako služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-467">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d8b33-468">Vzhledem k tomu, že [zprostředkovatel konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) vyžaduje páry název-hodnota pro argumenty příkazového řádku, `--console` přepínač je odebrán z argumentů před <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> přijetím argumentů.</span><span class="sxs-lookup"><span data-stu-id="d8b33-468">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d8b33-469">Chcete-li zapisovat do protokolu událostí systému Windows, přidejte zprostředkovatele EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-469">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d8b33-470">Nastavte úroveň protokolování pomocí `Logging:LogLevel:Default` klíče v *appSettings. Soubor produkčního. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-470">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d8b33-471">V následujícím příkladu z ukázkové aplikace `RunAsCustomService` je volána místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pro zpracování událostí životního cyklu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-471">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d8b33-472">Další informace naleznete v části [popisovač spouštění a zastavování událostí](#handle-starting-and-stopping-events) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-472">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d8b33-473">Typ nasazení</span><span class="sxs-lookup"><span data-stu-id="d8b33-473">Deployment type</span></span>

<span data-ttu-id="d8b33-474">Informace a Rady týkající se scénářů nasazení najdete v tématu [nasazení aplikace .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="d8b33-474">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d8b33-475">Sada SDK</span><span class="sxs-lookup"><span data-stu-id="d8b33-475">SDK</span></span>

<span data-ttu-id="d8b33-476">U služby založené na webové aplikaci, která používá Razor stránky nebo architektury MVC, zadejte webovou sadu SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-476">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d8b33-477">Pokud služba spouští pouze úlohy na pozadí (například [hostované služby](xref:fundamentals/host/hosted-services)), zadejte sadu pracovních procesů sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-477">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d8b33-478">Nasazení závislé na rozhraní (FDD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-478">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d8b33-479">Nasazení závislé na rozhraní (FDD) spoléhá na přítomnost sdílené verze .NET Core v rámci systému v cílovém systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-479">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d8b33-480">Pokud je FDD scénář přijatý podle pokynů v tomto článku, sada SDK vytvoří spustitelný soubor (*. exe*), který se nazývá *spustitelný soubor závislý na rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="d8b33-480">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d8b33-481">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) obsahuje cílovou architekturu.</span><span class="sxs-lookup"><span data-stu-id="d8b33-481">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d8b33-482">V následujícím příkladu je identifikátor RID nastaven na `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-482">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d8b33-483">`<SelfContained>`Vlastnost je nastavena na hodnotu `false` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-483">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d8b33-484">Tyto vlastnosti instruují sadu SDK, aby vygenerovala spustitelný soubor (*. exe*) pro Windows a aplikaci, která závisí na sdílené platformě .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8b33-484">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d8b33-485">`<UseAppHost>`Vlastnost je nastavena na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-485">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="d8b33-486">Tato vlastnost poskytuje službě aktivační cestu (spustitelný soubor *. exe*) pro FDD.</span><span class="sxs-lookup"><span data-stu-id="d8b33-486">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="d8b33-487">Soubor *Web. config* , který je obvykle vytvořen při publikování aplikace ASP.NET Core, není pro aplikaci pro Windows nezbytný.</span><span class="sxs-lookup"><span data-stu-id="d8b33-487">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d8b33-488">Chcete-li zakázat vytvoření souboru *Web. config* , přidejte `<IsTransformWebConfigDisabled>` vlastnost nastavenou na `true` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-488">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d8b33-489">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-489">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d8b33-490">Samostatně zahrnuté nasazení (SCD) se nespoléhá na přítomnost sdílené architektury v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-490">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d8b33-491">Modul runtime a závislosti aplikace se nasazují s aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8b33-491">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d8b33-492">Identifikátor prostředí Windows [runtime (RID)](/dotnet/core/rid-catalog) je součástí `<PropertyGroup>` , který obsahuje cílovou architekturu:</span><span class="sxs-lookup"><span data-stu-id="d8b33-492">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d8b33-493">Publikování pro několik identifikátorů RID:</span><span class="sxs-lookup"><span data-stu-id="d8b33-493">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d8b33-494">Zadejte identifikátorů RID do seznamu středníkem oddělených.</span><span class="sxs-lookup"><span data-stu-id="d8b33-494">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d8b33-495">Použijte název vlastnosti [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="d8b33-495">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d8b33-496">Další informace najdete v tématu [katalog identifikátorů RID .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="d8b33-496">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d8b33-497">`<SelfContained>`Vlastnost je nastavena na hodnotu `true` :</span><span class="sxs-lookup"><span data-stu-id="d8b33-497">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d8b33-498">Uživatelský účet služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-498">Service user account</span></span>

<span data-ttu-id="d8b33-499">Chcete-li vytvořit uživatelský účet pro službu, použijte rutinu [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z příkazového prostředí PowerShellu pro správu 6.</span><span class="sxs-lookup"><span data-stu-id="d8b33-499">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d8b33-500">Ve Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763) nebo novější:</span><span class="sxs-lookup"><span data-stu-id="d8b33-500">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-501">V operačním systému Windows starším než Windows 10 říjen 2018 Update (verze 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="d8b33-501">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d8b33-502">Po zobrazení výzvy zadejte [silné heslo](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-502">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d8b33-503">Pokud `-AccountExpires` není parametr zadán rutině [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) s vypršenou platností <xref:System.DateTime> , platnost účtu nekončí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-503">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d8b33-504">Další informace najdete v tématu [uživatelské účty](/windows/desktop/services/service-user-accounts) [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) a Service.</span><span class="sxs-lookup"><span data-stu-id="d8b33-504">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d8b33-505">Alternativním přístupem ke správě uživatelů při používání služby Active Directory je použití účtů spravované služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-505">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d8b33-506">Další informace najdete v tématu [Přehled skupinových účtů spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="d8b33-506">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d8b33-507">Přihlášení jako práva služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-507">Log on as a service rights</span></span>

<span data-ttu-id="d8b33-508">Chcete-li vytvořit oprávnění *Přihlásit se jako služba* pro uživatelský účet služby:</span><span class="sxs-lookup"><span data-stu-id="d8b33-508">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d8b33-509">Spuštěním nástroje *secpol. msc*otevřete Editor místních zásad zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d8b33-509">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d8b33-510">Rozbalte uzel **místní zásady** a vyberte **přiřazení uživatelských práv**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-510">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d8b33-511">Otevřete zásadu **Přihlásit se jako služba** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-511">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d8b33-512">Vyberte **Přidat uživatele nebo skupinu**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-512">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d8b33-513">Zadejte název objektu (uživatelský účet) pomocí některého z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d8b33-513">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d8b33-514">Do pole název objektu zadejte uživatelský účet ( `{DOMAIN OR COMPUTER NAME\USER}` ) a výběrem **OK** přidejte uživatele do zásad.</span><span class="sxs-lookup"><span data-stu-id="d8b33-514">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d8b33-515">Vyberte **Upřesnit**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-515">Select **Advanced**.</span></span> <span data-ttu-id="d8b33-516">Vyberte **Najít hned**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-516">Select **Find Now**.</span></span> <span data-ttu-id="d8b33-517">V seznamu vyberte uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="d8b33-517">Select the user account from the list.</span></span> <span data-ttu-id="d8b33-518">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8b33-518">Select **OK**.</span></span> <span data-ttu-id="d8b33-519">Znovu vyberte **OK** a přidejte uživatele k zásadám.</span><span class="sxs-lookup"><span data-stu-id="d8b33-519">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d8b33-520">Potvrďte změny kliknutím na **tlačítko OK** nebo **použít** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-520">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d8b33-521">Vytvoření a Správa služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="d8b33-521">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d8b33-522">Vytvoření služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-522">Create a service</span></span>

<span data-ttu-id="d8b33-523">K registraci služby použijte příkazy prostředí PowerShell.</span><span class="sxs-lookup"><span data-stu-id="d8b33-523">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d8b33-524">V příkazovém prostředí PowerShellu pro správu 6 spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d8b33-524">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d8b33-525">`{EXE PATH}`: Cesta ke složce aplikace na hostiteli (například `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-525">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d8b33-526">Do cesty nezahrnujte spustitelný soubor aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-526">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d8b33-527">Koncové lomítko není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="d8b33-527">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d8b33-528">`{DOMAIN OR COMPUTER NAME\USER}`: Uživatelský účet služby (například `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-528">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d8b33-529">`{SERVICE NAME}`: Název služby (například `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-529">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d8b33-530">`{EXE FILE PATH}`: Cesta ke spustitelnému souboru aplikace (například `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-530">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d8b33-531">Zahrňte název spustitelného souboru s příponou.</span><span class="sxs-lookup"><span data-stu-id="d8b33-531">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d8b33-532">`{DESCRIPTION}`: Popis služby (například `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-532">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d8b33-533">`{DISPLAY NAME}`: Zobrazovaný název služby (například `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="d8b33-533">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d8b33-534">Spuštění služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-534">Start a service</span></span>

<span data-ttu-id="d8b33-535">Spusťte službu s následujícím příkazem prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-535">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-536">Spuštění služby trvá několik sekund.</span><span class="sxs-lookup"><span data-stu-id="d8b33-536">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d8b33-537">Určení stavu služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-537">Determine a service's status</span></span>

<span data-ttu-id="d8b33-538">Chcete-li zjistit stav služby, použijte následující příkaz prostředí PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-538">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d8b33-539">Stav je hlášen jako jedna z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="d8b33-539">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d8b33-540">Zastavení služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-540">Stop a service</span></span>

<span data-ttu-id="d8b33-541">Zastavte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-541">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d8b33-542">Odebrání služby</span><span class="sxs-lookup"><span data-stu-id="d8b33-542">Remove a service</span></span>

<span data-ttu-id="d8b33-543">Po krátké prodlevě pro zastavení služby odeberte službu pomocí následujícího příkazu PowerShellu 6:</span><span class="sxs-lookup"><span data-stu-id="d8b33-543">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d8b33-544">Zpracování událostí spouštění a zastavování</span><span class="sxs-lookup"><span data-stu-id="d8b33-544">Handle starting and stopping events</span></span>

<span data-ttu-id="d8b33-545">Pro zpracování <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> událostí, a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="d8b33-545">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d8b33-546">Vytvořte třídu, která je odvozena z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> s `OnStarting` `OnStarted` `OnStopping` metodami, a:</span><span class="sxs-lookup"><span data-stu-id="d8b33-546">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d8b33-547">Vytvořte metodu rozšíření pro <xref:Microsoft.AspNetCore.Hosting.IWebHost> , která předá `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="d8b33-547">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d8b33-548">V `Program.Main` volejte `RunAsCustomService` metodu rozšíření místo <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="d8b33-548">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d8b33-549">Chcete-li zobrazit umístění <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> v nástroji `Program.Main` , podívejte se na ukázku kódu zobrazenou v části [typ nasazení](#deployment-type) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-549">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d8b33-550">Scénáře proxy serveru a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="d8b33-550">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d8b33-551">Služby, které komunikují s požadavky z Internetu nebo podnikové sítě a jsou za proxy serverem nebo nástrojem pro vyrovnávání zatížení, můžou vyžadovat další konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-551">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d8b33-552">Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="d8b33-552">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d8b33-553">Konfigurace koncových bodů</span><span class="sxs-lookup"><span data-stu-id="d8b33-553">Configure endpoints</span></span>

<span data-ttu-id="d8b33-554">Ve výchozím nastavení ASP.NET Core váže `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-554">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d8b33-555">Nakonfigurujte adresu URL a port nastavením `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-555">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d8b33-556">Další přístupy k adresám URL a konfiguraci portů najdete v příslušném článku na serveru:</span><span class="sxs-lookup"><span data-stu-id="d8b33-556">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d8b33-557">Předchozí doprovodné materiály zahrnují podporu koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d8b33-557">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d8b33-558">Například můžete nakonfigurovat aplikaci pro protokol HTTPS při použití ověřování u služby systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d8b33-558">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d8b33-559">Použití vývojového certifikátu HTTPS ASP.NET Core k zabezpečení koncového bodu služby se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="d8b33-559">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d8b33-560">Aktuální adresář a kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="d8b33-560">Current directory and content root</span></span>

<span data-ttu-id="d8b33-561">Aktuální pracovní adresář vrácený voláním <xref:System.IO.Directory.GetCurrentDirectory*> pro službu systému Windows je složka *C: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-561">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d8b33-562">Složka *system32* není vhodným umístěním pro ukládání souborů služby (například souborů nastavení).</span><span class="sxs-lookup"><span data-stu-id="d8b33-562">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d8b33-563">Pomocí jednoho z následujících přístupů můžete spravovat a přistupovat k souborům assetů a nastavení služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-563">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d8b33-564">Nastavení kořenové cesty obsahu ke složce aplikace</span><span class="sxs-lookup"><span data-stu-id="d8b33-564">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d8b33-565"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>Je stejná Cesta zadaná `binPath` argumentu při vytvoření služby.</span><span class="sxs-lookup"><span data-stu-id="d8b33-565">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d8b33-566">Namísto volání `GetCurrentDirectory` k vytváření cest k souborům nastavení volejte <xref:System.IO.Directory.SetCurrentDirectory*> s cestou k [kořenu obsahu](xref:fundamentals/index#content-root)aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-566">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d8b33-567">V nástroji `Program.Main` Určete cestu ke složce spustitelného souboru služby a použijte cestu k vytvoření kořene obsahu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-567">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d8b33-568">Uložení souborů služby do vhodného umístění na disku</span><span class="sxs-lookup"><span data-stu-id="d8b33-568">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d8b33-569"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Při použití <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do složky obsahující soubory zadejte absolutní cestu.</span><span class="sxs-lookup"><span data-stu-id="d8b33-569">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d8b33-570">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="d8b33-570">Troubleshoot</span></span>

<span data-ttu-id="d8b33-571">Řešení potíží s aplikací služby systému Windows najdete v tématu <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="d8b33-571">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d8b33-572">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="d8b33-572">Common errors</span></span>

* <span data-ttu-id="d8b33-573">Stará nebo předběžná verze PowerShellu se používá.</span><span class="sxs-lookup"><span data-stu-id="d8b33-573">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d8b33-574">Registrovaná služba nepoužívá **publikovaný** výstup aplikace z příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="d8b33-574">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d8b33-575">Výstup příkazu [dotnet Build](/dotnet/core/tools/dotnet-build) není pro nasazení aplikace podporovaný.</span><span class="sxs-lookup"><span data-stu-id="d8b33-575">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d8b33-576">Publikované assety se nacházejí v některé z následujících složek v závislosti na typu nasazení:</span><span class="sxs-lookup"><span data-stu-id="d8b33-576">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d8b33-577">*bin/verze/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-577">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d8b33-578">*bin/verze/{Target Framework} identifikátor/{runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="d8b33-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d8b33-579">Služba není ve stavu SPUŠTĚNo.</span><span class="sxs-lookup"><span data-stu-id="d8b33-579">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d8b33-580">Cesty k prostředkům, které aplikace používá (například certifikáty), jsou nesprávné.</span><span class="sxs-lookup"><span data-stu-id="d8b33-580">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d8b33-581">Základní cesta služby systému Windows je *c: \\ Windows \\ system32*.</span><span class="sxs-lookup"><span data-stu-id="d8b33-581">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d8b33-582">Uživatel nemá oprávnění *Přihlásit se jako služba* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-582">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d8b33-583">Při provádění příkazu PowerShellu vypršela platnost hesla uživatele nebo se předala nesprávně `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-583">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d8b33-584">Aplikace vyžaduje ASP.NET Core ověřování, ale není nakonfigurovaná pro zabezpečená připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d8b33-584">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d8b33-585">Port adresy URL požadavku není správný nebo v aplikaci není správně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="d8b33-585">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d8b33-586">Protokoly událostí systému a aplikace</span><span class="sxs-lookup"><span data-stu-id="d8b33-586">System and Application Event Logs</span></span>

<span data-ttu-id="d8b33-587">Přístup k protokolům událostí systému a aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-587">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d8b33-588">Otevřete nabídku Start, vyhledejte *Prohlížeč událostí*a vyberte aplikaci **Prohlížeč událostí** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-588">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d8b33-589">V **Prohlížeč událostí**otevřete uzel **protokoly systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="d8b33-589">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d8b33-590">Vyberte **systém** a otevřete protokol událostí systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-590">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d8b33-591">Výběrem **aplikace** otevřete protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-591">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d8b33-592">Vyhledá chyby spojené s chybnou aplikací.</span><span class="sxs-lookup"><span data-stu-id="d8b33-592">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d8b33-593">Spuštění aplikace na příkazovém řádku</span><span class="sxs-lookup"><span data-stu-id="d8b33-593">Run the app at a command prompt</span></span>

<span data-ttu-id="d8b33-594">Mnoho chyb při spuštění nevytváří užitečné informace v protokolech událostí.</span><span class="sxs-lookup"><span data-stu-id="d8b33-594">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d8b33-595">Příčinu některých chyb můžete najít spuštěním aplikace na příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="d8b33-595">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d8b33-596">Pokud chcete protokolovat další podrobnosti z aplikace, snižte [úroveň protokolu](xref:fundamentals/logging/index#log-level) nebo spusťte aplikaci ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d8b33-596">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d8b33-597">Vymazat mezipaměti balíčků</span><span class="sxs-lookup"><span data-stu-id="d8b33-597">Clear package caches</span></span>

<span data-ttu-id="d8b33-598">Funkční aplikace může po upgradu .NET Core SDK ve vývojovém počítači nebo změně verzí balíčku v rámci aplikace selhat okamžitě.</span><span class="sxs-lookup"><span data-stu-id="d8b33-598">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d8b33-599">V některých případech můžou nesouvislé balíčky při provádění hlavních upgradů poškodit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-599">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d8b33-600">Většinu těchto problémů můžete vyřešit pomocí následujících pokynů:</span><span class="sxs-lookup"><span data-stu-id="d8b33-600">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d8b33-601">Odstraňte složky *bin* a *obj* .</span><span class="sxs-lookup"><span data-stu-id="d8b33-601">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d8b33-602">Pomocí příkazu [dotnet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) z příkazového prostředí vymažte mezipaměť balíčku.</span><span class="sxs-lookup"><span data-stu-id="d8b33-602">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d8b33-603">Mazání mezipamětí balíčků lze také provést pomocí nástroje [NuGet. exe](https://www.nuget.org/downloads) a provedením příkazu `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-603">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d8b33-604">*NuGet. exe* není sada instalovaná instalace s desktopovým operačním systémem Windows a musí se získat samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="d8b33-604">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d8b33-605">Obnovte a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="d8b33-605">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d8b33-606">Před opětovným nasazením aplikace odstraňte všechny soubory ve složce pro nasazení na serveru.</span><span class="sxs-lookup"><span data-stu-id="d8b33-606">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d8b33-607">Pomalá nebo zavěšená aplikace</span><span class="sxs-lookup"><span data-stu-id="d8b33-607">Slow or hanging app</span></span>

<span data-ttu-id="d8b33-608">*Výpis stavu* systému je snímek paměti systému a může vám pomůže určit příčinu selhání aplikace, selhání při spuštění nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8b33-608">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d8b33-609">Aplikace selže nebo dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="d8b33-609">App crashes or encounters an exception</span></span>

<span data-ttu-id="d8b33-610">Získat a analyzovat výpis z [zasílání zpráv o chybách systému Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="d8b33-610">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d8b33-611">Vytvořte složku uchovávající soubory s výpisem stavu systému `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="d8b33-611">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d8b33-612">Spusťte [skript PowerShellu EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) s názvem spustitelného souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="d8b33-612">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d8b33-613">Spusťte aplikaci za podmínek, které způsobí, že dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="d8b33-613">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d8b33-614">Po chybě spusťte [skript PowerShellu DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="d8b33-614">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d8b33-615">Po selhání aplikace a dokončení shromažďování výpisu je možné aplikaci ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="d8b33-615">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d8b33-616">Skript PowerShellu nakonfiguruje WER a shromáždí až pět výpisů paměti pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d8b33-616">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d8b33-617">Výpisy stavu systému můžou zabírat velké množství místa na disku (každý až několik gigabajtů).</span><span class="sxs-lookup"><span data-stu-id="d8b33-617">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d8b33-618">Aplikace přestane reagovat, dojde k chybě při spuštění nebo se spustí normálně.</span><span class="sxs-lookup"><span data-stu-id="d8b33-618">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d8b33-619">Když *aplikace přestane reagovat (zastaví* se, ale nejedná se o chybu), selže během spuštění nebo se spustí normálně, podívejte [se na soubory výpisu paměti v uživatelském režimu: zvolením nejlepšího](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) nástroje vyberte vhodný nástroj pro vytváření výpisu.</span><span class="sxs-lookup"><span data-stu-id="d8b33-619">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d8b33-620">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="d8b33-620">Analyze the dump</span></span>

<span data-ttu-id="d8b33-621">Výpis paměti lze analyzovat pomocí několika přístupů.</span><span class="sxs-lookup"><span data-stu-id="d8b33-621">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d8b33-622">Další informace najdete v tématu [Analýza souboru s výpisem stavu v uživatelském režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="d8b33-622">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8b33-623">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d8b33-623">Additional resources</span></span>

* <span data-ttu-id="d8b33-624">[Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (zahrnuje konfiguraci HTTPS a podporu sni)</span><span class="sxs-lookup"><span data-stu-id="d8b33-624">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
