---
title: Konfigurace ověřování Windows v ASP.NET Core
author: scottaddie
description: Zjistěte, jak nakonfigurovat ověřování Windows v ASP.NET Core pomocí služby IIS Express, IIS, ovladač HTTP.sys a WebListener.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/01/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: 342759a6ff4b5679e0d54c979188ae66d339562d
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121294"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="a4eb5-103">Konfigurace ověřování Windows v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4eb5-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="a4eb5-104">Podle [Steve Smith](https://ardalis.com) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a4eb5-104">By [Steve Smith](https://ardalis.com) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a4eb5-105">Ověřování Windows se dá nakonfigurovat pro aplikace ASP.NET Core hostované službou IIS, [HTTP.sys](xref:fundamentals/servers/httpsys), nebo [WebListener](xref:fundamentals/servers/weblistener).</span><span class="sxs-lookup"><span data-stu-id="a4eb5-105">Windows Authentication can be configured for ASP.NET Core apps hosted with IIS, [HTTP.sys](xref:fundamentals/servers/httpsys), or [WebListener](xref:fundamentals/servers/weblistener).</span></span>

## <a name="windows-authentication"></a><span data-ttu-id="a4eb5-106">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="a4eb5-106">Windows Authentication</span></span>

<span data-ttu-id="a4eb5-107">Ověřování Windows závisí na operačním systému k ověření uživatelů z aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="a4eb5-108">Ověřování Windows můžete použít, pokud váš server běží v podnikové síti pomocí identit domény služby Active Directory nebo jiné účty Windows k identifikaci uživatelů.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or other Windows accounts to identify users.</span></span> <span data-ttu-id="a4eb5-109">Ověřování Windows je nejvhodnější pro prostředí intranetu, ve kterých uživatelé, klientské aplikace a webové servery patří do stejné domény Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-109">Windows Authentication is best suited to intranet environments in which users, client applications, and web servers belong to the same Windows domain.</span></span>

<span data-ttu-id="a4eb5-110">[Další informace o ověřování Windows a nainstalovat ho pro službu IIS](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="a4eb5-110">[Learn more about Windows Authentication and installing it for IIS](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="a4eb5-111">Povolení ověřování Windows v aplikaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4eb5-111">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="a4eb5-112">Šablony Visual Studio webové aplikace mohou být nakonfigurované pro podporu ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-112">The Visual Studio Web Application template can be configured to support Windows Authentication.</span></span>

### <a name="use-the-windows-authentication-app-template"></a><span data-ttu-id="a4eb5-113">Použití šablony aplikace ověřování Windows</span><span class="sxs-lookup"><span data-stu-id="a4eb5-113">Use the Windows Authentication app template</span></span>

<span data-ttu-id="a4eb5-114">V sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a4eb5-114">In Visual Studio:</span></span>

1. <span data-ttu-id="a4eb5-115">Vytvořte novou webovou aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-115">Create a new ASP.NET Core Web Application.</span></span>
1. <span data-ttu-id="a4eb5-116">Vyberte webovou aplikaci ze seznamu šablon.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-116">Select Web Application from the list of templates.</span></span>
1. <span data-ttu-id="a4eb5-117">Vyberte **změna ověřování** tlačítko a vyberte **ověřování Windows**.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-117">Select the **Change Authentication** button and select **Windows Authentication**.</span></span>

<span data-ttu-id="a4eb5-118">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-118">Run the app.</span></span> <span data-ttu-id="a4eb5-119">Uživatelské jméno se zobrazí v horní části přímo z aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-119">The username appears in the top right of the app.</span></span>

![Snímek obrazovky prohlížeče ověřování Windows](windowsauth/_static/browser-screenshot.png)

<span data-ttu-id="a4eb5-121">Při vývojových pracích pomocí služby IIS Express Šablona nabízí veškeré konfigurace nezbytné pro použití ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-121">For development work using IIS Express, the template provides all the configuration necessary to use Windows Authentication.</span></span> <span data-ttu-id="a4eb5-122">Následující část popisuje postup při ruční konfiguraci aplikace ASP.NET Core pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-122">The following section shows how to manually configure an ASP.NET Core app for Windows Authentication.</span></span>

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a><span data-ttu-id="a4eb5-123">Nastavení sady Visual Studio pro Windows a anonymní ověřování</span><span class="sxs-lookup"><span data-stu-id="a4eb5-123">Visual Studio settings for Windows and anonymous authentication</span></span>

<span data-ttu-id="a4eb5-124">Projekt aplikace Visual Studio **vlastnosti** stránky **ladění** karta obsahuje zaškrtávací políčka pro ověřování Windows a anonymní ověřování.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-124">The Visual Studio project **Properties** page's **Debug** tab provides check boxes for Windows Authentication and anonymous authentication.</span></span>

![Snímek obrazovky prohlížeče ověřování Windows se zvýrazněnou možností ověřování](windowsauth/_static/vs-auth-property-menu.png)

<span data-ttu-id="a4eb5-126">Alternativně se dá nakonfigurovat tyto dvě vlastnosti v *launchSettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="a4eb5-126">Alternatively, these two properties can be configured in the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="a4eb5-127">Povolení ověřování Windows pomocí služby IIS</span><span class="sxs-lookup"><span data-stu-id="a4eb5-127">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="a4eb5-128">Služba IIS použije [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) pro hostování aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-128">IIS uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="a4eb5-129">Ověřování Windows konfigurován ve službě IIS, ne aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-129">Windows Authentication is configured in IIS, not the app.</span></span> <span data-ttu-id="a4eb5-130">Následující části vysvětlují, jak pomocí Správce služby IIS ke konfiguraci aplikace ASP.NET Core používat ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-130">The following sections show how to use IIS Manager to configure an ASP.NET Core app to use Windows Authentication.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="a4eb5-131">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="a4eb5-131">IIS configuration</span></span>

<span data-ttu-id="a4eb5-132">Povolte službu Role služby IIS pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-132">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="a4eb5-133">Další informace najdete v tématu [povolit ověřování Windows služby Role služby IIS (viz krok 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="a4eb5-133">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="a4eb5-134">Middleware pro integraci služby IIS je ve výchozím nastavení nakonfigurované k automatickému ověření žádosti.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-134">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="a4eb5-135">Další informace najdete v tématu [hostitele ASP.NET Core ve Windows se službou IIS: IIS možnosti (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="a4eb5-135">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="a4eb5-136">Modul ASP.NET Core je ve výchozím nastavení nakonfigurovaný pro předávání Windows ověřovací token do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-136">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="a4eb5-137">Další informace najdete v tématu [odkaz Konfigurace modul ASP.NET Core: atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="a4eb5-137">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="a4eb5-138">Vytvořit nový web služby IIS</span><span class="sxs-lookup"><span data-stu-id="a4eb5-138">Create a new IIS site</span></span>

<span data-ttu-id="a4eb5-139">Zadejte název a složku a mohla vytvořit nový fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-139">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="customize-authentication"></a><span data-ttu-id="a4eb5-140">Přizpůsobení ověřování</span><span class="sxs-lookup"><span data-stu-id="a4eb5-140">Customize authentication</span></span>

<span data-ttu-id="a4eb5-141">Otevřete funkcích ověřování pro lokalitu.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-141">Open the Authentication features for the site.</span></span>

![Nabídka ověřování služby IIS](windowsauth/_static/iis-authentication-menu.png)

<span data-ttu-id="a4eb5-143">Zakázat anonymní ověřování a povolit ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-143">Disable Anonymous Authentication and enable Windows Authentication.</span></span>

![Nastavení ověřování služby IIS](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a><span data-ttu-id="a4eb5-145">Publikovat projekt do složky webu služby IIS</span><span class="sxs-lookup"><span data-stu-id="a4eb5-145">Publish your project to the IIS site folder</span></span>

<span data-ttu-id="a4eb5-146">Pomocí sady Visual Studio nebo rozhraní příkazového řádku .NET Core, publikujte aplikaci do cílové složky.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-146">Using Visual Studio or the .NET Core CLI, publish the app to the destination folder.</span></span>

![Dialogové okno pro publikování aplikace Visual Studio](windowsauth/_static/vs-publish-app.png)

<span data-ttu-id="a4eb5-148">Další informace o [publikování do služby IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="a4eb5-148">Learn more about [publishing to IIS](xref:host-and-deploy/iis/index).</span></span>

<span data-ttu-id="a4eb5-149">Spuštění aplikace a zkontrolujte, že funguje ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-149">Launch the app to verify Windows Authentication is working.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="a4eb5-150">Povolení ověřování Windows pomocí HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a4eb5-150">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="a4eb5-151">Přestože Kestrel nepodporuje ověřování Windows, můžete použít [HTTP.sys](xref:fundamentals/servers/httpsys) pro zajištění podpory scénářů v místním prostředí ve Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-151">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="a4eb5-152">Následující příklad nastaví hostitel webové aplikace pro použití HTTP.sys s ověřováním Windows:</span><span class="sxs-lookup"><span data-stu-id="a4eb5-152">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="a4eb5-153">Ovladač HTTP.sys delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-153">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a4eb5-154">Režim ověřování uživatele nepodporuje protokolů Kerberos a HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-154">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a4eb5-155">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-155">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a4eb5-156">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-156">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a4eb5-157">Soubor HTTP.sys nepodporuje na Nano Server verze 1709 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-157">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="a4eb5-158">Chcete-li používat ověřování Windows a HTTP.sys s Nano serverem, použijte [jádra serveru (microsoft/windowsservercore) kontejneru](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="a4eb5-158">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="a4eb5-159">Další informace o jádra serveru najdete v tématu [co je možnost instalace jádra serveru v systému Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="a4eb5-159">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

## <a name="enable-windows-authentication-with-weblistener"></a><span data-ttu-id="a4eb5-160">Povolení ověřování Windows pomocí WebListener</span><span class="sxs-lookup"><span data-stu-id="a4eb5-160">Enable Windows Authentication with WebListener</span></span>

<span data-ttu-id="a4eb5-161">Přestože Kestrel nepodporuje ověřování Windows, můžete použít [WebListener](xref:fundamentals/servers/weblistener) pro zajištění podpory scénářů v místním prostředí ve Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-161">Although Kestrel doesn't support Windows Authentication, you can use [WebListener](xref:fundamentals/servers/weblistener) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="a4eb5-162">Následující příklad nastaví hostitel webové aplikace pro použití WebListener s ověřováním Windows:</span><span class="sxs-lookup"><span data-stu-id="a4eb5-162">The following example configures the app's web host to use WebListener with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample/Program1x.cs?highlight=6-11)]

> [!NOTE]
> <span data-ttu-id="a4eb5-163">WebListener delegáty pro ověřování v režimu jádra ověřování protokolem Kerberos.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-163">WebListener delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a4eb5-164">Režim ověřování uživatele nepodporuje protokolů Kerberos a WebListener.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-164">User mode authentication isn't supported with Kerberos and WebListener.</span></span> <span data-ttu-id="a4eb5-165">Účet počítače musí být použité k dešifrování token/lístek služby Kerberos, která se získá z Active Directory a předá klienta na serveru k ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-165">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a4eb5-166">Zaregistrujte hlavní název služby (SPN) příslušného hostitele není uživatel aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-166">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

::: moniker-end

## <a name="work-with-windows-authentication"></a><span data-ttu-id="a4eb5-167">Práce s ověřováním Windows</span><span class="sxs-lookup"><span data-stu-id="a4eb5-167">Work with Windows Authentication</span></span>

<span data-ttu-id="a4eb5-168">Stav konfigurace anonymního přístupu určuje způsob, jakým `[Authorize]` a `[AllowAnonymous]` atributy se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-168">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="a4eb5-169">Následující dvě části popisují, jak zpracovat stavy zakázaných a povolených Konfigurace anonymního přístupu.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-169">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="a4eb5-170">Zakázat anonymní přístup</span><span class="sxs-lookup"><span data-stu-id="a4eb5-170">Disallow anonymous access</span></span>

<span data-ttu-id="a4eb5-171">Když je povolené ověřování Windows a je zakázán anonymní přístup, `[Authorize]` a `[AllowAnonymous]` atributy nemají žádný účinek.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-171">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="a4eb5-172">Pokud web služby IIS (nebo HTTP.sys nebo WebListener server) je konfigurována tak anonymní přístup, požadavek dosáhne nikdy vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-172">If the IIS site (or HTTP.sys or WebListener server) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="a4eb5-173">Z tohoto důvodu `[AllowAnonymous]` atributu nelze použít.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-173">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="a4eb5-174">Povolení anonymního přístupu</span><span class="sxs-lookup"><span data-stu-id="a4eb5-174">Allow anonymous access</span></span>

<span data-ttu-id="a4eb5-175">Pokud jsou povolené ověřování Windows a anonymní přístup, použijte `[Authorize]` a `[AllowAnonymous]` atributy.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-175">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="a4eb5-176">`[Authorize]` Atribut umožňuje zabezpečit části aplikace, které skutečně vyžadují ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-176">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="a4eb5-177">`[AllowAnonymous]` Atribut přepsání `[Authorize]` atribut využití v aplikacích, které umožňují anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-177">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="a4eb5-178">Zobrazit [jednoduchá autorizace](xref:security/authorization/simple) podrobnosti o použití atributu.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-178">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="a4eb5-179">V ASP.NET Core 2.x, `[Authorize]` atribut vyžaduje další konfiguraci ve *Startup.cs* vybízí anonymních požadavků pro ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-179">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="a4eb5-180">Doporučená konfigurace mírně závisí na webovém serveru, který je používán.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-180">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="a4eb5-181">Ve výchozím nastavení uživatelé, kteří nemají oprávnění k získání přístupu ke stránce se zobrazí prázdnou odpověď HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-181">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="a4eb5-182">[StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) umožňují uživatelům poskytovat lepší prostředí "Přístup byl odepřen".</span><span class="sxs-lookup"><span data-stu-id="a4eb5-182">The [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="a4eb5-183">IIS</span><span class="sxs-lookup"><span data-stu-id="a4eb5-183">IIS</span></span>

<span data-ttu-id="a4eb5-184">Pokud používáte službu IIS, přidejte následující text do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="a4eb5-184">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="a4eb5-185">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a4eb5-185">HTTP.sys</span></span>

<span data-ttu-id="a4eb5-186">Pokud používáte HTTP.sys, přidejte následující text do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="a4eb5-186">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="a4eb5-187">Zosobnění</span><span class="sxs-lookup"><span data-stu-id="a4eb5-187">Impersonation</span></span>

<span data-ttu-id="a4eb5-188">ASP.NET Core neimplementuje zosobnění.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-188">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="a4eb5-189">Aplikace běží s identitou aplikace pro všechny požadavky pomocí aplikace identity fondu nebo procesu.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-189">Apps run with the application identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="a4eb5-190">Pokud je třeba explicitně provést akce jménem uživatele, použijte `WindowsIdentity.RunImpersonated`.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-190">If you need to explicitly perform an action on behalf of a user, use `WindowsIdentity.RunImpersonated`.</span></span> <span data-ttu-id="a4eb5-191">V tomto kontextu spuštění jedné akce a potom zavřete kontextu.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-191">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

<span data-ttu-id="a4eb5-192">Všimněte si, že `RunImpersonated` nepodporuje asynchronní operace by se neměl používat pro komplexní scénáře.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-192">Note that `RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="a4eb5-193">Například obtékání celý požadavky nebo middleware zřetězen není podporován nebo doporučené.</span><span class="sxs-lookup"><span data-stu-id="a4eb5-193">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>
