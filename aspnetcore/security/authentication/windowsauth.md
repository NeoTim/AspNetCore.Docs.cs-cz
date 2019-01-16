---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pomocí služby IIS Express, IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 01/15/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: c98bdedcf943a9057c96a8e5d62615e400074899
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341651"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="b884e-103">Konfigurace ověřování Windows v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b884e-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="b884e-104">Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b884e-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b884e-105">[Ověřování Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) lze konfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="b884e-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="b884e-106">Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b884e-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="b884e-107">Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identity služby Active Directory domény nebo účty Windows k identifikaci uživatelů.</span><span class="sxs-lookup"><span data-stu-id="b884e-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="b884e-108">Ověřování Windows je nejvhodnější pro prostředí intranetu, kde uživatelé klientských aplikací a webové servery patří do stejné domény Windows.</span><span class="sxs-lookup"><span data-stu-id="b884e-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="b884e-109">Povolení ověřování Windows v aplikaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b884e-109">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="b884e-110">**Webovou aplikaci** šablony, které jsou k dispozici prostřednictvím sady Visual Studio nebo rozhraní příkazového řádku .NET Core může být nakonfigurované pro podporu ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="b884e-110">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b884e-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b884e-111">Visual Studio</span></span>](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a><span data-ttu-id="b884e-112">Použití šablony aplikace ověřování Windows pro nový projekt</span><span class="sxs-lookup"><span data-stu-id="b884e-112">Use the Windows Authentication app template for a new project</span></span>

<span data-ttu-id="b884e-113">V sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b884e-113">In Visual Studio:</span></span>

1. <span data-ttu-id="b884e-114">Vytvořte nový **webové aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b884e-114">Create a new **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="b884e-115">Vyberte **webovou aplikaci** ze seznamu šablon.</span><span class="sxs-lookup"><span data-stu-id="b884e-115">Select **Web Application** from the list of templates.</span></span>
1. <span data-ttu-id="b884e-116">Vyberte **změna ověřování** tlačítko a vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="b884e-116">Select the **Change Authentication** button and select **Windows Authentication**.</span></span>

<span data-ttu-id="b884e-117">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b884e-117">Run the app.</span></span> <span data-ttu-id="b884e-118">Uživatelské jméno se zobrazí v uživatelském rozhraní vygenerované aplikace.</span><span class="sxs-lookup"><span data-stu-id="b884e-118">The username appears in the rendered app's user interface.</span></span>

### <a name="manual-configuration-for-an-existing-project"></a><span data-ttu-id="b884e-119">Ruční konfigurace pro existující projekt</span><span class="sxs-lookup"><span data-stu-id="b884e-119">Manual configuration for an existing project</span></span>

<span data-ttu-id="b884e-120">Vlastnosti projektu umožňují povolit ověřování Windows a vypnutí anonymního ověřování:</span><span class="sxs-lookup"><span data-stu-id="b884e-120">The project's properties allow you to enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="b884e-121">Klikněte pravým tlačítkem na projekt v sadě Visual Studio **Průzkumníka řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="b884e-121">Right-click the project in Visual Studio's **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="b884e-122">Vyberte **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="b884e-122">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="b884e-123">Zrušte zaškrtnutí políčka pro **povolit anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="b884e-123">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="b884e-124">Zaškrtněte políčko pro **povolit ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="b884e-124">Select the check box for **Enable Windows Authentication**.</span></span>

