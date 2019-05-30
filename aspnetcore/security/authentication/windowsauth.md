---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pro službu IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 05/29/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 9dfff5dcba409ddca7e05c771b864ab121e0ea85
ms.sourcegitcommit: 06c4f2910dd54ded25e1b8750e09c66578748bc9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66395926"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="4227a-103">Konfigurace ověřování Windows v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4227a-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="4227a-104">Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4227a-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4227a-105">[Ověřování Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) lze konfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="4227a-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="4227a-106">Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4227a-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="4227a-107">Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identity služby Active Directory domény nebo účty Windows k identifikaci uživatelů.</span><span class="sxs-lookup"><span data-stu-id="4227a-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="4227a-108">Ověřování Windows je nejvhodnější pro prostředí intranetu, kde uživatelé klientských aplikací a webové servery patří do stejné domény Windows.</span><span class="sxs-lookup"><span data-stu-id="4227a-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="launch-settings-debugger"></a><span data-ttu-id="4227a-109">Spusťte nastavení (ladicí program)</span><span class="sxs-lookup"><span data-stu-id="4227a-109">Launch settings (debugger)</span></span>

<span data-ttu-id="4227a-110">Konfigurace nastavení spuštění má vliv pouze *Properties/launchSettings.json* souboru a neprovede konfiguraci serveru služby IIS nebo ovladač HTTP.sys pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="4227a-110">Configuration for launch settings only affects the *Properties/launchSettings.json* file and doesn't configure the IIS or HTTP.sys server for Windows Authentication.</span></span> <span data-ttu-id="4227a-111">Konfigurace serveru je podrobně [povolit ověřování služby IIS nebo ovladač HTTP.sys](#authentication-services-for-iis-or-httpsys) oddílu.</span><span class="sxs-lookup"><span data-stu-id="4227a-111">Configuration of the server is explained in the [Enable authentication services for IIS or HTTP.sys](#authentication-services-for-iis-or-httpsys) section.</span></span>

<span data-ttu-id="4227a-112">**Webovou aplikaci** šablony, které jsou k dispozici prostřednictvím sady Visual Studio nebo rozhraní příkazového řádku .NET Core může být nakonfigurované pro podporu ověřování Windows, která aktualizuje *Properties/launchSettings.json* souboru automaticky.</span><span class="sxs-lookup"><span data-stu-id="4227a-112">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4227a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4227a-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4227a-114">**Nový projekt**</span><span class="sxs-lookup"><span data-stu-id="4227a-114">**New project**</span></span>

1. <span data-ttu-id="4227a-115">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="4227a-115">Create a new project.</span></span>
1. <span data-ttu-id="4227a-116">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4227a-116">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="4227a-117">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="4227a-117">Select **Next**.</span></span>
1. <span data-ttu-id="4227a-118">Zadejte název **název projektu** pole.</span><span class="sxs-lookup"><span data-stu-id="4227a-118">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="4227a-119">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="4227a-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4227a-120">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4227a-120">Select **Create**.</span></span>
1. <span data-ttu-id="4227a-121">Vyberte **změnu** pod **ověřování**.</span><span class="sxs-lookup"><span data-stu-id="4227a-121">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="4227a-122">V **změna ověřování** okně **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="4227a-122">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="4227a-123">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="4227a-123">Select **OK**.</span></span>
1. <span data-ttu-id="4227a-124">Vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="4227a-124">Select **Web Application**.</span></span>
1. <span data-ttu-id="4227a-125">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="4227a-125">Select **Create**.</span></span>

<span data-ttu-id="4227a-126">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4227a-126">Run the app.</span></span> <span data-ttu-id="4227a-127">Uživatelské jméno se zobrazí v uživatelském rozhraní vygenerované aplikace.</span><span class="sxs-lookup"><span data-stu-id="4227a-127">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="4227a-128">**Existující projekt**</span><span class="sxs-lookup"><span data-stu-id="4227a-128">**Existing project**</span></span>

<span data-ttu-id="4227a-129">Vlastnosti projektu povolit ověřování Windows a vypnutí anonymního ověřování:</span><span class="sxs-lookup"><span data-stu-id="4227a-129">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="4227a-130">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="4227a-130">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="4227a-131">Vyberte **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="4227a-131">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="4227a-132">Zrušte zaškrtnutí políčka pro **povolit anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="4227a-132">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="4227a-133">Zaškrtněte políčko pro **povolit ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="4227a-133">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="4227a-134">Uložit a zavřít jeho stránku vlastností.</span><span class="sxs-lookup"><span data-stu-id="4227a-134">Save and close the property page.</span></span>

