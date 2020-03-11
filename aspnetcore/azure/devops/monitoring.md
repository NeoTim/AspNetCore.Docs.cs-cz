---
title: Monitorování a ladění – DevOps s využitím ASP.NET Core a Azure
author: CamSoper
description: Monitorování a ladění kódu jako součást řešení DevOps s ASP.NET Core a Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659500"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="ad230-103">Monitorování a ladění</span><span class="sxs-lookup"><span data-stu-id="ad230-103">Monitor and debug</span></span>

<span data-ttu-id="ad230-104">S aplikaci nasadili a vytvořili kanál DevOps, je důležité pochopit, jak monitorovat a řešení potíží s aplikací.</span><span class="sxs-lookup"><span data-stu-id="ad230-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="ad230-105">V této části budete provádět následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="ad230-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="ad230-106">Najít základní monitorování a řešení potíží s dat na webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="ad230-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="ad230-107">Zjistěte, jak Azure Monitor poskytuje hlubší pohled na metriky v rámci všech služeb Azure</span><span class="sxs-lookup"><span data-stu-id="ad230-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="ad230-108">Připojení webové aplikace pomocí Application Insights pro aplikace pro profilaci</span><span class="sxs-lookup"><span data-stu-id="ad230-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="ad230-109">Zapnutí protokolování a zjistíte, kde ke stažení protokolů</span><span class="sxs-lookup"><span data-stu-id="ad230-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="ad230-110">Stream protokolů v reálném čase</span><span class="sxs-lookup"><span data-stu-id="ad230-110">Stream logs in real time</span></span>
* <span data-ttu-id="ad230-111">Zjistíte, kde nastavit výstrahy</span><span class="sxs-lookup"><span data-stu-id="ad230-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="ad230-112">Další informace o vzdálené ladění Azure App Service web apps.</span><span class="sxs-lookup"><span data-stu-id="ad230-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="ad230-113">Základní monitorování a řešení potíží</span><span class="sxs-lookup"><span data-stu-id="ad230-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="ad230-114">Aplikace služby App Service web apps je snadné sledování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="ad230-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="ad230-115">Na webu Azure portal vykreslí metriky na snadno pochopitelné tabulky a grafy.</span><span class="sxs-lookup"><span data-stu-id="ad230-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="ad230-116">Otevřete [Azure Portal](https://portal.azure.com)a potom přejděte do *mywebapp\<unique_number\>* App Service.</span><span class="sxs-lookup"><span data-stu-id="ad230-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="ad230-117">Karta **Přehled** zobrazuje užitečné informace, včetně grafů, které zobrazují nedávné metriky.</span><span class="sxs-lookup"><span data-stu-id="ad230-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Snímek obrazovky zobrazující Přehled panelu](./media/monitoring/overview.png)

    * <span data-ttu-id="ad230-119">**Http 5xx**: počet chyb na straně serveru, obvykle výjimky v kódu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad230-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="ad230-120">**Data v**: příchozí data přicházejí do vaší webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad230-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="ad230-121">**Data odchozí**: odchozí přenos dat z vaší webové aplikace na klienty.</span><span class="sxs-lookup"><span data-stu-id="ad230-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="ad230-122">**Požadavky**: počet požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad230-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="ad230-123">**Průměrná doba odezvy**: Průměrná doba, jakou webové aplikace reagují na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad230-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="ad230-124">Několik samoobslužné nástroje pro řešení potíží a optimalizace, které také najdete na této stránce.</span><span class="sxs-lookup"><span data-stu-id="ad230-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Snímek obrazovky znázorňující samoobslužné nástroje](./media/monitoring/wizards.png)

    * <span data-ttu-id="ad230-126">**Diagnostiku a řešení problémů** je samoobslužný Poradce při potížích.</span><span class="sxs-lookup"><span data-stu-id="ad230-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="ad230-127">**Application Insights** je pro profilaci výkonu a aplikace a zabývá se dále v této části.</span><span class="sxs-lookup"><span data-stu-id="ad230-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="ad230-128">**App Service Advisor** vytváří doporučení pro optimalizaci prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad230-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="ad230-129">Pokročilé sledování</span><span class="sxs-lookup"><span data-stu-id="ad230-129">Advanced monitoring</span></span>

<span data-ttu-id="ad230-130">[Azure monitor](/azure/monitoring-and-diagnostics/) je centralizovaná služba pro monitorování všech metrik a nastavení výstrah napříč službami Azure.</span><span class="sxs-lookup"><span data-stu-id="ad230-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="ad230-131">V rámci Azure Monitor správci podrobně sledovat výkon a identifikovat trendy.</span><span class="sxs-lookup"><span data-stu-id="ad230-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="ad230-132">Každá služba Azure nabízí svou vlastní [sadu metrik](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) pro Azure monitor.</span><span class="sxs-lookup"><span data-stu-id="ad230-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="ad230-133">Profil Application insights</span><span class="sxs-lookup"><span data-stu-id="ad230-133">Profile with Application Insights</span></span>

