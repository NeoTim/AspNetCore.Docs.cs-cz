---
title: Monitorování a ladění – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Monitorování a ladění kódu jako součást řešení DevOps s využitím ASP.NET Core a Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/monitor
ms.openlocfilehash: d5d5189563760cb8a61c188436caa3838e75a31e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626906"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="cd20f-103">Monitorování a ladění</span><span class="sxs-lookup"><span data-stu-id="cd20f-103">Monitor and debug</span></span>

<span data-ttu-id="cd20f-104">Když jste nasadili aplikaci a vytvořili kanál DevOps, je důležité pochopit, jak monitorovat a řešit potíže s aplikací.</span><span class="sxs-lookup"><span data-stu-id="cd20f-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="cd20f-105">V této části provedete následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="cd20f-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="cd20f-106">Vyhledání základních dat monitorování a řešení potíží v Azure Portal</span><span class="sxs-lookup"><span data-stu-id="cd20f-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="cd20f-107">Přečtěte si, jak Azure Monitor poskytuje hlubší přehled o metrikách napříč všemi službami Azure.</span><span class="sxs-lookup"><span data-stu-id="cd20f-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="cd20f-108">Připojení webové aplikace s Application Insights pro profilaci aplikace</span><span class="sxs-lookup"><span data-stu-id="cd20f-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="cd20f-109">Zapnout protokolování a zjistit, kde stahovat protokoly</span><span class="sxs-lookup"><span data-stu-id="cd20f-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="cd20f-110">Streamování protokolů v reálném čase</span><span class="sxs-lookup"><span data-stu-id="cd20f-110">Stream logs in real time</span></span>
* <span data-ttu-id="cd20f-111">Informace o nastavení výstrah</span><span class="sxs-lookup"><span data-stu-id="cd20f-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="cd20f-112">Přečtěte si o vzdáleném ladění Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="cd20f-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="cd20f-113">Základní monitorování a řešení potíží</span><span class="sxs-lookup"><span data-stu-id="cd20f-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="cd20f-114">App Service webové aplikace lze snadno sledovat v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="cd20f-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="cd20f-115">Azure Portal vykresluje metriky v snadno srozumitelných grafech a grafech.</span><span class="sxs-lookup"><span data-stu-id="cd20f-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="cd20f-116">Otevřete [Azure Portal](https://portal.azure.com)a pak přejděte na App Service \*MyWebApp \<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="cd20f-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="cd20f-117">Karta **Přehled** zobrazuje užitečné informace, včetně grafů, které zobrazují nedávné metriky.</span><span class="sxs-lookup"><span data-stu-id="cd20f-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Snímek obrazovky znázorňující panel přehledu](./media/monitoring/overview.png)

    * <span data-ttu-id="cd20f-119">**Http 5xx**: počet chyb na straně serveru, obvykle výjimky v kódu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd20f-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="cd20f-120">**Data v**: příchozí data přicházejí do vaší webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd20f-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="cd20f-121">**Data odchozí**: odchozí přenos dat z vaší webové aplikace na klienty.</span><span class="sxs-lookup"><span data-stu-id="cd20f-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="cd20f-122">**Požadavky**: počet požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd20f-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="cd20f-123">**Průměrná doba odezvy**: Průměrná doba, jakou webové aplikace reagují na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="cd20f-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="cd20f-124">Na této stránce se taky nachází několik samoobslužných nástrojů pro řešení potíží a optimalizaci.</span><span class="sxs-lookup"><span data-stu-id="cd20f-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Snímek obrazovky zobrazující nástroje pro samoobslužné služby](./media/monitoring/wizards.png)

    * <span data-ttu-id="cd20f-126">**Diagnostiku a řešení problémů** je samoobslužný Poradce při potížích.</span><span class="sxs-lookup"><span data-stu-id="cd20f-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="cd20f-127">**Application Insights** je pro profilaci výkonu a aplikace a zabývá se dále v této části.</span><span class="sxs-lookup"><span data-stu-id="cd20f-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="cd20f-128">**App Service Advisor** vytváří doporučení pro optimalizaci prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd20f-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="cd20f-129">Pokročilé sledování</span><span class="sxs-lookup"><span data-stu-id="cd20f-129">Advanced monitoring</span></span>

<span data-ttu-id="cd20f-130">[Azure monitor](/azure/monitoring-and-diagnostics/) je centralizovaná služba pro monitorování všech metrik a nastavení výstrah napříč službami Azure.</span><span class="sxs-lookup"><span data-stu-id="cd20f-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="cd20f-131">V rámci Azure Monitor můžou správci podrobně sledovat výkon a identifikovat trendy.</span><span class="sxs-lookup"><span data-stu-id="cd20f-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="cd20f-132">Každá služba Azure nabízí svou vlastní [sadu metrik](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) pro Azure monitor.</span><span class="sxs-lookup"><span data-stu-id="cd20f-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="cd20f-133">Profil s Application Insights</span><span class="sxs-lookup"><span data-stu-id="cd20f-133">Profile with Application Insights</span></span>