<span data-ttu-id="4227a-135">Alternativně se dá nakonfigurovat vlastnosti v `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="4227a-135">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="4227a-136">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4227a-136">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="4227a-137">**Nový projekt**</span><span class="sxs-lookup"><span data-stu-id="4227a-137">**New project**</span></span>

<span data-ttu-id="4227a-138">Spustit [dotnet nové](/dotnet/core/tools/dotnet-new) příkazů `webapp` argument (webové aplikace ASP.NET Core) a `--auth Windows` přepínače:</span><span class="sxs-lookup"><span data-stu-id="4227a-138">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="4227a-139">**Existující projekt**</span><span class="sxs-lookup"><span data-stu-id="4227a-139">**Existing project**</span></span>

<span data-ttu-id="4227a-140">Aktualizace `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="4227a-140">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="4227a-141">Při úpravě existujícího projektu, přesvědčte se, že soubor projektu obsahuje odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) **nebo** [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="4227a-141">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

## <a name="authentication-services-for-iis-or-httpsys"></a><span data-ttu-id="4227a-142">Ověřování služby IIS nebo ovladač HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4227a-142">Authentication services for IIS or HTTP.sys</span></span>

<span data-ttu-id="4227a-143">V závislosti na scénáři hostování, postupujte podle pokynů v **buď** [IIS](#iis) části **nebo** [HTTP.sys](#httpsys) oddílu.</span><span class="sxs-lookup"><span data-stu-id="4227a-143">Depending on the hosting scenario, follow the guidance in **either** the [IIS](#iis) section **or** [HTTP.sys](#httpsys) section.</span></span>

### <a name="iis"></a><span data-ttu-id="4227a-144">IIS</span><span class="sxs-lookup"><span data-stu-id="4227a-144">IIS</span></span>

<span data-ttu-id="4227a-145">Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> oboru názvů) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4227a-145">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="4227a-146">Služba IIS použije [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4227a-146">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="4227a-147">Ověřování Windows je nakonfigurovaný pro službu IIS prostřednictvím *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="4227a-147">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="4227a-148">V následujících částech zobrazit postup:</span><span class="sxs-lookup"><span data-stu-id="4227a-148">The following sections show how to:</span></span>

* <span data-ttu-id="4227a-149">Zadejte místní *web.config* soubor, který aktivuje ověřování Windows na serveru při nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="4227a-149">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="4227a-150">Pomocí Správce služby IIS ke konfiguraci *web.config* souboru aplikace v ASP.NET Core, která již byla nasazena na server.</span><span class="sxs-lookup"><span data-stu-id="4227a-150">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="4227a-151">Pokud jste tak již neučinili, povolte službu IIS pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4227a-151">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="4227a-152">Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="4227a-152">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="4227a-153">Povolte službu Role služby IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="4227a-153">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="4227a-154">Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="4227a-154">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="4227a-155">[Middleware pro integraci služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti.</span><span class="sxs-lookup"><span data-stu-id="4227a-155">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="4227a-156">Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: Možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="4227a-156">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="4227a-157">Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace.</span><span class="sxs-lookup"><span data-stu-id="4227a-157">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="4227a-158">Další informace najdete v tématu [odkaz na modul ASP.NET Core konfigurace: Atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="4227a-158">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="4227a-159">Použití **buď** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="4227a-159">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="4227a-160">**Před publikováním a nasazování projektu,** přidejte následující *web.config* souboru do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="4227a-160">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="4227a-161">Když se publikuje projektu .NET Core SDK (bez `<IsTransformWebConfigDisabled>` vlastnost nastavena na hodnotu `true` v souboru projektu), publikovanému *web.config* soubor obsahuje `<location><system.webServer><security><authentication>` části.</span><span class="sxs-lookup"><span data-stu-id="4227a-161">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="4227a-162">Další informace o `<IsTransformWebConfigDisabled>` vlastnost, naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="4227a-162">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="4227a-163">**Po publikování a nasazení projektu,** provedení konfigurace na straně serveru pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="4227a-163">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="4227a-164">Ve Správci služby IIS vyberte web služby IIS v části **lokality** uzlu **připojení** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="4227a-164">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="4227a-165">Dvakrát klikněte na panel **ověřování** v **IIS** oblasti.</span><span class="sxs-lookup"><span data-stu-id="4227a-165">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="4227a-166">Vyberte **anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="4227a-166">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="4227a-167">Vyberte **zakázat** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="4227a-167">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="4227a-168">Vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="4227a-168">Select **Windows Authentication**.</span></span> <span data-ttu-id="4227a-169">Vyberte **povolit** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="4227a-169">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="4227a-170">Když se provedou tyto akce, Správce služby IIS upraví aplikace *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="4227a-170">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="4227a-171">A `<system.webServer><security><authentication>` s aktualizovaným nastavením pro přidání uzlu `anonymousAuthentication` a `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="4227a-171">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="4227a-172">`<system.webServer>` Přidá do části *web.config* soubor pomocí Správce služby IIS je mimo aplikaci prvku `<location>` části přidá .NET Core SDK, když je aplikace publikována.</span><span class="sxs-lookup"><span data-stu-id="4227a-172">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="4227a-173">Vzhledem k tomu, že v části Přidání mimo `<location>` uzlu nastavení dědí žádné [dílčí aplikace](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace.</span><span class="sxs-lookup"><span data-stu-id="4227a-173">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="4227a-174">Chcete-li zabránit dědění, přesuňte přidaného `<security>` části uvnitř `<location><system.webServer>` části, která poskytuje sada .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="4227a-174">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="4227a-175">Když správce služby IIS se používá k přidání konfigurace služby IIS, ovlivní pouze aplikace *web.config* souboru na serveru.</span><span class="sxs-lookup"><span data-stu-id="4227a-175">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="4227a-176">Následné nasazení aplikace může přepsat nastavení na serveru, pokud na server kopii *web.config* nahrazuje projektu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="4227a-176">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="4227a-177">Použití **buď** z následujících postupů můžete spravovat nastavení:</span><span class="sxs-lookup"><span data-stu-id="4227a-177">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="4227a-178">Pomocí Správce služby IIS k resetování nastavení ve *web.config* souboru po souboru se přepíše při nasazení.</span><span class="sxs-lookup"><span data-stu-id="4227a-178">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="4227a-179">Přidat *souboru web.config* do aplikace místně s nastavením.</span><span class="sxs-lookup"><span data-stu-id="4227a-179">Add a *web.config file* to the app locally with the settings.</span></span>

### <a name="httpsys"></a><span data-ttu-id="4227a-180">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4227a-180">HTTP.sys</span></span>

<span data-ttu-id="4227a-181">I když [Kestrel](xref:fundamentals/servers/kestrel) nepodporuje ověřování Windows, můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys) pro zajištění podpory scénářů v místním prostředí ve Windows.</span><span class="sxs-lookup"><span data-stu-id="4227a-181">Although [Kestrel](xref:fundamentals/servers/kestrel) doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span>