<span data-ttu-id="ad230-134">[Application Insights](/azure/application-insights/app-insights-overview) je služba Azure, která slouží k analýze výkonu a stability webových aplikací a způsobu jejich používání uživateli.</span><span class="sxs-lookup"><span data-stu-id="ad230-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="ad230-135">Data ze služby Application Insights je rozsáhlejší a podrobnější než u Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="ad230-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="ad230-136">Data můžete zadat, vývojáři a správci s informace o klíči pro zlepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad230-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="ad230-137">Application Insights je přidat do prostředku služby Azure App Service bez jakýchkoli změn kódu.</span><span class="sxs-lookup"><span data-stu-id="ad230-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="ad230-138">Otevřete [Azure Portal](https://portal.azure.com)a potom přejděte do *mywebapp\<unique_number\>* App Service.</span><span class="sxs-lookup"><span data-stu-id="ad230-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ad230-139">Na kartě **Přehled** klikněte na dlaždici **Application Insights** .</span><span class="sxs-lookup"><span data-stu-id="ad230-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Dlaždici služby Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="ad230-141">Zvolte přepínač **vytvořit nový prostředek** .</span><span class="sxs-lookup"><span data-stu-id="ad230-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="ad230-142">Použijte výchozí název prostředku a vyberte umístění pro prostředek služby Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ad230-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="ad230-143">Umístění se nemusí shodovat s vaší webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad230-143">The location doesn't need to match that of your web app.</span></span>

    ![Nastavení Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="ad230-145">Pro **modul runtime/rozhraní**vyberte možnost **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ad230-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="ad230-146">Přijměte výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="ad230-146">Accept the default settings.</span></span>
1. <span data-ttu-id="ad230-147">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="ad230-147">Select **OK**.</span></span> <span data-ttu-id="ad230-148">Pokud se zobrazí výzva k potvrzení, vyberte **pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="ad230-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="ad230-149">Po vytvoření prostředku, klikněte na název prostředku Application Insights a přejít přímo na stránku Application Insights.</span><span class="sxs-lookup"><span data-stu-id="ad230-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Nový prostředek Application Insights je připravený](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="ad230-151">Při použití aplikace, data hromadí.</span><span class="sxs-lookup"><span data-stu-id="ad230-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="ad230-152">Výběrem **aktualizovat** znovu načtěte okno s novými daty.</span><span class="sxs-lookup"><span data-stu-id="ad230-152">Select **Refresh** to reload the blade with new data.</span></span>

![Karta Přehled Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="ad230-154">Application Insights poskytuje užitečné informace na straně serveru bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ad230-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="ad230-155">Pokud chcete získat co nejvíc hodnot z Application Insights, [Instrumentujte svoji aplikaci pomocí Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="ad230-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="ad230-156">Pokud správně nakonfigurovaná, které služba poskytuje začátku do konce monitorování v rámci webového serveru a prohlížeče, včetně výkonu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="ad230-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="ad230-157">Další informace najdete v dokumentaci k [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="ad230-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="ad230-158">Protokolování</span><span class="sxs-lookup"><span data-stu-id="ad230-158">Logging</span></span>

<span data-ttu-id="ad230-159">Protokoly webového serveru a aplikace jsou zakázané ve výchozím nastavení ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ad230-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="ad230-160">Povolení protokolů pomocí následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="ad230-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="ad230-161">Otevřete [Azure Portal](https://portal.azure.com)a přejděte do *mywebapp\<unique_number\>* App Service.</span><span class="sxs-lookup"><span data-stu-id="ad230-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ad230-162">V nabídce vlevo přejděte dolů k části **monitorování** .</span><span class="sxs-lookup"><span data-stu-id="ad230-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="ad230-163">Vyberte **diagnostické protokoly**.</span><span class="sxs-lookup"><span data-stu-id="ad230-163">Select **Diagnostics logs**.</span></span>

    ![Diagnostické protokoly odkaz](./media/monitoring/logging.png)

1. <span data-ttu-id="ad230-165">Zapněte **protokolování aplikace (systém souborů)** .</span><span class="sxs-lookup"><span data-stu-id="ad230-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="ad230-166">Pokud se zobrazí výzva, klikněte na políčko pro instalaci rozšíření povolíte protokolování ve webové aplikaci app.</span><span class="sxs-lookup"><span data-stu-id="ad230-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="ad230-167">Nastavte **protokolování webového serveru** do **systému souborů**.</span><span class="sxs-lookup"><span data-stu-id="ad230-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="ad230-168">Zadejte **dobu uchování** ve dnech.</span><span class="sxs-lookup"><span data-stu-id="ad230-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="ad230-169">Například 30.</span><span class="sxs-lookup"><span data-stu-id="ad230-169">For example, 30.</span></span>
1. <span data-ttu-id="ad230-170">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="ad230-170">Click **Save**.</span></span>

<span data-ttu-id="ad230-171">Pro webovou aplikaci se generují protokoly ASP.NET Core a web server (App Service).</span><span class="sxs-lookup"><span data-stu-id="ad230-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="ad230-172">Stahování lze provádět pomocí FTP/FTPS informace zobrazené.</span><span class="sxs-lookup"><span data-stu-id="ad230-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="ad230-173">Heslo je stejná jako přihlašovací údaje pro nasazení, vytvořili dříve v tomto průvodci.</span><span class="sxs-lookup"><span data-stu-id="ad230-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="ad230-174">Protokoly můžou být [streamované přímo do vašeho místního počítače pomocí PowerShellu nebo Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="ad230-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="ad230-175">Protokoly lze také [Zobrazit v Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="ad230-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="ad230-176">Streamování protokolů</span><span class="sxs-lookup"><span data-stu-id="ad230-176">Log streaming</span></span>

<span data-ttu-id="ad230-177">Protokoly aplikací a webového serveru můžete streamování v reálném čase prostřednictvím portálu.</span><span class="sxs-lookup"><span data-stu-id="ad230-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="ad230-178">Otevřete [Azure Portal](https://portal.azure.com)a přejděte do *mywebapp\<unique_number\>* App Service.</span><span class="sxs-lookup"><span data-stu-id="ad230-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="ad230-179">V nabídce vlevo přejděte dolů k části **monitorování** a vyberte **log Stream**.</span><span class="sxs-lookup"><span data-stu-id="ad230-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Snímek obrazovky znázorňující protokolu stream odkaz](./media/monitoring/log-stream.png)

<span data-ttu-id="ad230-181">Protokoly lze také [streamovat prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), včetně Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="ad230-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="ad230-182">Výstrahy</span><span class="sxs-lookup"><span data-stu-id="ad230-182">Alerts</span></span>

<span data-ttu-id="ad230-183">Azure Monitor také poskytuje [výstrahy v reálném čase](/azure/monitoring-and-diagnostics/insights-alerts-portal) na základě metrik, událostí správy a dalších kritérií.</span><span class="sxs-lookup"><span data-stu-id="ad230-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="ad230-184">*Poznámka: upozornění na metriky webové aplikace je dostupné jenom ve službě výstrahy (Classic).*</span><span class="sxs-lookup"><span data-stu-id="ad230-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="ad230-185">[Službu Výstrahy (Classic)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) najdete v Azure monitor nebo v části **monitorování** nastavení App Service.</span><span class="sxs-lookup"><span data-stu-id="ad230-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Upozornění (klasická) odkaz](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="ad230-187">Živé ladění</span><span class="sxs-lookup"><span data-stu-id="ad230-187">Live debugging</span></span>

<span data-ttu-id="ad230-188">Azure App Service lze [ladit vzdáleně pomocí sady Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) , když protokoly neposkytují dostatek informací.</span><span class="sxs-lookup"><span data-stu-id="ad230-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="ad230-189">Ale vzdálené ladění vyžaduje, aby aplikace kompilována se symboly ladění.</span><span class="sxs-lookup"><span data-stu-id="ad230-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="ad230-190">Ladění nelze provádět v produkčním prostředí, s výjimkou jako poslední možnost.</span><span class="sxs-lookup"><span data-stu-id="ad230-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="ad230-191">Závěr</span><span class="sxs-lookup"><span data-stu-id="ad230-191">Conclusion</span></span>

<span data-ttu-id="ad230-192">V této části jste dokončili následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="ad230-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="ad230-193">Najít základní monitorování a řešení potíží s dat na webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="ad230-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="ad230-194">Zjistěte, jak Azure Monitor poskytuje hlubší pohled na metriky v rámci všech služeb Azure</span><span class="sxs-lookup"><span data-stu-id="ad230-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="ad230-195">Připojení webové aplikace pomocí Application Insights pro aplikace pro profilaci</span><span class="sxs-lookup"><span data-stu-id="ad230-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="ad230-196">Zapnutí protokolování a zjistíte, kde ke stažení protokolů</span><span class="sxs-lookup"><span data-stu-id="ad230-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="ad230-197">Stream protokolů v reálném čase</span><span class="sxs-lookup"><span data-stu-id="ad230-197">Stream logs in real time</span></span>
* <span data-ttu-id="ad230-198">Zjistíte, kde nastavit výstrahy</span><span class="sxs-lookup"><span data-stu-id="ad230-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="ad230-199">Další informace o vzdálené ladění Azure App Service web apps.</span><span class="sxs-lookup"><span data-stu-id="ad230-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="ad230-200">Další čtení</span><span class="sxs-lookup"><span data-stu-id="ad230-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="ad230-201">Monitorování výkonu webových aplikací Azure pomocí Application Insights</span><span class="sxs-lookup"><span data-stu-id="ad230-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="ad230-202">Povolit protokolování diagnostiky pro webové aplikace v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ad230-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="ad230-203">Řešení potíží s webovou aplikací v Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad230-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="ad230-204">Vytváření klasických upozornění na metriky v Azure Monitor pro služby Azure – Azure Portal</span><span class="sxs-lookup"><span data-stu-id="ad230-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
