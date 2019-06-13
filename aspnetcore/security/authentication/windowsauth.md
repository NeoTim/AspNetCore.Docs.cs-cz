---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pro službu IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/12/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 93f833adff95f25d570947cd1a9035d652f522c2
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034948"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="2e4d4-103">Konfigurace ověřování Windows v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e4d4-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="2e4d4-104">Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2e4d4-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2e4d4-105">Ověřování Windows (označované také jako ověřování Negotiate, Kerberos nebo NTLM) se dá nakonfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), nebo [HTTP.sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="2e4d4-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2e4d4-106">Ověřování Windows (označované také jako ověřování Negotiate, Kerberos nebo NTLM) se dá nakonfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2e4d4-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="2e4d4-107">Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="2e4d4-108">Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identity služby Active Directory domény nebo účty Windows k identifikaci uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="2e4d4-109">Ověřování Windows je nejvhodnější pro prostředí intranetu, kde uživatelé klientských aplikací a webové servery patří do stejné domény Windows.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="2e4d4-110">HTTP/2 nepodporuje ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="2e4d4-111">Výzev ověřování lze odeslat v odpovědi HTTP/2, ale klient musí před ověřením downgradovat HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="2e4d4-112">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="2e4d4-112">IIS/IIS Express</span></span>

<span data-ttu-id="2e4d4-113">Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> oboru názvů) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-113">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="2e4d4-114">Spusťte nastavení (ladicí program)</span><span class="sxs-lookup"><span data-stu-id="2e4d4-114">Launch settings (debugger)</span></span>

<span data-ttu-id="2e4d4-115">Konfigurace nastavení spuštění má vliv pouze *Properties/launchSettings.json* souboru pro službu IIS Express a nebude konfigurace IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-115">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="2e4d4-116">Konfigurace serveru je podrobně [IIS](#iis) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-116">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="2e4d4-117">**Webovou aplikaci** šablony, které jsou k dispozici prostřednictvím sady Visual Studio nebo rozhraní příkazového řádku .NET Core může být nakonfigurované pro podporu ověřování Windows, která aktualizuje *Properties/launchSettings.json* souboru automaticky.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-117">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e4d4-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e4d4-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2e4d4-119">**Nový projekt**</span><span class="sxs-lookup"><span data-stu-id="2e4d4-119">**New project**</span></span>

1. <span data-ttu-id="2e4d4-120">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-120">Create a new project.</span></span>
1. <span data-ttu-id="2e4d4-121">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-121">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2e4d4-122">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-122">Select **Next**.</span></span>
1. <span data-ttu-id="2e4d4-123">Zadejte název **název projektu** pole.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-123">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="2e4d4-124">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2e4d4-125">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-125">Select **Create**.</span></span>
1. <span data-ttu-id="2e4d4-126">Vyberte **změnu** pod **ověřování**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-126">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="2e4d4-127">V **změna ověřování** okně **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-127">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="2e4d4-128">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-128">Select **OK**.</span></span>
1. <span data-ttu-id="2e4d4-129">Vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-129">Select **Web Application**.</span></span>
1. <span data-ttu-id="2e4d4-130">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-130">Select **Create**.</span></span>

<span data-ttu-id="2e4d4-131">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-131">Run the app.</span></span> <span data-ttu-id="2e4d4-132">Uživatelské jméno se zobrazí v uživatelském rozhraní vygenerované aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-132">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="2e4d4-133">**Existující projekt**</span><span class="sxs-lookup"><span data-stu-id="2e4d4-133">**Existing project**</span></span>

<span data-ttu-id="2e4d4-134">Vlastnosti projektu povolit ověřování Windows a vypnutí anonymního ověřování:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-134">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="2e4d4-135">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-135">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="2e4d4-136">Vyberte **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-136">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="2e4d4-137">Zrušte zaškrtnutí políčka pro **povolit anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-137">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="2e4d4-138">Zaškrtněte políčko pro **povolit ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-138">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="2e4d4-139">Uložit a zavřít jeho stránku vlastností.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-139">Save and close the property page.</span></span>

