---
title: Konfigurace ověřování systému Windows v ASP.NET Core
author: scottaddie
description: Přečtěte si, jak nakonfigurovat ověřování systému Windows v ASP.NET Core pro službu IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330678"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="3b10c-103">Konfigurace ověřování systému Windows v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b10c-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="3b10c-104">[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3b10c-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3b10c-105">Ověřování systému Windows (označované také jako Negotiate, Kerberos nebo ověřování NTLM) lze nakonfigurovat pro ASP.NET Core aplikace hostované službou [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="3b10c-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3b10c-106">Ověřování systému Windows (označované také jako Negotiate, Kerberos nebo ověřování NTLM) lze nakonfigurovat pro ASP.NET Core aplikace hostované službou [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="3b10c-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="3b10c-107">Ověřování systému Windows využívá operační systém k ověřování uživatelů aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b10c-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="3b10c-108">Ověřování systému Windows můžete použít, pokud server běží na podnikové síti pomocí identit domény služby Active Directory nebo účtů systému Windows k identifikaci uživatelů.</span><span class="sxs-lookup"><span data-stu-id="3b10c-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="3b10c-109">Ověřování systému Windows je nejvhodnější pro intranetová prostředí, kde uživatelé, klientské aplikace a webové servery patří do stejné domény systému Windows.</span><span class="sxs-lookup"><span data-stu-id="3b10c-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="3b10c-110">Ověřování systému Windows není u protokolu HTTP/2 podporováno.</span><span class="sxs-lookup"><span data-stu-id="3b10c-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="3b10c-111">Výzvy k ověřování se dají odesílat na odpovědi HTTP/2, ale klient se musí před ověřením snížit na HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="3b10c-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="3b10c-112">Scénáře proxy a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="3b10c-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="3b10c-113">Ověřování systému Windows je stavový scénář primárně používaný v intranetu, kde proxy server nebo nástroj pro vyrovnávání zatížení obvykle nezpracovává přenos mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="3b10c-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="3b10c-114">Pokud se používá proxy server nebo nástroj pro vyrovnávání zatížení, funguje ověřování systému Windows pouze v případě, že proxy server nebo nástroj pro vyrovnávání zatížení:</span><span class="sxs-lookup"><span data-stu-id="3b10c-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="3b10c-115">Zpracovává ověřování.</span><span class="sxs-lookup"><span data-stu-id="3b10c-115">Handles the authentication.</span></span>
* <span data-ttu-id="3b10c-116">Předá do aplikace informace o ověřování uživatele (například v hlavičce požadavku), která funguje na ověřovacích informacích.</span><span class="sxs-lookup"><span data-stu-id="3b10c-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="3b10c-117">Alternativou k ověřování Windows v prostředích, kde se používají proxy a služby Vyrovnávání zatížení, je služba AD FS (Active Directory federovaných služeb) se službou OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="3b10c-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="3b10c-118">Služba IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="3b10c-118">IIS/IIS Express</span></span>

<span data-ttu-id="3b10c-119">Přidejte ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3b10c-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="3b10c-120">Nastavení spuštění (ladicí program)</span><span class="sxs-lookup"><span data-stu-id="3b10c-120">Launch settings (debugger)</span></span>

<span data-ttu-id="3b10c-121">Konfigurace pro nastavení spuštění má vliv pouze na *vlastnosti/launchSettings.jsv* souboru pro IIS Express a NEKONFIGURUJE službu IIS pro ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="3b10c-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="3b10c-122">Konfigurace serveru je vysvětlena v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="3b10c-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="3b10c-123">Šablona **webové aplikace** , která je k dispozici prostřednictvím sady Visual Studio, nebo .NET Core CLI lze nakonfigurovat pro podporu ověřování systému Windows, která automaticky aktualizuje *vlastnosti/launchSettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="3b10c-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b10c-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b10c-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b10c-125">**Nový projekt**</span><span class="sxs-lookup"><span data-stu-id="3b10c-125">**New project**</span></span>

1. <span data-ttu-id="3b10c-126">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="3b10c-126">Create a new project.</span></span>
1. <span data-ttu-id="3b10c-127">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="3b10c-128">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-128">Select **Next**.</span></span>
1. <span data-ttu-id="3b10c-129">Do pole **název projektu** zadejte název.</span><span class="sxs-lookup"><span data-stu-id="3b10c-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="3b10c-130">Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu.</span><span class="sxs-lookup"><span data-stu-id="3b10c-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3b10c-131">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-131">Select **Create**.</span></span>
1. <span data-ttu-id="3b10c-132">V části **ověřování**vyberte **změnit** .</span><span class="sxs-lookup"><span data-stu-id="3b10c-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="3b10c-133">V okně **změnit ověřování** vyberte **ověřování systému Windows**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="3b10c-134">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-134">Select **OK**.</span></span>
1. <span data-ttu-id="3b10c-135">Vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="3b10c-136">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-136">Select **Create**.</span></span>

<span data-ttu-id="3b10c-137">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b10c-137">Run the app.</span></span> <span data-ttu-id="3b10c-138">Uživatelské jméno se zobrazí v uživatelském rozhraní vykreslené aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b10c-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="3b10c-139">**Existující projekt**</span><span class="sxs-lookup"><span data-stu-id="3b10c-139">**Existing project**</span></span>

<span data-ttu-id="3b10c-140">Vlastnosti projektu umožňují ověřování systému Windows a zakázání anonymního ověřování:</span><span class="sxs-lookup"><span data-stu-id="3b10c-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="3b10c-141">Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="3b10c-142">Vyberte kartu **ladit** .</span><span class="sxs-lookup"><span data-stu-id="3b10c-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="3b10c-143">Zrušte zaškrtnutí políčka **Povolit anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="3b10c-144">Zaškrtněte políčko **Povolit ověřování systému Windows**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="3b10c-145">Uložte a zavřete stránku vlastností.</span><span class="sxs-lookup"><span data-stu-id="3b10c-145">Save and close the property page.</span></span>

<span data-ttu-id="3b10c-146">Případně lze vlastnosti nakonfigurovat v `iisSettings` uzlu *launchSettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="3b10c-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3b10c-147">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3b10c-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3b10c-148">**Nový projekt**</span><span class="sxs-lookup"><span data-stu-id="3b10c-148">**New project**</span></span>

<span data-ttu-id="3b10c-149">Spusťte příkaz [dotnet New](/dotnet/core/tools/dotnet-new) s `webapp` argumentem (ASP.NET Core webové aplikace) a `--auth Windows` Přepněte:</span><span class="sxs-lookup"><span data-stu-id="3b10c-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="3b10c-150">**Existující projekt**</span><span class="sxs-lookup"><span data-stu-id="3b10c-150">**Existing project**</span></span>

<span data-ttu-id="3b10c-151">Aktualizovat `iisSettings` uzel *launchSettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="3b10c-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="3b10c-152">Při úpravách existujícího projektu potvrďte, že soubor projektu obsahuje odkaz na balíček pro [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) **nebo** balíček NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="3b10c-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="3b10c-153">IIS</span><span class="sxs-lookup"><span data-stu-id="3b10c-153">IIS</span></span>

<span data-ttu-id="3b10c-154">Služba IIS používá [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) k hostování ASP.NET Corech aplikací.</span><span class="sxs-lookup"><span data-stu-id="3b10c-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="3b10c-155">Ověřování systému Windows je konfigurováno pro službu IIS prostřednictvím souboru *web.config* .</span><span class="sxs-lookup"><span data-stu-id="3b10c-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="3b10c-156">Následující části ukazují, jak:</span><span class="sxs-lookup"><span data-stu-id="3b10c-156">The following sections show how to:</span></span>

* <span data-ttu-id="3b10c-157">Zadejte místní *web.config* soubor, který aktivuje ověřování systému Windows na serveru při nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b10c-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="3b10c-158">Pomocí Správce služby IIS nakonfigurujte *web.config* soubor aplikace ASP.NET Core, která už je nasazená na serveru.</span><span class="sxs-lookup"><span data-stu-id="3b10c-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="3b10c-159">Pokud jste to ještě neudělali, povolte službě IIS hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b10c-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="3b10c-160">Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="3b10c-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="3b10c-161">Povolte službu role IIS pro ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="3b10c-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="3b10c-162">Další informace najdete v tématu [Povolení ověřování systému Windows v rolích služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="3b10c-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="3b10c-163">[Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) je nakonfigurována pro automatické ověřování požadavků ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="3b10c-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="3b10c-164">Další informace najdete v tématu [hostitelská ASP.NET Core ve Windows se službou IIS: možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="3b10c-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="3b10c-165">Modul ASP.NET Core je nakonfigurován tak, aby ve výchozím nastavení předal token ověřování systému Windows do aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b10c-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="3b10c-166">Další informace najdete v tématu [Referenční příručka konfigurace modulu ASP.NET Core: atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="3b10c-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="3b10c-167">Použijte **některý** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="3b10c-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="3b10c-168">**Před publikováním a nasazením projektu** přidejte následující soubor *web.config* do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="3b10c-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="3b10c-169">Pokud je projekt publikován pomocí .NET Core SDK (bez `<IsTransformWebConfigDisabled>` vlastnosti nastavenou na hodnotu `true` v souboru projektu), publikovaný soubor *web.config* obsahuje `<location><system.webServer><security><authentication>` oddíl.</span><span class="sxs-lookup"><span data-stu-id="3b10c-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="3b10c-170">Další informace o této `<IsTransformWebConfigDisabled>` vlastnosti naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file> .</span><span class="sxs-lookup"><span data-stu-id="3b10c-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="3b10c-171">**Po publikování a nasazení projektu** proveďte u správce služby IIS konfiguraci na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="3b10c-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="3b10c-172">Ve Správci služby IIS vyberte v uzlu **lokality** na postranním panelu **připojení** web služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3b10c-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="3b10c-173">Dvakrát klikněte na **ověřování** v oblasti **IIS** .</span><span class="sxs-lookup"><span data-stu-id="3b10c-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="3b10c-174">Vyberte **anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="3b10c-175">Na bočním panelu **Akce** vyberte **Zakázat** .</span><span class="sxs-lookup"><span data-stu-id="3b10c-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="3b10c-176">Vyberte **ověřování systému Windows**.</span><span class="sxs-lookup"><span data-stu-id="3b10c-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="3b10c-177">Na postranním panelu **Akce** vyberte **Povolit** .</span><span class="sxs-lookup"><span data-stu-id="3b10c-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="3b10c-178">Při těchto akcích Správce služby IIS upraví soubor *web.config* aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b10c-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="3b10c-179">`<system.webServer><security><authentication>`Uzel je přidán s aktualizovaným nastavením pro `anonymousAuthentication` a `windowsAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="3b10c-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="3b10c-180">`<system.webServer>`Oddíl přidaný do souboru *web.config* správcem služby IIS je mimo `<location>` oddíl aplikace přidaný .NET Core SDK při publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b10c-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="3b10c-181">Vzhledem k tomu, že část je přidána mimo `<location>` uzel, nastavení jsou děděna všemi [podaplikacemi](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b10c-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="3b10c-182">Chcete-li zabránit dědění, přesuňte přidaný `<security>` oddíl do `<location><system.webServer>` oddílu, který .NET Core SDK poskytl.</span><span class="sxs-lookup"><span data-stu-id="3b10c-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="3b10c-183">Když se k přidání konfigurace služby IIS použije Správce služby IIS, ovlivní to jenom *web.config* souboru aplikace na serveru.</span><span class="sxs-lookup"><span data-stu-id="3b10c-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="3b10c-184">Následné nasazení aplikace může přepsat nastavení na serveru, pokud je kopie *web.config* serveru nahrazena *web.configm* souborem projektu.</span><span class="sxs-lookup"><span data-stu-id="3b10c-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="3b10c-185">Ke správě nastavení použijte **některý** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="3b10c-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="3b10c-186">Pomocí Správce služby IIS obnovte nastavení v souboru *web.config* poté, co je soubor po nasazení přepsán.</span><span class="sxs-lookup"><span data-stu-id="3b10c-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="3b10c-187">Do aplikace lokálně přidejte *souborweb.config* s nastavením.</span><span class="sxs-lookup"><span data-stu-id="3b10c-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="3b10c-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="3b10c-188">Kestrel</span></span>

<span data-ttu-id="3b10c-189">Balíček NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) se dá použít s [Kestrel](xref:fundamentals/servers/kestrel) k podpoře ověřování systému Windows pomocí dohadování a protokolu Kerberos v systémech Windows, Linux a MacOS.</span><span class="sxs-lookup"><span data-stu-id="3b10c-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="3b10c-190">Přihlašovací údaje je možné zachovat v rámci požadavků na připojení.</span><span class="sxs-lookup"><span data-stu-id="3b10c-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="3b10c-191">*Ověřování Negotiate se nesmí používat s proxy servery, pokud proxy neudržuje spřažení připojení 1:1 (trvalé připojení) pomocí Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="3b10c-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="3b10c-192">Obslužná rutina Negotiate detekuje, jestli základní server podporuje ověřování systému Windows nativně a jestli je povolený.</span><span class="sxs-lookup"><span data-stu-id="3b10c-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="3b10c-193">Pokud server podporuje ověřování systému Windows, ale je zakázaný, vyvolá se chyba s výzvou, abyste povolili implementaci serveru.</span><span class="sxs-lookup"><span data-stu-id="3b10c-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="3b10c-194">Pokud je na serveru povoleno ověřování systému Windows, obslužná rutina Negotiate ji transparentně přepošle.</span><span class="sxs-lookup"><span data-stu-id="3b10c-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="3b10c-195">Přidejte ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3b10c-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="3b10c-196">Přidejte middleware ověřování voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3b10c-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="3b10c-197">Další informace o middlewaru naleznete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="3b10c-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="3b10c-198">Anonymní požadavky jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="3b10c-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="3b10c-199">K ověření anonymních žádostí o ověření použijte [ASP.NET Core autorizaci](xref:security/authorization/introduction) .</span><span class="sxs-lookup"><span data-stu-id="3b10c-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="3b10c-200">Konfigurace prostředí Windows</span><span class="sxs-lookup"><span data-stu-id="3b10c-200">Windows environment configuration</span></span>

<span data-ttu-id="3b10c-201">Komponenta [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) provádí ověřování v uživatelském režimu.</span><span class="sxs-lookup"><span data-stu-id="3b10c-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="3b10c-202">Hlavní názvy služeb (SPN) musí být přidány do uživatelského účtu, který spouští službu, nikoli z účtu počítače.</span><span class="sxs-lookup"><span data-stu-id="3b10c-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="3b10c-203">Spustit `setspn -S HTTP/myservername.mydomain.com myuser` v příkazovém prostředí pro správu.</span><span class="sxs-lookup"><span data-stu-id="3b10c-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="3b10c-204">Konfigurace prostředí Linux a macOS</span><span class="sxs-lookup"><span data-stu-id="3b10c-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="3b10c-205">Pokyny pro připojení počítače se systémem Linux nebo macOS k doméně systému Windows jsou k dispozici v tématu [připojení Azure Data Studio k vašemu SQL Server pomocí ověřování systému Windows – Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) .</span><span class="sxs-lookup"><span data-stu-id="3b10c-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="3b10c-206">Pokyny vytvoří účet počítače pro počítač se systémem Linux v doméně.</span><span class="sxs-lookup"><span data-stu-id="3b10c-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="3b10c-207">Do tohoto účtu počítače se musí přidat hlavní názvy služby (SPN).</span><span class="sxs-lookup"><span data-stu-id="3b10c-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="3b10c-208">Pokud [budete postupovat podle pokynů v Azure Data Studio připojit k vašemu SQL Server pomocí ověřování systému Windows – Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , `python-software-properties` v `python3-software-properties` případě potřeby nahraďte.</span><span class="sxs-lookup"><span data-stu-id="3b10c-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="3b10c-209">Jakmile je počítač se systémem Linux nebo macOS připojený k doméně, jsou potřeba další kroky k poskytnutí [souboru keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) s názvy SPN:</span><span class="sxs-lookup"><span data-stu-id="3b10c-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="3b10c-210">Na řadiči domény přidejte nové hlavní názvy služby webu k účtu počítače:</span><span class="sxs-lookup"><span data-stu-id="3b10c-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="3b10c-211">K vygenerování souboru keytab použijte [Ktpass](/windows-server/administration/windows-commands/ktpass) :</span><span class="sxs-lookup"><span data-stu-id="3b10c-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="3b10c-212">Některá pole musí být zadána velkými písmeny, jak je uvedeno.</span><span class="sxs-lookup"><span data-stu-id="3b10c-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="3b10c-213">Zkopírujte soubor keytab do počítače se systémem Linux nebo macOS.</span><span class="sxs-lookup"><span data-stu-id="3b10c-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="3b10c-214">Vyberte soubor keytab přes proměnnou prostředí:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="3b10c-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="3b10c-215">Invoke `klist` pro zobrazení hlavních názvů služby, které jsou aktuálně k dispozici pro použití.</span><span class="sxs-lookup"><span data-stu-id="3b10c-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="3b10c-216">Soubor keytab obsahuje přihlašovací údaje pro přístup k doméně a musí být vhodným způsobem chráněn.</span><span class="sxs-lookup"><span data-stu-id="3b10c-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="3b10c-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="3b10c-217">HTTP.sys</span></span>

<span data-ttu-id="3b10c-218">[HTTP.sys](xref:fundamentals/servers/httpsys) podporuje ověřování systému Windows v režimu jádra pomocí ověřování Negotiate, NTLM nebo Basic.</span><span class="sxs-lookup"><span data-stu-id="3b10c-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="3b10c-219">Přidejte ověřovací služby vyvoláním <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3b10c-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="3b10c-220">Nakonfigurujte webového hostitele aplikace tak, aby používal HTTP.sys s ověřováním systému Windows (*program.cs*).</span><span class="sxs-lookup"><span data-stu-id="3b10c-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="3b10c-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>je v <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="3b10c-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3b10c-222">HTTP.sys deleguje ověřování režimu jádra pomocí ověřovacího protokolu Kerberos.</span><span class="sxs-lookup"><span data-stu-id="3b10c-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="3b10c-223">Ověřování pomocí uživatelského režimu není pro protokol Kerberos a HTTP.sys podporováno.</span><span class="sxs-lookup"><span data-stu-id="3b10c-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="3b10c-224">Účet počítače se musí použít k dešifrování tokenu nebo lístku protokolu Kerberos, který je získaný ze služby Active Directory a který přesměruje klient na server za účelem ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="3b10c-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="3b10c-225">Zaregistrujte hlavní název služby (SPN) pro hostitele, nikoli uživatele aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b10c-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3b10c-226">HTTP.sys se na nano serveru verze 1709 nebo novější nepodporují.</span><span class="sxs-lookup"><span data-stu-id="3b10c-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="3b10c-227">Pokud chcete používat ověřování Windows a HTTP.sys s Nano serverem, použijte [kontejner jádro serveru (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="3b10c-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="3b10c-228">Další informace o jádru serveru najdete v tématu [co je možnost instalace jádra serveru ve Windows serveru](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="3b10c-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="3b10c-229">Autorizovat uživatele</span><span class="sxs-lookup"><span data-stu-id="3b10c-229">Authorize users</span></span>

<span data-ttu-id="3b10c-230">Stav konfigurace anonymního přístupu určuje způsob, jakým se v `[Authorize]` `[AllowAnonymous]` aplikaci používají atributy a.</span><span class="sxs-lookup"><span data-stu-id="3b10c-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="3b10c-231">Následující dvě části vysvětlují, jak zpracovat nepovolené a povolené stavy Konfigurace anonymního přístupu.</span><span class="sxs-lookup"><span data-stu-id="3b10c-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="3b10c-232">Zakázat anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="3b10c-232">Disallow anonymous access</span></span>

<span data-ttu-id="3b10c-233">Pokud je povoleno ověřování systému Windows a je zakázán anonymní přístup, `[Authorize]` `[AllowAnonymous]` atributy a nemají žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="3b10c-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="3b10c-234">Pokud je lokalita IIS nakonfigurovaná tak, aby povolovala anonymní přístup, požadavek nikdy nedosáhne vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b10c-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="3b10c-235">Z tohoto důvodu se `[AllowAnonymous]` atribut nedá použít.</span><span class="sxs-lookup"><span data-stu-id="3b10c-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="3b10c-236">Povolení anonymního přístupu</span><span class="sxs-lookup"><span data-stu-id="3b10c-236">Allow anonymous access</span></span>

<span data-ttu-id="3b10c-237">Pokud je povoleno ověřování systému Windows i anonymní přístup, použijte `[Authorize]` atributy a `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="3b10c-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="3b10c-238">`[Authorize]`Atribut umožňuje zabezpečit koncové body aplikace, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="3b10c-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="3b10c-239">`[AllowAnonymous]`Atribut Přepisuje `[Authorize]` atribut v aplikacích, které povolují anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="3b10c-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="3b10c-240">Podrobnosti o použití atributů naleznete v tématu <xref:security/authorization/simple> .</span><span class="sxs-lookup"><span data-stu-id="3b10c-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="3b10c-241">Ve výchozím nastavení se uživatelům, kteří nemají oprávnění k přístupu na stránku, zobrazí prázdná odpověď HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="3b10c-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="3b10c-242">[Middleware StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) se dá nakonfigurovat tak, aby poskytoval uživatelům lepší přístup "přístup byl odepřen".</span><span class="sxs-lookup"><span data-stu-id="3b10c-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="3b10c-243">Zosobnění</span><span class="sxs-lookup"><span data-stu-id="3b10c-243">Impersonation</span></span>

<span data-ttu-id="3b10c-244">ASP.NET Core neimplementuje zosobnění.</span><span class="sxs-lookup"><span data-stu-id="3b10c-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="3b10c-245">Aplikace se spouštějí s identitou aplikace pro všechny žádosti pomocí fondu aplikací nebo identity procesu.</span><span class="sxs-lookup"><span data-stu-id="3b10c-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="3b10c-246">Pokud by aplikace měla provést akci jménem uživatele, použijte [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [middlewaru vloženého terminálu](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="3b10c-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="3b10c-247">Spusťte v tomto kontextu jednu akci a pak kontext zavřete.</span><span class="sxs-lookup"><span data-stu-id="3b10c-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="3b10c-248">`RunImpersonated`nepodporuje asynchronní operace a neměl by se používat pro složité scénáře.</span><span class="sxs-lookup"><span data-stu-id="3b10c-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="3b10c-249">Například zabalení všech požadavků nebo řetězů middleware není podporováno ani doporučeno.</span><span class="sxs-lookup"><span data-stu-id="3b10c-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3b10c-250">I když balíček [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) umožňuje ověřování v systémech Windows, Linux a MacOS, zosobnění je podporováno pouze v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="3b10c-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="3b10c-251">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="3b10c-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3b10c-252">Při hostování se službou IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="3b10c-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3b10c-253">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="3b10c-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3b10c-254">Další informace a příklad kódu, který aktivuje transformace deklarací identity, najdete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="3b10c-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3b10c-255">Při hostování v režimu vnitroprocesové v rámci služby IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="3b10c-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3b10c-256">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="3b10c-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3b10c-257">Další informace a příklad kódu, který aktivuje transformace deklarací při hostování v procesu, naleznete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="3b10c-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3b10c-258">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3b10c-258">Additional resources</span></span>

* [<span data-ttu-id="3b10c-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="3b10c-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
