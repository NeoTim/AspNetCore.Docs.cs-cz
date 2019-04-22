---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pomocí služby IIS Express, IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/03/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: bd4ffa79c4d1e0070c820fa9c06b0a84c3aaae74
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468650"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="c4cb8-103">Konfigurace ověřování Windows v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4cb8-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="c4cb8-104">Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4cb8-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4cb8-105">[Ověřování Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) lze konfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="c4cb8-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="c4cb8-106">Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="c4cb8-107">Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identity služby Active Directory domény nebo účty Windows k identifikaci uživatelů.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="c4cb8-108">Ověřování Windows je nejvhodnější pro prostředí intranetu, kde uživatelé klientských aplikací a webové servery patří do stejné domény Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="c4cb8-109">Povolení ověřování Windows v aplikaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4cb8-109">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="c4cb8-110">**Webovou aplikaci** šablony, které jsou k dispozici prostřednictvím sady Visual Studio nebo rozhraní příkazového řádku .NET Core může být nakonfigurované pro podporu ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-110">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c4cb8-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4cb8-111">Visual Studio</span></span>](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a><span data-ttu-id="c4cb8-112">Použití šablony aplikace ověřování Windows pro nový projekt</span><span class="sxs-lookup"><span data-stu-id="c4cb8-112">Use the Windows Authentication app template for a new project</span></span>

<span data-ttu-id="c4cb8-113">V sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-113">In Visual Studio:</span></span>

1. <span data-ttu-id="c4cb8-114">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-114">Create a new project.</span></span>
1. <span data-ttu-id="c4cb8-115">Vyberte **webová aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="c4cb8-116">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-116">Select **Next**.</span></span>
1. <span data-ttu-id="c4cb8-117">Zadejte název **název projektu** pole.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-117">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="c4cb8-118">Potvrďte **umístění** položka je správný, a zadejte umístění pro projekt.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-118">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="c4cb8-119">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-119">Select **Create**.</span></span>
1. <span data-ttu-id="c4cb8-120">Vyberte **změnu** pod **ověřování**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-120">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="c4cb8-121">V **změna ověřování** okně **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-121">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="c4cb8-122">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-122">Select **OK**.</span></span>
1. <span data-ttu-id="c4cb8-123">Vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-123">Select **Web Application**.</span></span>
1. <span data-ttu-id="c4cb8-124">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-124">Select **Create**.</span></span>

<span data-ttu-id="c4cb8-125">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-125">Run the app.</span></span> <span data-ttu-id="c4cb8-126">Uživatelské jméno se zobrazí v uživatelském rozhraní vygenerované aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-126">The username appears in the rendered app's user interface.</span></span>

### <a name="manual-configuration-for-an-existing-project"></a><span data-ttu-id="c4cb8-127">Ruční konfigurace pro existující projekt</span><span class="sxs-lookup"><span data-stu-id="c4cb8-127">Manual configuration for an existing project</span></span>

<span data-ttu-id="c4cb8-128">Vlastnosti projektu umožňují povolit ověřování Windows a vypnutí anonymního ověřování:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-128">The project's properties allow you to enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="c4cb8-129">Klikněte pravým tlačítkem na projekt v sadě Visual Studio **Průzkumníka řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-129">Right-click the project in Visual Studio's **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="c4cb8-130">Vyberte **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-130">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="c4cb8-131">Zrušte zaškrtnutí políčka pro **povolit anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-131">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="c4cb8-132">Zaškrtněte políčko pro **povolit ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-132">Select the check box for **Enable Windows Authentication**.</span></span>

<span data-ttu-id="c4cb8-133">Alternativně se dá nakonfigurovat vlastnosti v `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-133">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c4cb8-134">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="c4cb8-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c4cb8-135">Použití **ověřování Windows** šablony aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-135">Use the **Windows Authentication** app template.</span></span>

<span data-ttu-id="c4cb8-136">Spustit [dotnet nové](/dotnet/core/tools/dotnet-new) příkazů `webapp` argument (webové aplikace ASP.NET Core) a `--auth Windows` přepínače:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-136">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

---

<span data-ttu-id="c4cb8-137">Při úpravě existujícího projektu, přesvědčte se, že soubor projektu obsahuje odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) **nebo** [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-137">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="c4cb8-138">Povolení ověřování Windows pomocí služby IIS</span><span class="sxs-lookup"><span data-stu-id="c4cb8-138">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="c4cb8-139">Služba IIS použije [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-139">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="c4cb8-140">Ověřování Windows je nakonfigurovaný pro službu IIS prostřednictvím *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-140">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="c4cb8-141">V následujících částech zobrazit postup:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-141">The following sections show how to:</span></span>

* <span data-ttu-id="c4cb8-142">Zadejte místní *web.config* soubor, který aktivuje ověřování Windows na serveru při nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-142">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="c4cb8-143">Pomocí Správce služby IIS ke konfiguraci *web.config* souboru aplikace v ASP.NET Core, která již byla nasazena na server.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-143">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="c4cb8-144">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="c4cb8-144">IIS configuration</span></span>

<span data-ttu-id="c4cb8-145">Pokud jste tak již neučinili, povolte službu IIS pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-145">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="c4cb8-146">Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-146">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="c4cb8-147">Povolte službu Role služby IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-147">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="c4cb8-148">Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="c4cb8-148">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="c4cb8-149">Middleware pro integraci služby IIS je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-149">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="c4cb8-150">Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: Možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="c4cb8-150">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="c4cb8-151">Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-151">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="c4cb8-152">Další informace najdete v tématu [odkaz na modul ASP.NET Core konfigurace: Atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="c4cb8-152">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="c4cb8-153">Vytvořit nový web služby IIS</span><span class="sxs-lookup"><span data-stu-id="c4cb8-153">Create a new IIS site</span></span>

<span data-ttu-id="c4cb8-154">Zadejte název a složku a mohla vytvořit nový fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-154">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="enable-windows-authentication-for-the-app-in-iis"></a><span data-ttu-id="c4cb8-155">Povolení ověřování Windows pro aplikace ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="c4cb8-155">Enable Windows Authentication for the app in IIS</span></span>

<span data-ttu-id="c4cb8-156">Použití **buď** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-156">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="c4cb8-157">[Konfigurace na straně vývoj před publikováním aplikace](#development-side-configuration-with-a-local-webconfig-file) (*doporučená*)</span><span class="sxs-lookup"><span data-stu-id="c4cb8-157">[Development-side configuration before publishing the app](#development-side-configuration-with-a-local-webconfig-file) (*Recommended*)</span></span>
* [<span data-ttu-id="c4cb8-158">Konfigurace na straně serveru po publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="c4cb8-158">Server-side configuration after publishing the app</span></span>](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a><span data-ttu-id="c4cb8-159">Konfigurace na straně pro vývoj se souborem web.config místní</span><span class="sxs-lookup"><span data-stu-id="c4cb8-159">Development-side configuration with a local web.config file</span></span>

<span data-ttu-id="c4cb8-160">Proveďte následující kroky **před** vám [publikujte a nasaďte svůj projekt](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="c4cb8-160">Perform the following steps **before** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

<span data-ttu-id="c4cb8-161">Přidejte následující *web.config* souboru do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-161">Add the following *web.config* file to the project root:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

<span data-ttu-id="c4cb8-162">Při publikování projektu sada SDK (bez `<IsTransformWebConfigDisabled>` vlastnost nastavena na hodnotu `true` v souboru projektu), publikovanému *web.config* soubor obsahuje `<location><system.webServer><security><authentication>` oddílu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-162">When the project is published by the SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="c4cb8-163">Další informace o `<IsTransformWebConfigDisabled>` vlastnost, naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-163">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

#### <a name="server-side-configuration-with-the-iis-manager"></a><span data-ttu-id="c4cb8-164">Konfigurace na straně serveru pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="c4cb8-164">Server-side configuration with the IIS Manager</span></span>

<span data-ttu-id="c4cb8-165">Proveďte následující kroky **po** vám [publikujte a nasaďte svůj projekt](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="c4cb8-165">Perform the following steps **after** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

1. <span data-ttu-id="c4cb8-166">Ve Správci služby IIS vyberte web služby IIS v části **lokality** uzlu **připojení** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
1. <span data-ttu-id="c4cb8-167">Dvakrát klikněte na panel **ověřování** v **IIS** oblasti.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-167">Double-click **Authentication** in the **IIS** area.</span></span>
1. <span data-ttu-id="c4cb8-168">Vyberte **anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="c4cb8-169">Vyberte **zakázat** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-169">Select **Disable** in the **Actions** sidebar.</span></span>
1. <span data-ttu-id="c4cb8-170">Vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="c4cb8-171">Vyberte **povolit** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-171">Select **Enable** in the **Actions** sidebar.</span></span>

<span data-ttu-id="c4cb8-172">Když se provedou tyto akce, Správce služby IIS upraví aplikace *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="c4cb8-173">A `<system.webServer><security><authentication>` s aktualizovaným nastavením pro přidání uzlu `anonymousAuthentication` a `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

<span data-ttu-id="c4cb8-174">`<system.webServer>` Přidá do části *web.config* soubor pomocí Správce služby IIS je mimo aplikaci prvku `<location>` části přidá .NET Core SDK, když je aplikace publikována.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="c4cb8-175">Vzhledem k tomu, že v části Přidání mimo `<location>` uzlu nastavení dědí žádné [dílčí aplikace](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="c4cb8-176">Chcete-li zabránit dědění, přesuňte přidaného `<security>` části uvnitř `<location><system.webServer>` oddíl, který poskytuje sady SDK.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the SDK provided.</span></span>

<span data-ttu-id="c4cb8-177">Když správce služby IIS se používá k přidání konfigurace služby IIS, ovlivní pouze aplikace *web.config* souboru na serveru.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="c4cb8-178">Následné nasazení aplikace může přepsat nastavení na serveru, pokud na server kopii *web.config* nahrazuje projektu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="c4cb8-179">Použití **buď** z následujících postupů můžete spravovat nastavení:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-179">Use **either** of the following approaches to manage the settings:</span></span>

* <span data-ttu-id="c4cb8-180">Pomocí Správce služby IIS k resetování nastavení ve *web.config* souboru po souboru se přepíše při nasazení.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
* <span data-ttu-id="c4cb8-181">Přidat *souboru web.config* do aplikace místně s nastavením.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-181">Add a *web.config file* to the app locally with the settings.</span></span> <span data-ttu-id="c4cb8-182">Další informace najdete v tématu [konfigurace na straně vývoj](#development-side-configuration-with-a-local-webconfig-file) oddílu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-182">For more information, see the [Development-side configuration](#development-side-configuration-with-a-local-webconfig-file) section.</span></span>

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a><span data-ttu-id="c4cb8-183">Publikujte a nasaďte projekt do složky webu služby IIS</span><span class="sxs-lookup"><span data-stu-id="c4cb8-183">Publish and deploy your project to the IIS site folder</span></span>

<span data-ttu-id="c4cb8-184">Pomocí sady Visual Studio nebo rozhraní příkazového řádku .NET Core, publikujte a nasaďte aplikaci do cílové složky.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-184">Using Visual Studio or the .NET Core CLI, publish and deploy the app to the destination folder.</span></span>

<span data-ttu-id="c4cb8-185">Další informace o hostování se službou IIS publikování a nasazení, najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-185">For more information on hosting with IIS, publishing, and deployment, see the following topics:</span></span>

* [<span data-ttu-id="c4cb8-186">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="c4cb8-186">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

<span data-ttu-id="c4cb8-187">Spuštění aplikace a zkontrolujte, že funguje ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-187">Launch the app to verify Windows Authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="c4cb8-188">Povolení ověřování Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c4cb8-188">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="c4cb8-189">Přestože Kestrel nepodporuje ověřování Windows, můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys) pro zajištění podpory scénářů v místním prostředí ve Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-189">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="c4cb8-190">Následující příklad nastaví hostitel webové aplikace pro použití HTTP.sys s ověřováním Windows:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-190">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="c4cb8-191">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-191">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="c4cb8-192">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-192">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="c4cb8-193">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-193">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="c4cb8-194">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-194">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="c4cb8-195">Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-195">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="c4cb8-196">Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="c4cb8-196">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="c4cb8-197">Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="c4cb8-197">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="work-with-windows-authentication"></a><span data-ttu-id="c4cb8-198">Práce s ověřováním Windows</span><span class="sxs-lookup"><span data-stu-id="c4cb8-198">Work with Windows Authentication</span></span>

<span data-ttu-id="c4cb8-199">Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-199">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="c4cb8-200">Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-200">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="c4cb8-201">Zakázat anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="c4cb8-201">Disallow anonymous access</span></span>

<span data-ttu-id="c4cb8-202">Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-202">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="c4cb8-203">Pokud chcete zakázat anonymní přístup je nakonfigurovaný web služby IIS (nebo ovladač HTTP.sys), požadavek dosáhne nikdy vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-203">If the IIS site (or HTTP.sys) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="c4cb8-204">Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-204">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="c4cb8-205">Povolení anonymního přístupu</span><span class="sxs-lookup"><span data-stu-id="c4cb8-205">Allow anonymous access</span></span>

<span data-ttu-id="c4cb8-206">Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-206">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="c4cb8-207">`[Authorize]` Atribut umožňuje zabezpečit části aplikace, které skutečně vyžadují ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-207">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="c4cb8-208">`[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut využití v aplikacích, které umožňují anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-208">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="c4cb8-209">Zobrazit [jednoduchá autorizace](xref:security/authorization/simple) podrobnosti o použití atributu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-209">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="c4cb8-210">V ASP.NET Core 2.x, `[Authorize]` atribut vyžaduje další konfiguraci ve *Startup.cs* vybízí anonymních požadavků pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-210">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="c4cb8-211">Doporučená konfigurace mírně závisí na webovém serveru, který je používán.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-211">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="c4cb8-212">Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-212">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="c4cb8-213">[StatusCodePages middleware](xref:fundamentals/error-handling#usestatuscodepages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".</span><span class="sxs-lookup"><span data-stu-id="c4cb8-213">The [StatusCodePages middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="c4cb8-214">IIS</span><span class="sxs-lookup"><span data-stu-id="c4cb8-214">IIS</span></span>

<span data-ttu-id="c4cb8-215">Pokud používáte službu IIS, přidejte následující text do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-215">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="c4cb8-216">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="c4cb8-216">HTTP.sys</span></span>

<span data-ttu-id="c4cb8-217">Pokud používáte HTTP.sys, přidejte následující text do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="c4cb8-217">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="c4cb8-218">Zosobnění</span><span class="sxs-lookup"><span data-stu-id="c4cb8-218">Impersonation</span></span>

<span data-ttu-id="c4cb8-219">ASP.NET Core neimplementuje zosobnění.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-219">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="c4cb8-220">Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-220">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="c4cb8-221">Pokud je třeba explicitně provést akce jménem uživatele, použijte [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [terminálu vložené middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-221">If you need to explicitly perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="c4cb8-222">V tomto kontextu spuštění jedné akce a potom zavřete kontextu.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-222">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="c4cb8-223">`RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-223">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="c4cb8-224">Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-224">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

### <a name="claims-transformations"></a><span data-ttu-id="c4cb8-225">Transformace deklarací identity</span><span class="sxs-lookup"><span data-stu-id="c4cb8-225">Claims transformations</span></span>

<span data-ttu-id="c4cb8-226">Při hostování za nástrojem s režimem v procesu služby IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nevolá interně k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-226">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="c4cb8-227">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaném k transformaci deklarací identity po každém ověření není ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-227">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="c4cb8-228">Další informace a příklad kódu, který aktivuje transformace deklarací identity, při hostování v procesu najdete v tématu <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="c4cb8-228">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>
