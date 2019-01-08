---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pomocí služby IIS Express, IIS a HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 12/23/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: 64178c8fce71445fc6a728a236d811484b21e3e0
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099257"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="2d066-103">Konfigurace ověřování Windows v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d066-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="2d066-104">Podle [Scott Addie](https://twitter.com/Scott_Addie) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2d066-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2d066-105">[Ověřování Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) lze konfigurovat pro aplikace ASP.NET Core s hostitelem [IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2d066-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="2d066-106">Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d066-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="2d066-107">Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identity služby Active Directory domény nebo účty Windows k identifikaci uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2d066-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="2d066-108">Ověřování Windows je nejvhodnější pro prostředí intranetu, kde uživatelé klientských aplikací a webové servery patří do stejné domény Windows.</span><span class="sxs-lookup"><span data-stu-id="2d066-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="2d066-109">Povolení ověřování Windows v aplikaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d066-109">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="2d066-110">**Webovou aplikaci** šablony, které jsou k dispozici prostřednictvím sady Visual Studio nebo rozhraní příkazového řádku .NET Core může být nakonfigurované pro podporu ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="2d066-110">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2d066-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d066-111">Visual Studio</span></span>](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a><span data-ttu-id="2d066-112">Použití šablony aplikace ověřování Windows pro nový projekt</span><span class="sxs-lookup"><span data-stu-id="2d066-112">Use the Windows Authentication app template for a new project</span></span>

<span data-ttu-id="2d066-113">V sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="2d066-113">In Visual Studio:</span></span>

1. <span data-ttu-id="2d066-114">Vytvořte nový **webové aplikace ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2d066-114">Create a new **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="2d066-115">Vyberte **webovou aplikaci** ze seznamu šablon.</span><span class="sxs-lookup"><span data-stu-id="2d066-115">Select **Web Application** from the list of templates.</span></span>
1. <span data-ttu-id="2d066-116">Vyberte **změna ověřování** tlačítko a vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="2d066-116">Select the **Change Authentication** button and select **Windows Authentication**.</span></span>

<span data-ttu-id="2d066-117">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d066-117">Run the app.</span></span> <span data-ttu-id="2d066-118">Uživatelské jméno se zobrazí v uživatelském rozhraní vygenerované aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d066-118">The username appears in the rendered app's user interface.</span></span>

### <a name="manual-configuration-for-an-existing-project"></a><span data-ttu-id="2d066-119">Ruční konfigurace pro existující projekt</span><span class="sxs-lookup"><span data-stu-id="2d066-119">Manual configuration for an existing project</span></span>

<span data-ttu-id="2d066-120">Vlastnosti projektu umožňují povolit ověřování Windows a vypnutí anonymního ověřování:</span><span class="sxs-lookup"><span data-stu-id="2d066-120">The project's properties allow you to enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="2d066-121">Klikněte pravým tlačítkem na projekt v sadě Visual Studio **Průzkumníka řešení** a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="2d066-121">Right-click the project in Visual Studio's **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="2d066-122">Vyberte **ladění** kartu.</span><span class="sxs-lookup"><span data-stu-id="2d066-122">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="2d066-123">Zrušte zaškrtnutí políčka pro **povolit anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="2d066-123">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="2d066-124">Zaškrtněte políčko pro **povolit ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="2d066-124">Select the check box for **Enable Windows Authentication**.</span></span>

<span data-ttu-id="2d066-125">Alternativně se dá nakonfigurovat vlastnosti v `iisSettings` uzlu *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="2d066-125">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2d066-126">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="2d066-126">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2d066-127">Použití **ověřování Windows** šablony aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d066-127">Use the **Windows Authentication** app template.</span></span>

<span data-ttu-id="2d066-128">Spustit [dotnet nové](/dotnet/core/tools/dotnet-new) příkazů `webapp` argument (webové aplikace ASP.NET Core) a `--auth Windows` přepínače:</span><span class="sxs-lookup"><span data-stu-id="2d066-128">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

---

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="2d066-129">Povolení ověřování Windows pomocí služby IIS</span><span class="sxs-lookup"><span data-stu-id="2d066-129">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="2d066-130">Služba IIS použije [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d066-130">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="2d066-131">Ověřování Windows je nakonfigurovaný pro službu IIS prostřednictvím *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2d066-131">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="2d066-132">V následujících částech zobrazit postup:</span><span class="sxs-lookup"><span data-stu-id="2d066-132">The following sections show how to:</span></span>

* <span data-ttu-id="2d066-133">Zadejte místní *web.config* soubor, který aktivuje ověřování Windows na serveru při nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d066-133">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="2d066-134">Pomocí Správce služby IIS ke konfiguraci *web.config* souboru aplikace v ASP.NET Core, která již byla nasazena na server.</span><span class="sxs-lookup"><span data-stu-id="2d066-134">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="2d066-135">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="2d066-135">IIS configuration</span></span>

<span data-ttu-id="2d066-136">Pokud jste tak již neučinili, povolte službu IIS pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d066-136">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="2d066-137">Další informace naleznete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="2d066-137">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="2d066-138">Povolte službu Role služby IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="2d066-138">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="2d066-139">Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="2d066-139">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="2d066-140">Middleware pro integraci služby IIS je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti.</span><span class="sxs-lookup"><span data-stu-id="2d066-140">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="2d066-141">Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: Možnosti služby IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="2d066-141">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="2d066-142">Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d066-142">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="2d066-143">Další informace najdete v tématu [odkaz na modul ASP.NET Core konfigurace: Atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="2d066-143">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="2d066-144">Vytvořit nový web služby IIS</span><span class="sxs-lookup"><span data-stu-id="2d066-144">Create a new IIS site</span></span>

<span data-ttu-id="2d066-145">Zadejte název a složku a mohla vytvořit nový fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="2d066-145">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="enable-windows-authentication-for-the-app-in-iis"></a><span data-ttu-id="2d066-146">Povolení ověřování Windows pro aplikace ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="2d066-146">Enable Windows Authentication for the app in IIS</span></span>

<span data-ttu-id="2d066-147">Použití **buď** z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="2d066-147">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="2d066-148">[Konfigurace na straně vývoj před publikováním aplikace](#development-side-configuration-with-a-local-webconfig-file) (*doporučená*)</span><span class="sxs-lookup"><span data-stu-id="2d066-148">[Development-side configuration before publishing the app](#development-side-configuration-with-a-local-webconfig-file) (*Recommended*)</span></span>
* [<span data-ttu-id="2d066-149">Konfigurace na straně serveru po publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="2d066-149">Server-side configuration after publishing the app</span></span>](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a><span data-ttu-id="2d066-150">Konfigurace na straně pro vývoj se souborem web.config místní</span><span class="sxs-lookup"><span data-stu-id="2d066-150">Development-side configuration with a local web.config file</span></span>

<span data-ttu-id="2d066-151">Proveďte následující kroky **před** vám [publikujte a nasaďte svůj projekt](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="2d066-151">Perform the following steps **before** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

<span data-ttu-id="2d066-152">Přidejte následující *web.config* souboru do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="2d066-152">Add the following *web.config* file to the project root:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

<span data-ttu-id="2d066-153">Při publikování projektu sada SDK (bez `<IsTransformWebConfigDisabled>` vlastnost nastavena na hodnotu `true` v souboru projektu), publikovanému *web.config* soubor obsahuje `<location><system.webServer><security><authentication>` oddílu.</span><span class="sxs-lookup"><span data-stu-id="2d066-153">When the project is published by the SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="2d066-154">Další informace o `<IsTransformWebConfigDisabled>` vlastnost, naleznete v tématu <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="2d066-154">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

#### <a name="server-side-configuration-with-the-iis-manager"></a><span data-ttu-id="2d066-155">Konfigurace na straně serveru pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="2d066-155">Server-side configuration with the IIS Manager</span></span>

<span data-ttu-id="2d066-156">Proveďte následující kroky **po** vám [publikujte a nasaďte svůj projekt](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="2d066-156">Perform the following steps **after** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

1. <span data-ttu-id="2d066-157">Ve Správci služby IIS vyberte web služby IIS v části **lokality** uzlu **připojení** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="2d066-157">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
1. <span data-ttu-id="2d066-158">Dvakrát klikněte na panel **ověřování** v **IIS** oblasti.</span><span class="sxs-lookup"><span data-stu-id="2d066-158">Double-click **Authentication** in the **IIS** area.</span></span>
1. <span data-ttu-id="2d066-159">Vyberte **anonymní ověřování**.</span><span class="sxs-lookup"><span data-stu-id="2d066-159">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="2d066-160">Vyberte **zakázat** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="2d066-160">Select **Disable** in the **Actions** sidebar.</span></span>
1. <span data-ttu-id="2d066-161">Vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="2d066-161">Select **Windows Authentication**.</span></span> <span data-ttu-id="2d066-162">Vyberte **povolit** v **akce** bočním panelu.</span><span class="sxs-lookup"><span data-stu-id="2d066-162">Select **Enable** in the **Actions** sidebar.</span></span>

<span data-ttu-id="2d066-163">Když se provedou tyto akce, Správce služby IIS upraví aplikace *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2d066-163">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="2d066-164">A `<system.webServer><security><authentication>` s aktualizovaným nastavením pro přidání uzlu `anonymousAuthentication` a `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2d066-164">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

<span data-ttu-id="2d066-165">`<system.webServer>` Přidá do části *web.config* soubor pomocí Správce služby IIS je mimo aplikaci prvku `<location>` části přidá .NET Core SDK, když je aplikace publikována.</span><span class="sxs-lookup"><span data-stu-id="2d066-165">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="2d066-166">Vzhledem k tomu, že v části Přidání mimo `<location>` uzlu nastavení dědí žádné [dílčí aplikace](xref:host-and-deploy/iis/index#sub-applications) do aktuální aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d066-166">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="2d066-167">Chcete-li zabránit dědění, přesuňte přidaného `<security>` části uvnitř `<location><system.webServer>` oddíl, který poskytuje sady SDK.</span><span class="sxs-lookup"><span data-stu-id="2d066-167">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the SDK provided.</span></span>

<span data-ttu-id="2d066-168">Když správce služby IIS se používá k přidání konfigurace služby IIS, ovlivní pouze aplikace *web.config* souboru na serveru.</span><span class="sxs-lookup"><span data-stu-id="2d066-168">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="2d066-169">Následné nasazení aplikace může přepsat nastavení na serveru, pokud na server kopii *web.config* nahrazuje projektu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2d066-169">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="2d066-170">Použití **buď** z následujících postupů můžete spravovat nastavení:</span><span class="sxs-lookup"><span data-stu-id="2d066-170">Use **either** of the following approaches to manage the settings:</span></span>

* <span data-ttu-id="2d066-171">Pomocí Správce služby IIS k resetování nastavení ve *web.config* souboru po souboru se přepíše při nasazení.</span><span class="sxs-lookup"><span data-stu-id="2d066-171">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
* <span data-ttu-id="2d066-172">Přidat *souboru web.config* do aplikace místně s nastavením.</span><span class="sxs-lookup"><span data-stu-id="2d066-172">Add a *web.config file* to the app locally with the settings.</span></span> <span data-ttu-id="2d066-173">Další informace najdete v tématu [konfigurace na straně vývoj](#development-side-configuration-with-a-local-webconfig-file) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2d066-173">For more information, see the [Development-side configuration](#development-side-configuration-with-a-local-webconfig-file) section.</span></span>

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a><span data-ttu-id="2d066-174">Publikujte a nasaďte projekt do složky webu služby IIS</span><span class="sxs-lookup"><span data-stu-id="2d066-174">Publish and deploy your project to the IIS site folder</span></span>

<span data-ttu-id="2d066-175">Pomocí sady Visual Studio nebo rozhraní příkazového řádku .NET Core, publikujte a nasaďte aplikaci do cílové složky.</span><span class="sxs-lookup"><span data-stu-id="2d066-175">Using Visual Studio or the .NET Core CLI, publish and deploy the app to the destination folder.</span></span>

<span data-ttu-id="2d066-176">Další informace o hostování se službou IIS publikování a nasazení, najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="2d066-176">For more information on hosting with IIS, publishing, and deployment, see the following topics:</span></span>

* [<span data-ttu-id="2d066-177">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="2d066-177">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

<span data-ttu-id="2d066-178">Spuštění aplikace a zkontrolujte, že funguje ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="2d066-178">Launch the app to verify Windows Authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="2d066-179">Povolení ověřování Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="2d066-179">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="2d066-180">Přestože Kestrel nepodporuje ověřování Windows, můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys) pro zajištění podpory scénářů v místním prostředí ve Windows.</span><span class="sxs-lookup"><span data-stu-id="2d066-180">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="2d066-181">Následující příklad nastaví hostitel webové aplikace pro použití HTTP.sys s ověřováním Windows:</span><span class="sxs-lookup"><span data-stu-id="2d066-181">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="2d066-182">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="2d066-182">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="2d066-183">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="2d066-183">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="2d066-184">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="2d066-184">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="2d066-185">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d066-185">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="2d066-186">Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="2d066-186">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="2d066-187">Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="2d066-187">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="2d066-188">Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="2d066-188">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="work-with-windows-authentication"></a><span data-ttu-id="2d066-189">Práce s ověřováním Windows</span><span class="sxs-lookup"><span data-stu-id="2d066-189">Work with Windows Authentication</span></span>

<span data-ttu-id="2d066-190">Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d066-190">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="2d066-191">Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.</span><span class="sxs-lookup"><span data-stu-id="2d066-191">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="2d066-192">Zakázat anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="2d066-192">Disallow anonymous access</span></span>

<span data-ttu-id="2d066-193">Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek.</span><span class="sxs-lookup"><span data-stu-id="2d066-193">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="2d066-194">Pokud chcete zakázat anonymní přístup je nakonfigurovaný web služby IIS (nebo ovladač HTTP.sys), požadavek dosáhne nikdy vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d066-194">If the IIS site (or HTTP.sys) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="2d066-195">Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.</span><span class="sxs-lookup"><span data-stu-id="2d066-195">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="2d066-196">Povolení anonymního přístupu</span><span class="sxs-lookup"><span data-stu-id="2d066-196">Allow anonymous access</span></span>

<span data-ttu-id="2d066-197">Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy.</span><span class="sxs-lookup"><span data-stu-id="2d066-197">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="2d066-198">`[Authorize]` Atribut umožňuje zabezpečit části aplikace, které skutečně vyžadují ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="2d066-198">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="2d066-199">`[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut využití v aplikacích, které umožňují anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="2d066-199">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="2d066-200">Zobrazit [jednoduchá autorizace](xref:security/authorization/simple) podrobnosti o použití atributu.</span><span class="sxs-lookup"><span data-stu-id="2d066-200">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="2d066-201">V ASP.NET Core 2.x, `[Authorize]` atribut vyžaduje další konfiguraci ve *Startup.cs* vybízí anonymních požadavků pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="2d066-201">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="2d066-202">Doporučená konfigurace mírně závisí na webovém serveru, který je používán.</span><span class="sxs-lookup"><span data-stu-id="2d066-202">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="2d066-203">Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="2d066-203">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="2d066-204">[StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".</span><span class="sxs-lookup"><span data-stu-id="2d066-204">The [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="2d066-205">IIS</span><span class="sxs-lookup"><span data-stu-id="2d066-205">IIS</span></span>

<span data-ttu-id="2d066-206">Pokud používáte službu IIS, přidejte následující text do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2d066-206">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="2d066-207">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="2d066-207">HTTP.sys</span></span>

<span data-ttu-id="2d066-208">Pokud používáte HTTP.sys, přidejte následující text do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2d066-208">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="2d066-209">Zosobnění</span><span class="sxs-lookup"><span data-stu-id="2d066-209">Impersonation</span></span>

<span data-ttu-id="2d066-210">ASP.NET Core neimplementuje zosobnění.</span><span class="sxs-lookup"><span data-stu-id="2d066-210">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="2d066-211">Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu.</span><span class="sxs-lookup"><span data-stu-id="2d066-211">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="2d066-212">Pokud je třeba explicitně provést akce jménem uživatele, použijte [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) v [terminálu vložené middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2d066-212">If you need to explicitly perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="2d066-213">V tomto kontextu spuštění jedné akce a potom zavřete kontextu.</span><span class="sxs-lookup"><span data-stu-id="2d066-213">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="2d066-214">`RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře.</span><span class="sxs-lookup"><span data-stu-id="2d066-214">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="2d066-215">Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.</span><span class="sxs-lookup"><span data-stu-id="2d066-215">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>