<span data-ttu-id="b884e-125">Alternativně se dá nakonfigurovat vlastnosti v `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="b884e-125">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b884e-126">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b884e-126">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b884e-127">Použití **ověřování Windows** šablony aplikace.</span><span class="sxs-lookup"><span data-stu-id="b884e-127">Use the **Windows Authentication** app template.</span></span>

<span data-ttu-id="b884e-128">Spustit [dotnet nové](/dotnet/core/tools/dotnet-new) příkazů `webapp` argument (webové aplikace ASP.NET Core) a `--auth Windows` přepínače:</span><span class="sxs-lookup"><span data-stu-id="b884e-128">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

---

<span data-ttu-id="b884e-129">Při úpravě existujícího projektu, přesvědčte se, že soubor projektu obsahuje odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) **nebo** [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b884e-129">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="b884e-130">Povolení ověřování Windows pomocí služby IIS</span><span class="sxs-lookup"><span data-stu-id="b884e-130">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="b884e-131">Služba IIS použije [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b884e-131">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="b884e-132">Ověřování Windows je nakonfigurovaný pro službu IIS prostřednictvím *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="b884e-132">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="b884e-133">V následujících částech zobrazit postup:</span><span class="sxs-lookup"><span data-stu-id="b884e-133">The following sections show how to:</span></span>

* <span data-ttu-id="b884e-134">Zadejte místní *web.config* soubor, který aktivuje ověřování Windows na serveru při nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b884e-134">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="b884e-135">Pomocí Správce služby IIS ke konfiguraci *web.config* souboru aplikace v ASP.NET Core, která již byla nasazena na server.</span><span class="sxs-lookup"><span data-stu-id="b884e-135">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="b884e-136">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="b884e-136">IIS configuration</span></span>

<span data-ttu-id="b884e-137">Pokud jste tak již neučinili, povolte službu IIS pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b884e-137">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="b884e-138">Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="b884e-138">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="b884e-139">Povolte službu Role služby IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="b884e-139">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="b884e-140">Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="b884e-140">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="b884e-141">Middleware pro integraci služby IIS je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti.</span><span class="sxs-lookup"><span data-stu-id="b884e-141">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="b884e-142">Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: Možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="b884e-142">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="b884e-143">Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b884e-143">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="b884e-144">Další informace najdete v tématu [odkaz na modul ASP.NET Core konfigurace: Atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="b884e-144">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="b884e-145">Vytvořit nový web služby IIS</span><span class="sxs-lookup"><span data-stu-id="b884e-145">Create a new IIS site</span></span>

<span data-ttu-id="b884e-146">Zadejte název a složku a mohla vytvořit nový fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="b884e-146">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="enable-windows-authentication-for-the-app-in-iis"></a><span data-ttu-id="b884e-147">Povolení ověřování Windows pro aplikace ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="b884e-147">Enable Windows Authentication for the app in IIS</span></span>

<span data-ttu-id="b884e-148">Použití **buď** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="b884e-148">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="b884e-149">[Konfigurace na straně vývoj před publikováním aplikace](#development-side-configuration-with-a-local-webconfig-file) (*doporučená*)</span><span class="sxs-lookup"><span data-stu-id="b884e-149">[Development-side configuration before publishing the app](#development-side-configuration-with-a-local-webconfig-file) (*Recommended*)</span></span>
* [<span data-ttu-id="b884e-150">Konfigurace na straně serveru po publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="b884e-150">Server-side configuration after publishing the app</span></span>](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a><span data-ttu-id="b884e-151">Konfigurace na straně pro vývoj se souborem web.config místní</span><span class="sxs-lookup"><span data-stu-id="b884e-151">Development-side configuration with a local web.config file</span></span>

<span data-ttu-id="b884e-152">Proveďte následující kroky **před** vám [publikujte a nasaďte svůj projekt](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="b884e-152">Perform the following steps **before** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

<span data-ttu-id="b884e-153">Přidejte následující *web.config* souboru do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="b884e-153">Add the following *web.config* file to the project root:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

<span data-ttu-id="b884e-154">Při publikování projektu sada SDK (bez `<IsTransformWebConfigDisabled>` vlastnost nastavena na hodnotu `true` v souboru projektu), publikovanému *web.config* soubor obsahuje `<location><system.webServer><security><authentication>` oddílu.</span><span class="sxs-lookup"><span data-stu-id="b884e-154">When the project is published by the SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="b884e-155">Další informace o `<IsTransformWebConfigDisabled>` vlastnost, naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="b884e-155">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

#### <a name="server-side-configuration-with-the-iis-manager"></a><span data-ttu-id="b884e-156">Konfigurace na straně serveru pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="b884e-156">Server-side configuration with the IIS Manager</span></span>

<span data-ttu-id="b884e-157">Proveďte následující kroky **po** vám [publikujte a nasaďte svůj projekt](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="b884e-157">Perform the following steps **after** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

1. <span data-ttu-id="b884e-158">Ve Správci služby IIS vyberte web služby IIS v části **lokality** uzlu **připojení** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="b884e-158">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
1. <span data-ttu-id="b884e-159">Dvakrát klikněte na panel **ověřování** v **IIS** oblasti.</span><span class="sxs-lookup"><span data-stu-id="b884e-159">Double-click **Authentication** in the **IIS** area.</span></span>
1. <span data-ttu-id="b884e-160">Vyberte **anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="b884e-160">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="b884e-161">Vyberte **zakázat** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="b884e-161">Select **Disable** in the **Actions** sidebar.</span></span>
1. <span data-ttu-id="b884e-162">Vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="b884e-162">Select **Windows Authentication**.</span></span> <span data-ttu-id="b884e-163">Vyberte **povolit** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="b884e-163">Select **Enable** in the **Actions** sidebar.</span></span>

<span data-ttu-id="b884e-164">Když se provedou tyto akce, Správce služby IIS upraví aplikace *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="b884e-164">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="b884e-165">A `<system.webServer><security><authentication>` s aktualizovaným nastavením pro přidání uzlu `anonymousAuthentication` a `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="b884e-165">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