<span data-ttu-id="2e4d4-140">Alternativně se dá nakonfigurovat vlastnosti v `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-140">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="2e4d4-141">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2e4d4-141">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="2e4d4-142">**Nový projekt**</span><span class="sxs-lookup"><span data-stu-id="2e4d4-142">**New project**</span></span>

<span data-ttu-id="2e4d4-143">Spustit [dotnet nové](/dotnet/core/tools/dotnet-new) příkazů `webapp` argument (webové aplikace ASP.NET Core) a `--auth Windows` přepínače:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-143">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="2e4d4-144">**Existující projekt**</span><span class="sxs-lookup"><span data-stu-id="2e4d4-144">**Existing project**</span></span>

<span data-ttu-id="2e4d4-145">Aktualizace `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-145">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="2e4d4-146">Při úpravě existujícího projektu, přesvědčte se, že soubor projektu obsahuje odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) **nebo** [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-146">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="2e4d4-147">IIS</span><span class="sxs-lookup"><span data-stu-id="2e4d4-147">IIS</span></span>

<span data-ttu-id="2e4d4-148">Služba IIS použije [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-148">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="2e4d4-149">Ověřování Windows je nakonfigurovaný pro službu IIS prostřednictvím *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-149">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="2e4d4-150">V následujících částech zobrazit postup:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-150">The following sections show how to:</span></span>

* <span data-ttu-id="2e4d4-151">Zadejte místní *web.config* soubor, který aktivuje ověřování Windows na serveru při nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-151">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="2e4d4-152">Pomocí Správce služby IIS ke konfiguraci *web.config* souboru aplikace v ASP.NET Core, která již byla nasazena na server.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-152">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="2e4d4-153">Pokud jste tak již neučinili, povolte službu IIS pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-153">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="2e4d4-154">Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-154">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="2e4d4-155">Povolte službu Role služby IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-155">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="2e4d4-156">Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="2e4d4-156">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="2e4d4-157">[Middleware pro integraci služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-157">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="2e4d4-158">Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: Možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="2e4d4-158">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="2e4d4-159">Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-159">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="2e4d4-160">Další informace najdete v tématu [odkaz na modul ASP.NET Core konfigurace: Atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="2e4d4-160">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="2e4d4-161">Použití **buď** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-161">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="2e4d4-162">**Před publikováním a nasazování projektu,** přidejte následující *web.config* souboru do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-162">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="2e4d4-163">Když se publikuje projektu .NET Core SDK (bez `<IsTransformWebConfigDisabled>` vlastnost nastavena na hodnotu `true` v souboru projektu), publikovanému *web.config* soubor obsahuje `<location><system.webServer><security><authentication>` části.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-163">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="2e4d4-164">Další informace o `<IsTransformWebConfigDisabled>` vlastnost, naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-164">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="2e4d4-165">**Po publikování a nasazení projektu,** provedení konfigurace na straně serveru pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-165">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="2e4d4-166">Ve Správci služby IIS vyberte web služby IIS v části **lokality** uzlu **připojení** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="2e4d4-167">Dvakrát klikněte na panel **ověřování** v **IIS** oblasti.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-167">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="2e4d4-168">Vyberte **anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="2e4d4-169">Vyberte **zakázat** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-169">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="2e4d4-170">Vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="2e4d4-171">Vyberte **povolit** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-171">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="2e4d4-172">Když se provedou tyto akce, Správce služby IIS upraví aplikace *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="2e4d4-173">A `<system.webServer><security><authentication>` s aktualizovaným nastavením pro přidání uzlu `anonymousAuthentication` a `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="2e4d4-174">`<system.webServer>` Přidá do části *web.config* soubor pomocí Správce služby IIS je mimo aplikaci prvku `<location>` části přidá .NET Core SDK, když je aplikace publikována.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="2e4d4-175">Vzhledem k tomu, že v části Přidání mimo `<location>` uzlu nastavení dědí žádné [dílčí aplikace](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="2e4d4-176">Chcete-li zabránit dědění, přesuňte přidaného `<security>` části uvnitř `<location><system.webServer>` části, která poskytuje sada .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="2e4d4-177">Když správce služby IIS se používá k přidání konfigurace služby IIS, ovlivní pouze aplikace *web.config* souboru na serveru.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="2e4d4-178">Následné nasazení aplikace může přepsat nastavení na serveru, pokud na server kopii *web.config* nahrazuje projektu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="2e4d4-179">Použití **buď** z následujících postupů můžete spravovat nastavení:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-179">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="2e4d4-180">Pomocí Správce služby IIS k resetování nastavení ve *web.config* souboru po souboru se přepíše při nasazení.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="2e4d4-181">Přidat *souboru web.config* do aplikace místně s nastavením.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-181">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="2e4d4-182">Kestrel</span><span class="sxs-lookup"><span data-stu-id="2e4d4-182">Kestrel</span></span>

 <span data-ttu-id="2e4d4-183">[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) jde použít balíček NuGet s [Kestrel](xref:fundamentals/servers/kestrel) pro podporu ověřování Windows ve Windows, Linuxu a macOS pomocí Negotiate, protokolu Kerberos a NTLM.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-183">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate, Kerberos, and NTLM on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="2e4d4-184">Přihlašovací údaje můžete nastavit jako trvalý napříč požadavky na připojení.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-184">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="2e4d4-185">*Vyjednávání ověřování se nesmí používat s proxy servery, pokud proxy server udržuje vztahů 1:1 připojení (trvalé připojení) s Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="2e4d4-185">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span> <span data-ttu-id="2e4d4-186">To znamená, že vyjednat ověření se nesmí používat s Kestrel za IIS [ASP.NET Core modulu (ANCM) out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="2e4d4-186">This means that Negotiate authentication must not be used with Kestrel behind the IIS [ASP.NET Core Module (ANCM) out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model).</span></span>

 <span data-ttu-id="2e4d4-187">Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` oboru názvů) a `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` oboru názvů) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-187">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) and `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) in `Startup.ConfigureServices`:</span></span>

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="2e4d4-188">Přidat ověřovací Middleware voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-188">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

<span data-ttu-id="2e4d4-189">Další informace o middlewaru, naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-189">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="2e4d4-190">Anonymní žádosti jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-190">Anonymous requests are allowed.</span></span> <span data-ttu-id="2e4d4-191">Použití [ověřování ASP.NET Core](xref:security/authorization/introduction) vybízí anonymní žádosti o ověření.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-191">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="2e4d4-192">Konfigurace prostředí Windows</span><span class="sxs-lookup"><span data-stu-id="2e4d4-192">Windows environment configuration</span></span>

<span data-ttu-id="2e4d4-193">[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) komponenta provede ověřování v režimu uživatele.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-193">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="2e4d4-194">Hlavní názvy služby (SPN) musí být přidaný do uživatelský účet spouštějící službu, účet počítače.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-194">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="2e4d4-195">Spustit `setspn -S HTTP/mysrevername.mydomain.com myuser` v správu příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-195">Execute `setspn -S HTTP/mysrevername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="2e4d4-196">Konfigurace prostředí Linux a macOS</span><span class="sxs-lookup"><span data-stu-id="2e4d4-196">Linux and macOS environment configuration</span></span>

<span data-ttu-id="2e4d4-197">Pokyny pro připojení k počítači s Linuxem nebo macOS k doméně Windows jsou k dispozici v [připojení Azure Data Studio k SQL serveru pomocí ověřování Windows - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) článku.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-197">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="2e4d4-198">Podle pokynů vytvořte účet počítače pro počítač s Linuxem v doméně.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-198">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="2e4d4-199">K tomuto účtu počítače je nutné přidat hlavní názvy služby.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-199">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="2e4d4-200">Podle pokynů v [připojení Azure Data Studio k SQL serveru pomocí ověřování Windows - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) článek, nahraďte `python-software-properties` s `python3-software-properties` v případě potřeby.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-200">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="2e4d4-201">Jakmile počítače s Linuxem nebo macOS je připojen k doméně, je třeba zadat další kroky [soubor keytab souboru](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) s hlavní názvy služby:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-201">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="2e4d4-202">Na řadiči domény přidejte novou webovou službu SPN pro účet počítače:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-202">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="2e4d4-203">Použití [příkazový řádek ktpass](/windows-server/administration/windows-commands/ktpass) vygenerovat soubor soubor keytab:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-203">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="2e4d4-204">Některá pole musí být zadán v velká písmena, jak je uvedeno.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-204">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="2e4d4-205">Zkopírujte soubor keytab soubor do počítače s Linuxem nebo macOS.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-205">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="2e4d4-206">Vyberte soubor keytab soubor přes proměnnou prostředí: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="2e4d4-206">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="2e4d4-207">Vyvolání `klist` zobrazíte aktuálně k dispozici pro použití hlavní názvy služby.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-207">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="2e4d4-208">Soubor keytab soubor obsahuje přihlašovacích údajů pro přístup k doméně a musí být chráněné odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-208">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="2e4d4-209">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="2e4d4-209">HTTP.sys</span></span>

<span data-ttu-id="2e4d4-210">[Ovladač HTTP.sys](xref:fundamentals/servers/httpsys) podporuje ověřování Windows v režimu jádra pomocí Negotiate, NTLM nebo základní ověřování.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-210">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="2e4d4-211">Přidat ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2e4d4-211">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="2e4d4-212">Konfigurace hostitele webové aplikace pomocí HTTP.sys ověřování Windows (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="2e4d4-212">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="2e4d4-213"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Probíhá <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-213"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="2e4d4-214">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-214">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="2e4d4-215">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-215">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="2e4d4-216">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-216">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="2e4d4-217">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-217">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="2e4d4-218">Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-218">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="2e4d4-219">Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="2e4d4-219">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="2e4d4-220">Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="2e4d4-220">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="2e4d4-221">Autorizace uživatelů</span><span class="sxs-lookup"><span data-stu-id="2e4d4-221">Authorize users</span></span>

<span data-ttu-id="2e4d4-222">Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-222">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="2e4d4-223">Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-223">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="2e4d4-224">Zakázat anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="2e4d4-224">Disallow anonymous access</span></span>

<span data-ttu-id="2e4d4-225">Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-225">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="2e4d4-226">Pokud se k webu služby IIS je konfigurována tak anonymní přístup, požadavek nikdy dosáhne aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-226">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="2e4d4-227">Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-227">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="2e4d4-228">Povolení anonymního přístupu</span><span class="sxs-lookup"><span data-stu-id="2e4d4-228">Allow anonymous access</span></span>

<span data-ttu-id="2e4d4-229">Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-229">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="2e4d4-230">`[Authorize]` Atribut umožňuje zabezpečené koncové body aplikace, které vyžadují ověřování.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-230">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="2e4d4-231">`[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut v aplikacích, které povolí anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-231">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="2e4d4-232">Podrobnosti o použití atributu, naleznete v tématu <xref:security/authorization/simple>.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-232">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="2e4d4-233">Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-233">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="2e4d4-234">[StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".</span><span class="sxs-lookup"><span data-stu-id="2e4d4-234">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="2e4d4-235">Zosobnění</span><span class="sxs-lookup"><span data-stu-id="2e4d4-235">Impersonation</span></span>

<span data-ttu-id="2e4d4-236">ASP.NET Core neimplementuje zosobnění.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-236">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="2e4d4-237">Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-237">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="2e4d4-238">Pokud aplikace musí provést akce jménem uživatele, použijte [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [terminálu vložené middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-238">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="2e4d4-239">V tomto kontextu spuštění jedné akce a potom zavřete kontextu.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-239">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="2e4d4-240">`RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-240">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="2e4d4-241">Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-241">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2e4d4-242">Zatímco [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) balíček umožňuje ověřování na Windows, Linux a macOS, zosobnění se podporuje jenom na Windows.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-242">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="2e4d4-243">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="2e4d4-243">Claims transformations</span></span>

<span data-ttu-id="2e4d4-244">Při hostování za nástrojem s režimem v procesu služby IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nevolá interně k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-244">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2e4d4-245">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaném k transformaci deklarací identity po každém ověření není ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-245">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2e4d4-246">Další informace a příklad kódu, který aktivuje transformace deklarací identity, při hostování v procesu najdete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="2e4d4-246">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e4d4-247">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2e4d4-247">Additional resources</span></span>

* [<span data-ttu-id="2e4d4-248">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="2e4d4-248">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