<span data-ttu-id="cd20f-134">[Application Insights](/azure/application-insights/app-insights-overview) je služba Azure, která slouží k analýze výkonu a stability webových aplikací a způsobu jejich používání uživateli.</span><span class="sxs-lookup"><span data-stu-id="cd20f-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="cd20f-135">Data z Application Insights jsou širší a hlubší než Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="cd20f-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="cd20f-136">Data mohou vývojářům a správcům poskytnout klíčové informace pro zlepšení aplikací.</span><span class="sxs-lookup"><span data-stu-id="cd20f-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="cd20f-137">Application Insights lze přidat do prostředku Azure App Service bez změny kódu.</span><span class="sxs-lookup"><span data-stu-id="cd20f-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="cd20f-138">Otevřete [Azure Portal](https://portal.azure.com)a pak přejděte na App Service \*MyWebApp \<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="cd20f-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="cd20f-139">Na kartě **Přehled** klikněte na dlaždici **Application Insights** .</span><span class="sxs-lookup"><span data-stu-id="cd20f-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Dlaždice Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="cd20f-141">Zvolte přepínač **vytvořit nový prostředek** .</span><span class="sxs-lookup"><span data-stu-id="cd20f-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="cd20f-142">Použijte výchozí název prostředku a vyberte umístění prostředku Application Insights.</span><span class="sxs-lookup"><span data-stu-id="cd20f-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="cd20f-143">Toto umístění nemusí odpovídat vaší webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cd20f-143">The location doesn't need to match that of your web app.</span></span>

    ![Instalace Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="cd20f-145">Pro **modul runtime/rozhraní**vyberte možnost **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="cd20f-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="cd20f-146">Přijměte výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="cd20f-146">Accept the default settings.</span></span>
1. <span data-ttu-id="cd20f-147">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="cd20f-147">Select **OK**.</span></span> <span data-ttu-id="cd20f-148">Pokud se zobrazí výzva k potvrzení, vyberte **pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="cd20f-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="cd20f-149">Po vytvoření prostředku klikněte na název prostředku Application Insights, abyste mohli přejít přímo na stránku Application Insights.</span><span class="sxs-lookup"><span data-stu-id="cd20f-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Nový prostředek Application Insights je připravený.](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="cd20f-151">Při použití aplikace se shromažďují data.</span><span class="sxs-lookup"><span data-stu-id="cd20f-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="cd20f-152">Výběrem **aktualizovat** znovu načtěte okno s novými daty.</span><span class="sxs-lookup"><span data-stu-id="cd20f-152">Select **Refresh** to reload the blade with new data.</span></span>

![Karta Přehled Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="cd20f-154">Application Insights poskytuje užitečné informace na straně serveru bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd20f-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="cd20f-155">Pokud chcete získat co nejvíc hodnot z Application Insights, [Instrumentujte svoji aplikaci pomocí Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="cd20f-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="cd20f-156">Když je služba správně nakonfigurovaná, poskytuje kompletní monitorování mezi webovým serverem a prohlížečem, včetně výkonu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="cd20f-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="cd20f-157">Další informace najdete v dokumentaci k [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="cd20f-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="cd20f-158">Protokolování</span><span class="sxs-lookup"><span data-stu-id="cd20f-158">Logging</span></span>

<span data-ttu-id="cd20f-159">Protokoly webového serveru a aplikace jsou ve výchozím nastavení ve Azure App Service zakázané.</span><span class="sxs-lookup"><span data-stu-id="cd20f-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="cd20f-160">Povolte protokoly pomocí následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="cd20f-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="cd20f-161">Otevřete [Azure Portal](https://portal.azure.com)a přejděte do App Service \*MyWebApp \<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="cd20f-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="cd20f-162">V nabídce vlevo přejděte dolů k části **monitorování** .</span><span class="sxs-lookup"><span data-stu-id="cd20f-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="cd20f-163">Vyberte **diagnostické protokoly**.</span><span class="sxs-lookup"><span data-stu-id="cd20f-163">Select **Diagnostics logs**.</span></span>

    ![Odkaz na diagnostické protokoly](./media/monitoring/logging.png)

1. <span data-ttu-id="cd20f-165">Zapněte **protokolování aplikace (systém souborů)**.</span><span class="sxs-lookup"><span data-stu-id="cd20f-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="cd20f-166">Pokud se zobrazí výzva, kliknutím na toto políčko nainstalujete rozšíření a povolíte protokolování aplikace ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cd20f-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="cd20f-167">Nastavte **protokolování webového serveru** do **systému souborů**.</span><span class="sxs-lookup"><span data-stu-id="cd20f-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="cd20f-168">Zadejte **dobu uchování** ve dnech.</span><span class="sxs-lookup"><span data-stu-id="cd20f-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="cd20f-169">Například 30.</span><span class="sxs-lookup"><span data-stu-id="cd20f-169">For example, 30.</span></span>
1. <span data-ttu-id="cd20f-170">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="cd20f-170">Click **Save**.</span></span>

<span data-ttu-id="cd20f-171">Pro webovou aplikaci jsou vygenerovány protokoly ASP.NET Core a webový server (App Service).</span><span class="sxs-lookup"><span data-stu-id="cd20f-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="cd20f-172">Můžete je stáhnout pomocí zobrazených informací FTP/FTPS.</span><span class="sxs-lookup"><span data-stu-id="cd20f-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="cd20f-173">Heslo je stejné jako přihlašovací údaje pro nasazení vytvořené dříve v této příručce.</span><span class="sxs-lookup"><span data-stu-id="cd20f-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="cd20f-174">Protokoly můžou být [streamované přímo do vašeho místního počítače pomocí PowerShellu nebo Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="cd20f-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="cd20f-175">Protokoly lze také [Zobrazit v Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="cd20f-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="cd20f-176">Streamování protokolů</span><span class="sxs-lookup"><span data-stu-id="cd20f-176">Log streaming</span></span>

<span data-ttu-id="cd20f-177">Protokoly aplikací a webového serveru můžou být v reálném čase streamované prostřednictvím portálu.</span><span class="sxs-lookup"><span data-stu-id="cd20f-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="cd20f-178">Otevřete [Azure Portal](https://portal.azure.com)a přejděte do App Service \*MyWebApp \<unique_number\> \* .</span><span class="sxs-lookup"><span data-stu-id="cd20f-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="cd20f-179">V nabídce vlevo přejděte dolů k části **monitorování** a vyberte **log Stream**.</span><span class="sxs-lookup"><span data-stu-id="cd20f-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Snímek obrazovky ukazující odkaz na Stream protokolu](./media/monitoring/log-stream.png)

<span data-ttu-id="cd20f-181">Protokoly lze také [streamovat prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), včetně Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="cd20f-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="cd20f-182">Výstrahy</span><span class="sxs-lookup"><span data-stu-id="cd20f-182">Alerts</span></span>

<span data-ttu-id="cd20f-183">Azure Monitor také poskytuje [výstrahy v reálném čase](/azure/monitoring-and-diagnostics/insights-alerts-portal) na základě metrik, událostí správy a dalších kritérií.</span><span class="sxs-lookup"><span data-stu-id="cd20f-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="cd20f-184">*Poznámka: upozornění na metriky webové aplikace je dostupné jenom ve službě výstrahy (Classic).*</span><span class="sxs-lookup"><span data-stu-id="cd20f-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="cd20f-185">[Službu Výstrahy (Classic)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) najdete v Azure monitor nebo v části **monitorování** nastavení App Service.</span><span class="sxs-lookup"><span data-stu-id="cd20f-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Odkaz výstrahy (Classic)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="cd20f-187">Živé ladění</span><span class="sxs-lookup"><span data-stu-id="cd20f-187">Live debugging</span></span>

<span data-ttu-id="cd20f-188">Azure App Service lze [ladit vzdáleně pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) , když protokoly neposkytují dostatek informací.</span><span class="sxs-lookup"><span data-stu-id="cd20f-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="cd20f-189">Vzdálené ladění ale vyžaduje, aby se aplikace kompilována se symboly ladění.</span><span class="sxs-lookup"><span data-stu-id="cd20f-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="cd20f-190">Ladění by se nemělo provádět v produkčním prostředí, s výjimkou poslední.</span><span class="sxs-lookup"><span data-stu-id="cd20f-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="cd20f-191">Závěr</span><span class="sxs-lookup"><span data-stu-id="cd20f-191">Conclusion</span></span>

<span data-ttu-id="cd20f-192">V této části jste dokončili následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="cd20f-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="cd20f-193">Vyhledání základních dat monitorování a řešení potíží v Azure Portal</span><span class="sxs-lookup"><span data-stu-id="cd20f-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="cd20f-194">Přečtěte si, jak Azure Monitor poskytuje hlubší přehled o metrikách napříč všemi službami Azure.</span><span class="sxs-lookup"><span data-stu-id="cd20f-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="cd20f-195">Připojení webové aplikace s Application Insights pro profilaci aplikace</span><span class="sxs-lookup"><span data-stu-id="cd20f-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="cd20f-196">Zapnout protokolování a zjistit, kde stahovat protokoly</span><span class="sxs-lookup"><span data-stu-id="cd20f-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="cd20f-197">Streamování protokolů v reálném čase</span><span class="sxs-lookup"><span data-stu-id="cd20f-197">Stream logs in real time</span></span>
* <span data-ttu-id="cd20f-198">Informace o nastavení výstrah</span><span class="sxs-lookup"><span data-stu-id="cd20f-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="cd20f-199">Přečtěte si o vzdáleném ladění Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="cd20f-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="cd20f-200">Další materiály ke čtení</span><span class="sxs-lookup"><span data-stu-id="cd20f-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="cd20f-201">Monitorování výkonu webových aplikací Azure pomocí Application Insights</span><span class="sxs-lookup"><span data-stu-id="cd20f-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="cd20f-202">Povolit protokolování diagnostiky pro webové aplikace v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cd20f-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="cd20f-203">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd20f-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="cd20f-204">Vytváření klasických upozornění na metriky v Azure Monitor pro služby Azure – Azure Portal</span><span class="sxs-lookup"><span data-stu-id="cd20f-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