<span data-ttu-id="b884e-166">`<system.webServer>` Přidá do části *web.config* soubor pomocí Správce služby IIS je mimo aplikaci prvku `<location>` části přidá .NET Core SDK, když je aplikace publikována.</span><span class="sxs-lookup"><span data-stu-id="b884e-166">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="b884e-167">Vzhledem k tomu, že v části Přidání mimo `<location>` uzlu nastavení dědí žádné [dílčí aplikace](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace.</span><span class="sxs-lookup"><span data-stu-id="b884e-167">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="b884e-168">Chcete-li zabránit dědění, přesuňte přidaného `<security>` části uvnitř `<location><system.webServer>` oddíl, který poskytuje sady SDK.</span><span class="sxs-lookup"><span data-stu-id="b884e-168">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the SDK provided.</span></span>

<span data-ttu-id="b884e-169">Když správce služby IIS se používá k přidání konfigurace služby IIS, ovlivní pouze aplikace *web.config* souboru na serveru.</span><span class="sxs-lookup"><span data-stu-id="b884e-169">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="b884e-170">Následné nasazení aplikace může přepsat nastavení na serveru, pokud na server kopii *web.config* nahrazuje projektu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="b884e-170">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="b884e-171">Použití **buď** z následujících postupů můžete spravovat nastavení:</span><span class="sxs-lookup"><span data-stu-id="b884e-171">Use **either** of the following approaches to manage the settings:</span></span>

* <span data-ttu-id="b884e-172">Pomocí Správce služby IIS k resetování nastavení ve *web.config* souboru po souboru se přepíše při nasazení.</span><span class="sxs-lookup"><span data-stu-id="b884e-172">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
* <span data-ttu-id="b884e-173">Přidat *souboru web.config* do aplikace místně s nastavením.</span><span class="sxs-lookup"><span data-stu-id="b884e-173">Add a *web.config file* to the app locally with the settings.</span></span> <span data-ttu-id="b884e-174">Další informace najdete v tématu [konfigurace na straně vývoj](#development-side-configuration-with-a-local-webconfig-file) oddílu.</span><span class="sxs-lookup"><span data-stu-id="b884e-174">For more information, see the [Development-side configuration](#development-side-configuration-with-a-local-webconfig-file) section.</span></span>

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a><span data-ttu-id="b884e-175">Publikujte a nasaďte projekt do složky webu služby IIS</span><span class="sxs-lookup"><span data-stu-id="b884e-175">Publish and deploy your project to the IIS site folder</span></span>

<span data-ttu-id="b884e-176">Pomocí sady Visual Studio nebo rozhraní příkazového řádku .NET Core, publikujte a nasaďte aplikaci do cílové složky.</span><span class="sxs-lookup"><span data-stu-id="b884e-176">Using Visual Studio or the .NET Core CLI, publish and deploy the app to the destination folder.</span></span>

<span data-ttu-id="b884e-177">Další informace o hostování se službou IIS publikování a nasazení, najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b884e-177">For more information on hosting with IIS, publishing, and deployment, see the following topics:</span></span>

* [<span data-ttu-id="b884e-178">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="b884e-178">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

<span data-ttu-id="b884e-179">Spuštění aplikace a zkontrolujte, že funguje ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="b884e-179">Launch the app to verify Windows Authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="b884e-180">Povolení ověřování Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b884e-180">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="b884e-181">Přestože Kestrel nepodporuje ověřování Windows, můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys) pro zajištění podpory scénářů v místním prostředí ve Windows.</span><span class="sxs-lookup"><span data-stu-id="b884e-181">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="b884e-182">Následující příklad nastaví hostitel webové aplikace pro použití HTTP.sys s ověřováním Windows:</span><span class="sxs-lookup"><span data-stu-id="b884e-182">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="b884e-183">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="b884e-183">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="b884e-184">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="b884e-184">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="b884e-185">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="b884e-185">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="b884e-186">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="b884e-186">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="b884e-187">Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="b884e-187">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="b884e-188">Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="b884e-188">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="b884e-189">Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="b884e-189">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="work-with-windows-authentication"></a><span data-ttu-id="b884e-190">Práce s ověřováním Windows</span><span class="sxs-lookup"><span data-stu-id="b884e-190">Work with Windows Authentication</span></span>

<span data-ttu-id="b884e-191">Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b884e-191">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="b884e-192">Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.</span><span class="sxs-lookup"><span data-stu-id="b884e-192">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="b884e-193">Zakázat anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="b884e-193">Disallow anonymous access</span></span>

<span data-ttu-id="b884e-194">Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek.</span><span class="sxs-lookup"><span data-stu-id="b884e-194">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="b884e-195">Pokud chcete zakázat anonymní přístup je nakonfigurovaný web služby IIS (nebo ovladač HTTP.sys), požadavek dosáhne nikdy vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="b884e-195">If the IIS site (or HTTP.sys) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="b884e-196">Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.</span><span class="sxs-lookup"><span data-stu-id="b884e-196">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="b884e-197">Povolení anonymního přístupu</span><span class="sxs-lookup"><span data-stu-id="b884e-197">Allow anonymous access</span></span>

<span data-ttu-id="b884e-198">Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy.</span><span class="sxs-lookup"><span data-stu-id="b884e-198">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="b884e-199">`[Authorize]` Atribut umožňuje zabezpečit části aplikace, které skutečně vyžadují ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="b884e-199">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="b884e-200">`[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut využití v aplikacích, které umožňují anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="b884e-200">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="b884e-201">Zobrazit [jednoduchá autorizace](xref:security/authorization/simple) podrobnosti o použití atributu.</span><span class="sxs-lookup"><span data-stu-id="b884e-201">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="b884e-202">V ASP.NET Core 2.x, `[Authorize]` atribut vyžaduje další konfiguraci ve *Startup.cs* vybízí anonymních požadavků pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="b884e-202">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="b884e-203">Doporučená konfigurace mírně závisí na webovém serveru, který je používán.</span><span class="sxs-lookup"><span data-stu-id="b884e-203">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="b884e-204">Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="b884e-204">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="b884e-205">[StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".</span><span class="sxs-lookup"><span data-stu-id="b884e-205">The [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="b884e-206">IIS</span><span class="sxs-lookup"><span data-stu-id="b884e-206">IIS</span></span>

<span data-ttu-id="b884e-207">Pokud používáte službu IIS, přidejte následující text do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="b884e-207">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="b884e-208">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b884e-208">HTTP.sys</span></span>

<span data-ttu-id="b884e-209">Pokud používáte HTTP.sys, přidejte následující text do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="b884e-209">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="b884e-210">Zosobnění</span><span class="sxs-lookup"><span data-stu-id="b884e-210">Impersonation</span></span>

<span data-ttu-id="b884e-211">ASP.NET Core neimplementuje zosobnění.</span><span class="sxs-lookup"><span data-stu-id="b884e-211">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="b884e-212">Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu.</span><span class="sxs-lookup"><span data-stu-id="b884e-212">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="b884e-213">Pokud je třeba explicitně provést akce jménem uživatele, použijte [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [terminálu vložené middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b884e-213">If you need to explicitly perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="b884e-214">V tomto kontextu spuštění jedné akce a potom zavřete kontextu.</span><span class="sxs-lookup"><span data-stu-id="b884e-214">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="b884e-215">`RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře.</span><span class="sxs-lookup"><span data-stu-id="b884e-215">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="b884e-216">Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.</span><span class="sxs-lookup"><span data-stu-id="b884e-216">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>