<span data-ttu-id="4227a-182">Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4227a-182">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="4227a-183">Konfigurace hostitele webové aplikace pomocí HTTP.sys ověřování Windows (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="4227a-183">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="4227a-184"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Probíhá <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="4227a-184"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="4227a-185">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="4227a-185">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="4227a-186">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="4227a-186">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="4227a-187">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="4227a-187">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="4227a-188">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="4227a-188">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="4227a-189">Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="4227a-189">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="4227a-190">Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="4227a-190">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="4227a-191">Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="4227a-191">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="4227a-192">Autorizace uživatelů</span><span class="sxs-lookup"><span data-stu-id="4227a-192">Authorize users</span></span>

<span data-ttu-id="4227a-193">Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4227a-193">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="4227a-194">Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.</span><span class="sxs-lookup"><span data-stu-id="4227a-194">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="4227a-195">Zakázat anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="4227a-195">Disallow anonymous access</span></span>

<span data-ttu-id="4227a-196">Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek.</span><span class="sxs-lookup"><span data-stu-id="4227a-196">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="4227a-197">Pokud se k webu služby IIS je konfigurována tak anonymní přístup, požadavek nikdy dosáhne aplikace.</span><span class="sxs-lookup"><span data-stu-id="4227a-197">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="4227a-198">Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.</span><span class="sxs-lookup"><span data-stu-id="4227a-198">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="4227a-199">Povolení anonymního přístupu</span><span class="sxs-lookup"><span data-stu-id="4227a-199">Allow anonymous access</span></span>

<span data-ttu-id="4227a-200">Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy.</span><span class="sxs-lookup"><span data-stu-id="4227a-200">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="4227a-201">`[Authorize]` Atribut umožňuje zabezpečené koncové body aplikace, které vyžadují ověřování.</span><span class="sxs-lookup"><span data-stu-id="4227a-201">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="4227a-202">`[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut v aplikacích, které povolí anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="4227a-202">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="4227a-203">Podrobnosti o použití atributu, naleznete v tématu <xref:security/authorization/simple>.</span><span class="sxs-lookup"><span data-stu-id="4227a-203">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="4227a-204">Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="4227a-204">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="4227a-205">[StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".</span><span class="sxs-lookup"><span data-stu-id="4227a-205">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="4227a-206">Zosobnění</span><span class="sxs-lookup"><span data-stu-id="4227a-206">Impersonation</span></span>

<span data-ttu-id="4227a-207">ASP.NET Core neimplementuje zosobnění.</span><span class="sxs-lookup"><span data-stu-id="4227a-207">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="4227a-208">Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu.</span><span class="sxs-lookup"><span data-stu-id="4227a-208">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="4227a-209">Pokud aplikace musí provést akce jménem uživatele, použijte [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [terminálu vložené middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4227a-209">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="4227a-210">V tomto kontextu spuštění jedné akce a potom zavřete kontextu.</span><span class="sxs-lookup"><span data-stu-id="4227a-210">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="4227a-211">`RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře.</span><span class="sxs-lookup"><span data-stu-id="4227a-211">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="4227a-212">Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.</span><span class="sxs-lookup"><span data-stu-id="4227a-212">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

## <a name="claims-transformations"></a><span data-ttu-id="4227a-213">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="4227a-213">Claims transformations</span></span>

<span data-ttu-id="4227a-214">Při hostování za nástrojem s režimem v procesu služby IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nevolá interně k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="4227a-214">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="4227a-215">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaném k transformaci deklarací identity po každém ověření není ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="4227a-215">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="4227a-216">Další informace a příklad kódu, který aktivuje transformace deklarací identity, při hostování v procesu najdete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="4227a-216">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4227a-217">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4227a-217">Additional resources</span></span>

* [<span data-ttu-id="4227a-218">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="4227a-218">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
