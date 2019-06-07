---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pro službu IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/05/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 900bbf5f14b1876ad537b2b77e4ba07d7aa168f2
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750168"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="7e277-103">Konfigurace ověřování Windows v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e277-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="7e277-104">Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7e277-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7e277-105">[Ověřování Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) lze konfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="7e277-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="7e277-106">Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7e277-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="7e277-107">Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identity služby Active Directory domény nebo účty Windows k identifikaci uživatelů.</span><span class="sxs-lookup"><span data-stu-id="7e277-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="7e277-108">Ověřování Windows je nejvhodnější pro prostředí intranetu, kde uživatelé klientských aplikací a webové servery patří do stejné domény Windows.</span><span class="sxs-lookup"><span data-stu-id="7e277-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="7e277-109">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="7e277-109">IIS/IIS Express</span></span>

<span data-ttu-id="7e277-110">Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> oboru názvů) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7e277-110">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="7e277-111">Spusťte nastavení (ladicí program)</span><span class="sxs-lookup"><span data-stu-id="7e277-111">Launch settings (debugger)</span></span>

<span data-ttu-id="7e277-112">Konfigurace nastavení spuštění má vliv pouze *Properties/launchSettings.json* souboru pro službu IIS Express a nebude konfigurace IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="7e277-112">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="7e277-113">Konfigurace serveru je podrobně [IIS](#iis) oddílu.</span><span class="sxs-lookup"><span data-stu-id="7e277-113">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="7e277-114">**Webovou aplikaci** šablony, které jsou k dispozici prostřednictvím sady Visual Studio nebo rozhraní příkazového řádku .NET Core může být nakonfigurované pro podporu ověřování Windows, která aktualizuje *Properties/launchSettings.json* souboru automaticky.</span><span class="sxs-lookup"><span data-stu-id="7e277-114">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7e277-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e277-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e277-116">**Nový projekt**</span><span class="sxs-lookup"><span data-stu-id="7e277-116">**New project**</span></span>

1. <span data-ttu-id="7e277-117">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="7e277-117">Create a new project.</span></span>
1. <span data-ttu-id="7e277-118">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7e277-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="7e277-119">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="7e277-119">Select **Next**.</span></span>
1. <span data-ttu-id="7e277-120">Zadejte název **název projektu** pole.</span><span class="sxs-lookup"><span data-stu-id="7e277-120">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="7e277-121">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="7e277-121">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="7e277-122">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="7e277-122">Select **Create**.</span></span>
1. <span data-ttu-id="7e277-123">Vyberte **změnu** pod **ověřování**.</span><span class="sxs-lookup"><span data-stu-id="7e277-123">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="7e277-124">V **změna ověřování** okně **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="7e277-124">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="7e277-125">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="7e277-125">Select **OK**.</span></span>
1. <span data-ttu-id="7e277-126">Vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="7e277-126">Select **Web Application**.</span></span>
1. <span data-ttu-id="7e277-127">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="7e277-127">Select **Create**.</span></span>

<span data-ttu-id="7e277-128">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e277-128">Run the app.</span></span> <span data-ttu-id="7e277-129">Uživatelské jméno se zobrazí v uživatelském rozhraní vygenerované aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e277-129">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="7e277-130">**Existující projekt**</span><span class="sxs-lookup"><span data-stu-id="7e277-130">**Existing project**</span></span>

<span data-ttu-id="7e277-131">Vlastnosti projektu povolit ověřování Windows a vypnutí anonymního ověřování:</span><span class="sxs-lookup"><span data-stu-id="7e277-131">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="7e277-132">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="7e277-132">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="7e277-133">Vyberte **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="7e277-133">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="7e277-134">Zrušte zaškrtnutí políčka pro **povolit anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="7e277-134">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="7e277-135">Zaškrtněte políčko pro **povolit ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="7e277-135">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="7e277-136">Uložit a zavřít jeho stránku vlastností.</span><span class="sxs-lookup"><span data-stu-id="7e277-136">Save and close the property page.</span></span>

<span data-ttu-id="7e277-137">Alternativně se dá nakonfigurovat vlastnosti v `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="7e277-137">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="7e277-138">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7e277-138">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="7e277-139">**Nový projekt**</span><span class="sxs-lookup"><span data-stu-id="7e277-139">**New project**</span></span>

<span data-ttu-id="7e277-140">Spustit [dotnet nové](/dotnet/core/tools/dotnet-new) příkazů `webapp` argument (webové aplikace ASP.NET Core) a `--auth Windows` přepínače:</span><span class="sxs-lookup"><span data-stu-id="7e277-140">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="7e277-141">**Existující projekt**</span><span class="sxs-lookup"><span data-stu-id="7e277-141">**Existing project**</span></span>

<span data-ttu-id="7e277-142">Aktualizace `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="7e277-142">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="7e277-143">Při úpravě existujícího projektu, přesvědčte se, že soubor projektu obsahuje odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) **nebo** [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="7e277-143">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="7e277-144">IIS</span><span class="sxs-lookup"><span data-stu-id="7e277-144">IIS</span></span>

<span data-ttu-id="7e277-145">Služba IIS použije [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7e277-145">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="7e277-146">Ověřování Windows je nakonfigurovaný pro službu IIS prostřednictvím *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="7e277-146">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="7e277-147">V následujících částech zobrazit postup:</span><span class="sxs-lookup"><span data-stu-id="7e277-147">The following sections show how to:</span></span>

* <span data-ttu-id="7e277-148">Zadejte místní *web.config* soubor, který aktivuje ověřování Windows na serveru při nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e277-148">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="7e277-149">Pomocí Správce služby IIS ke konfiguraci *web.config* souboru aplikace v ASP.NET Core, která již byla nasazena na server.</span><span class="sxs-lookup"><span data-stu-id="7e277-149">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="7e277-150">Pokud jste tak již neučinili, povolte službu IIS pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7e277-150">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="7e277-151">Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="7e277-151">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="7e277-152">Povolte službu Role služby IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="7e277-152">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="7e277-153">Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="7e277-153">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="7e277-154">[Middleware pro integraci služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti.</span><span class="sxs-lookup"><span data-stu-id="7e277-154">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="7e277-155">Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: Možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="7e277-155">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="7e277-156">Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e277-156">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="7e277-157">Další informace najdete v tématu [odkaz na modul ASP.NET Core konfigurace: Atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="7e277-157">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="7e277-158">Použití **buď** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="7e277-158">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="7e277-159">**Před publikováním a nasazování projektu,** přidejte následující *web.config* souboru do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="7e277-159">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="7e277-160">Když se publikuje projektu .NET Core SDK (bez `<IsTransformWebConfigDisabled>` vlastnost nastavena na hodnotu `true` v souboru projektu), publikovanému *web.config* soubor obsahuje `<location><system.webServer><security><authentication>` části.</span><span class="sxs-lookup"><span data-stu-id="7e277-160">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="7e277-161">Další informace o `<IsTransformWebConfigDisabled>` vlastnost, naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="7e277-161">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="7e277-162">**Po publikování a nasazení projektu,** provedení konfigurace na straně serveru pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="7e277-162">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="7e277-163">Ve Správci služby IIS vyberte web služby IIS v části **lokality** uzlu **připojení** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="7e277-163">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="7e277-164">Dvakrát klikněte na panel **ověřování** v **IIS** oblasti.</span><span class="sxs-lookup"><span data-stu-id="7e277-164">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="7e277-165">Vyberte **anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="7e277-165">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="7e277-166">Vyberte **zakázat** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="7e277-166">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="7e277-167">Vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="7e277-167">Select **Windows Authentication**.</span></span> <span data-ttu-id="7e277-168">Vyberte **povolit** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="7e277-168">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="7e277-169">Když se provedou tyto akce, Správce služby IIS upraví aplikace *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="7e277-169">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="7e277-170">A `<system.webServer><security><authentication>` s aktualizovaným nastavením pro přidání uzlu `anonymousAuthentication` a `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="7e277-170">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="7e277-171">`<system.webServer>` Přidá do části *web.config* soubor pomocí Správce služby IIS je mimo aplikaci prvku `<location>` části přidá .NET Core SDK, když je aplikace publikována.</span><span class="sxs-lookup"><span data-stu-id="7e277-171">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="7e277-172">Vzhledem k tomu, že v části Přidání mimo `<location>` uzlu nastavení dědí žádné [dílčí aplikace](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e277-172">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="7e277-173">Chcete-li zabránit dědění, přesuňte přidaného `<security>` části uvnitř `<location><system.webServer>` části, která poskytuje sada .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="7e277-173">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="7e277-174">Když správce služby IIS se používá k přidání konfigurace služby IIS, ovlivní pouze aplikace *web.config* souboru na serveru.</span><span class="sxs-lookup"><span data-stu-id="7e277-174">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="7e277-175">Následné nasazení aplikace může přepsat nastavení na serveru, pokud na server kopii *web.config* nahrazuje projektu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="7e277-175">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="7e277-176">Použití **buď** z následujících postupů můžete spravovat nastavení:</span><span class="sxs-lookup"><span data-stu-id="7e277-176">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="7e277-177">Pomocí Správce služby IIS k resetování nastavení ve *web.config* souboru po souboru se přepíše při nasazení.</span><span class="sxs-lookup"><span data-stu-id="7e277-177">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="7e277-178">Přidat *souboru web.config* do aplikace místně s nastavením.</span><span class="sxs-lookup"><span data-stu-id="7e277-178">Add a *web.config file* to the app locally with the settings.</span></span>

## <a name="httpsys"></a><span data-ttu-id="7e277-179">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7e277-179">HTTP.sys</span></span>

<span data-ttu-id="7e277-180">V místním prostředí scénáře [Kestrel](xref:fundamentals/servers/kestrel) nemá podporu ověřování Windows, ale vy můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="7e277-180">In self-hosted scenarios, [Kestrel](xref:fundamentals/servers/kestrel) doesn't support Windows Authentication, but you can use [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="7e277-181">Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7e277-181">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="7e277-182">Konfigurace hostitele webové aplikace pomocí HTTP.sys ověřování Windows (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="7e277-182">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="7e277-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Probíhá <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="7e277-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7e277-184">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="7e277-184">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="7e277-185">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="7e277-185">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="7e277-186">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="7e277-186">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="7e277-187">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e277-187">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="7e277-188">Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="7e277-188">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="7e277-189">Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="7e277-189">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="7e277-190">Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="7e277-190">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="7e277-191">Autorizace uživatelů</span><span class="sxs-lookup"><span data-stu-id="7e277-191">Authorize users</span></span>

<span data-ttu-id="7e277-192">Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7e277-192">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="7e277-193">Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.</span><span class="sxs-lookup"><span data-stu-id="7e277-193">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="7e277-194">Zakázat anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="7e277-194">Disallow anonymous access</span></span>

<span data-ttu-id="7e277-195">Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek.</span><span class="sxs-lookup"><span data-stu-id="7e277-195">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="7e277-196">Pokud se k webu služby IIS je konfigurována tak anonymní přístup, požadavek nikdy dosáhne aplikace.</span><span class="sxs-lookup"><span data-stu-id="7e277-196">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="7e277-197">Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.</span><span class="sxs-lookup"><span data-stu-id="7e277-197">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="7e277-198">Povolení anonymního přístupu</span><span class="sxs-lookup"><span data-stu-id="7e277-198">Allow anonymous access</span></span>

<span data-ttu-id="7e277-199">Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy.</span><span class="sxs-lookup"><span data-stu-id="7e277-199">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="7e277-200">`[Authorize]` Atribut umožňuje zabezpečené koncové body aplikace, které vyžadují ověřování.</span><span class="sxs-lookup"><span data-stu-id="7e277-200">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="7e277-201">`[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut v aplikacích, které povolí anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="7e277-201">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="7e277-202">Podrobnosti o použití atributu, naleznete v tématu <xref:security/authorization/simple>.</span><span class="sxs-lookup"><span data-stu-id="7e277-202">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="7e277-203">Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="7e277-203">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="7e277-204">[StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".</span><span class="sxs-lookup"><span data-stu-id="7e277-204">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="7e277-205">Zosobnění</span><span class="sxs-lookup"><span data-stu-id="7e277-205">Impersonation</span></span>

<span data-ttu-id="7e277-206">ASP.NET Core neimplementuje zosobnění.</span><span class="sxs-lookup"><span data-stu-id="7e277-206">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="7e277-207">Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu.</span><span class="sxs-lookup"><span data-stu-id="7e277-207">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="7e277-208">Pokud aplikace musí provést akce jménem uživatele, použijte [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [terminálu vložené middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7e277-208">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="7e277-209">V tomto kontextu spuštění jedné akce a potom zavřete kontextu.</span><span class="sxs-lookup"><span data-stu-id="7e277-209">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="7e277-210">`RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře.</span><span class="sxs-lookup"><span data-stu-id="7e277-210">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="7e277-211">Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.</span><span class="sxs-lookup"><span data-stu-id="7e277-211">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

## <a name="claims-transformations"></a><span data-ttu-id="7e277-212">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="7e277-212">Claims transformations</span></span>

<span data-ttu-id="7e277-213">Při hostování za nástrojem s režimem v procesu služby IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nevolá interně k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="7e277-213">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="7e277-214">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaném k transformaci deklarací identity po každém ověření není ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="7e277-214">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="7e277-215">Další informace a příklad kódu, který aktivuje transformace deklarací identity, při hostování v procesu najdete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="7e277-215">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e277-216">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7e277-216">Additional resources</span></span>

* [<span data-ttu-id="7e277-217">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="7e277-217">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
