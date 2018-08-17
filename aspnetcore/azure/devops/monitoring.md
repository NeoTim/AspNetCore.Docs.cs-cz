---
title: DevOps s využitím ASP.NET Core a Azure | Monitorování a ladění
author: CamSoper
description: Průvodce, který poskytuje pokyny k začátku do konce na vytváření procesních toků pro DevOps pro aplikace ASP.NET Core hostované v Azure.
ms.author: casoper
ms.date: 08/07/2018
uid: azure/devops/monitor
ms.openlocfilehash: c2fc88493aee04d7ea2781d17e808581e89d2082
ms.sourcegitcommit: 29dfe436f54a27fbb4f6494bc639d16c75001fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "39722614"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="62813-103">Monitorování a ladění</span><span class="sxs-lookup"><span data-stu-id="62813-103">Monitor and debug</span></span>

<span data-ttu-id="62813-104">S aplikaci nasadili a vytvořili kanál DevOps, je důležité pochopit, jak monitorovat a řešení potíží s aplikací.</span><span class="sxs-lookup"><span data-stu-id="62813-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="62813-105">V této části budete provádět následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="62813-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="62813-106">Najít základní monitorování a řešení potíží s dat na webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="62813-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="62813-107">Zjistěte, jak Azure Monitor poskytuje hlubší pohled na metriky v rámci všech služeb Azure</span><span class="sxs-lookup"><span data-stu-id="62813-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="62813-108">Připojení webové aplikace pomocí Application Insights pro aplikace pro profilaci</span><span class="sxs-lookup"><span data-stu-id="62813-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="62813-109">Zapnutí protokolování a zjistíte, kde ke stažení protokolů</span><span class="sxs-lookup"><span data-stu-id="62813-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="62813-110">Stream protokolů v reálném čase</span><span class="sxs-lookup"><span data-stu-id="62813-110">Stream logs in real time</span></span>
* <span data-ttu-id="62813-111">Zjistíte, kde nastavit výstrahy</span><span class="sxs-lookup"><span data-stu-id="62813-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="62813-112">Další informace o vzdálené ladění Azure App Service web apps.</span><span class="sxs-lookup"><span data-stu-id="62813-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="62813-113">Základní monitorování a řešení potíží</span><span class="sxs-lookup"><span data-stu-id="62813-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="62813-114">Aplikace služby App Service web apps je snadné sledování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="62813-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="62813-115">Na webu Azure portal vykreslí metriky na snadno pochopitelné tabulky a grafy.</span><span class="sxs-lookup"><span data-stu-id="62813-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="62813-116">Otevřít [webu Azure portal](https://portal.azure.com)a potom přejděte na *mywebapp\<unique_number\>*  služby App Service.</span><span class="sxs-lookup"><span data-stu-id="62813-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="62813-117">**Přehled** karta obsahuje užitečné informace "na přehledem", včetně grafů zobrazení nedávné metriky.</span><span class="sxs-lookup"><span data-stu-id="62813-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Přehled panelu](./media/monitoring/overview.png)

    * <span data-ttu-id="62813-119">**Http 5xx**: počet chyb na straně serveru, obvykle výjimek v kódu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62813-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="62813-120">**Data v**: příchozí přenosy přicházejí do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="62813-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="62813-121">**Data odesílaná**: Data výchozí přenos dat z vaší webové aplikace pro klienty.</span><span class="sxs-lookup"><span data-stu-id="62813-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="62813-122">**Požadavky**: počet žádostí HTTP.</span><span class="sxs-lookup"><span data-stu-id="62813-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="62813-123">**Průměrná doba odezvy**: Průměrná doba pro webovou aplikaci, aby odpovídal na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="62813-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="62813-124">Několik samoobslužné nástroje pro řešení potíží a optimalizace, které také najdete na této stránce.</span><span class="sxs-lookup"><span data-stu-id="62813-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Samoobslužné nástroje](./media/monitoring/wizards.png)

    * <span data-ttu-id="62813-126">**Diagnostikovat a řešit problémy** je Poradce při potížích samoobslužné služby.</span><span class="sxs-lookup"><span data-stu-id="62813-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="62813-127">**Application Insights** je pro profilaci výkonu a chování aplikace a je popsána dále v této části.</span><span class="sxs-lookup"><span data-stu-id="62813-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="62813-128">**App Service Advisoru** doporučení pro optimalizaci prostředí pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="62813-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="62813-129">Rozšířené monitorování</span><span class="sxs-lookup"><span data-stu-id="62813-129">Advanced monitoring</span></span>

<span data-ttu-id="62813-130">[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) je centralizovaná služba pro monitorování všech metrik a nastavení výstrah napříč službami Azure.</span><span class="sxs-lookup"><span data-stu-id="62813-130">[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="62813-131">V rámci Azure Monitor správci podrobně sledovat výkon a identifikovat trendy.</span><span class="sxs-lookup"><span data-stu-id="62813-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="62813-132">Jednotlivé služby Azure nabízí svůj vlastní [nastavení metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) do Azure monitoru.</span><span class="sxs-lookup"><span data-stu-id="62813-132">Each Azure service offers its own [set of metrics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="62813-133">Profil Application insights</span><span class="sxs-lookup"><span data-stu-id="62813-133">Profile with Application Insights</span></span>

<span data-ttu-id="62813-134">[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je služba Azure pro analýzu výkonu a stability webových aplikací a jak uživatelé používají.</span><span class="sxs-lookup"><span data-stu-id="62813-134">[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="62813-135">Data ze služby Application Insights je rozsáhlejší a podrobnější než u Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="62813-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="62813-136">Data můžete zadat, vývojáři a správci s informace o klíči pro zlepšení aplikace.</span><span class="sxs-lookup"><span data-stu-id="62813-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="62813-137">Application Insights je přidat do prostředku služby Azure App Service bez jakýchkoli změn kódu.</span><span class="sxs-lookup"><span data-stu-id="62813-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="62813-138">Otevřít [webu Azure portal](https://portal.azure.com)a potom přejděte na *mywebapp\<unique_number\>*  služby App Service.</span><span class="sxs-lookup"><span data-stu-id="62813-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="62813-139">Z **přehled** klikněte na tlačítko **Application Insights** dlaždici.</span><span class="sxs-lookup"><span data-stu-id="62813-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Dlaždici služby Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="62813-141">Vyberte **vytvořit nový prostředek** přepínač.</span><span class="sxs-lookup"><span data-stu-id="62813-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="62813-142">Použijte výchozí název prostředku a vyberte umístění pro prostředek služby Application Insights.</span><span class="sxs-lookup"><span data-stu-id="62813-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="62813-143">Umístění se nemusí shodovat s vaší webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="62813-143">The location doesn't need to match that of your web app.</span></span>

    ![Nastavení Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="62813-145">Pro **modul Runtime ani architektura**vyberte **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="62813-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="62813-146">Přijměte výchozí nastavení.</span><span class="sxs-lookup"><span data-stu-id="62813-146">Accept the default settings.</span></span>
1. <span data-ttu-id="62813-147">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="62813-147">Select **OK**.</span></span> <span data-ttu-id="62813-148">Pokud se zobrazí výzva k potvrzení, vyberte **pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="62813-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="62813-149">Po vytvoření prostředku, klikněte na název prostředku Application Insights a přejít přímo na stránku Application Insights.</span><span class="sxs-lookup"><span data-stu-id="62813-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Nový prostředek Application Insights je připravený](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="62813-151">Při použití aplikace, data hromadí.</span><span class="sxs-lookup"><span data-stu-id="62813-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="62813-152">Vyberte **aktualizovat** znovu načíst okno s novými daty.</span><span class="sxs-lookup"><span data-stu-id="62813-152">Select **Refresh** to reload the blade with new data.</span></span>

![Karta Přehled Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="62813-154">Application Insights poskytuje užitečné informace na straně serveru bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="62813-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="62813-155">Chcete-li získat co nejvíce ze služby Application Insights [instrumentaci vaší aplikace pomocí Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="62813-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="62813-156">Pokud správně nakonfigurovaná, které služba poskytuje začátku do konce monitorování v rámci webového serveru a prohlížeče, včetně výkonu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="62813-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="62813-157">Další informace najdete v tématu [dokumentace k Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="62813-157">For more information, see the [Application Insights documentation](https://docs.microsoft.com/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="62813-158">protokolování</span><span class="sxs-lookup"><span data-stu-id="62813-158">Logging</span></span>

<span data-ttu-id="62813-159">Protokoly webového serveru a aplikace jsou zakázané ve výchozím nastavení ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="62813-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="62813-160">Povolení protokolů pomocí následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="62813-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="62813-161">Otevřít [webu Azure portal](https://portal.azure.com)a přejděte *mywebapp\<unique_number\>*  služby App Service.</span><span class="sxs-lookup"><span data-stu-id="62813-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="62813-162">V nabídce na levé straně, přejděte dolů k položce **monitorování** oddílu.</span><span class="sxs-lookup"><span data-stu-id="62813-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="62813-163">Vyberte **diagnostické protokoly**.</span><span class="sxs-lookup"><span data-stu-id="62813-163">Select **Diagnostics logs**.</span></span>

    ![Diagnostické protokoly odkaz](./media/monitoring/logging.png)

1. <span data-ttu-id="62813-165">Zapnout **protokolování aplikace (systém souborů)**.</span><span class="sxs-lookup"><span data-stu-id="62813-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="62813-166">Pokud se zobrazí výzva, klikněte na políčko pro instalaci rozšíření povolíte protokolování ve webové aplikaci app.</span><span class="sxs-lookup"><span data-stu-id="62813-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="62813-167">Nastavte **protokolování webového serveru** k **systému souborů**.</span><span class="sxs-lookup"><span data-stu-id="62813-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="62813-168">Zadejte **dobu uchování** ve dnech.</span><span class="sxs-lookup"><span data-stu-id="62813-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="62813-169">Například 30.</span><span class="sxs-lookup"><span data-stu-id="62813-169">For example, 30.</span></span>
1. <span data-ttu-id="62813-170">Klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="62813-170">Click **Save**.</span></span>

<span data-ttu-id="62813-171">Pro webovou aplikaci se generují protokoly ASP.NET Core a web server (App Service).</span><span class="sxs-lookup"><span data-stu-id="62813-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="62813-172">Stahování lze provádět pomocí FTP/FTPS informace zobrazené.</span><span class="sxs-lookup"><span data-stu-id="62813-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="62813-173">Heslo je stejná jako přihlašovací údaje pro nasazení, vytvořili dříve v tomto průvodci.</span><span class="sxs-lookup"><span data-stu-id="62813-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="62813-174">Může být protokoly [Streamovat přímo do svého místního počítače pomocí Powershellu nebo rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="62813-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="62813-175">Můžete se také protokoly [zobrazit ve službě Application Insights](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="62813-175">Logs can also be [viewed in Application Insights](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="62813-176">Streamování protokolů</span><span class="sxs-lookup"><span data-stu-id="62813-176">Log streaming</span></span>

<span data-ttu-id="62813-177">Protokoly aplikací a webového serveru můžete streamování v reálném čase prostřednictvím portálu.</span><span class="sxs-lookup"><span data-stu-id="62813-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="62813-178">Otevřít [webu Azure portal](https://portal.azure.com)a přejděte *mywebapp\<unique_number\>*  služby App Service.</span><span class="sxs-lookup"><span data-stu-id="62813-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="62813-179">V nabídce na levé straně, přejděte dolů k položce **monitorování** a vyberte **stream protokolů**.</span><span class="sxs-lookup"><span data-stu-id="62813-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Propojení stream protokolů](./media/monitoring/log-stream.png)

<span data-ttu-id="62813-181">Můžete se také protokoly [Streamovat prostřednictvím Azure Powershellu nebo rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), včetně Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="62813-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="62813-182">Upozornění</span><span class="sxs-lookup"><span data-stu-id="62813-182">Alerts</span></span>

<span data-ttu-id="62813-183">Azure Monitor poskytuje také [upozornění v reálném čase](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal) na základě metrik, správy událostí a jiných kritérií.</span><span class="sxs-lookup"><span data-stu-id="62813-183">Azure Monitor also provides [real time alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="62813-184">*Poznámka: Aktuálně upozornění na metriky webové aplikace je k dispozici pouze ve službě upozornění (klasická).*</span><span class="sxs-lookup"><span data-stu-id="62813-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="62813-185">[Upozornění (klasická) služby](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) najdete ve službě Azure Monitor nebo v části **monitorování** část nastavení služby App Service.</span><span class="sxs-lookup"><span data-stu-id="62813-185">The [Alerts (classic) service](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Upozornění (klasická) odkaz](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="62813-187">Živé ladění</span><span class="sxs-lookup"><span data-stu-id="62813-187">Live debugging</span></span>

<span data-ttu-id="62813-188">Azure App Service může být [vzdáleně ladit pomocí sady Visual Studio](https://docs.microsoft.com/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) když protokoly neposkytují dostatek informací.</span><span class="sxs-lookup"><span data-stu-id="62813-188">Azure App Service can be [debugged remotely with Visual Studio](https://docs.microsoft.com/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="62813-189">Ale vzdálené ladění vyžaduje, aby aplikace kompilována se symboly ladění.</span><span class="sxs-lookup"><span data-stu-id="62813-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="62813-190">Ladění nelze provádět v produkčním prostředí, s výjimkou jako poslední možnost.</span><span class="sxs-lookup"><span data-stu-id="62813-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="62813-191">Závěr</span><span class="sxs-lookup"><span data-stu-id="62813-191">Conclusion</span></span>

<span data-ttu-id="62813-192">V této části jste dokončili následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="62813-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="62813-193">Najít základní monitorování a řešení potíží s dat na webu Azure Portal</span><span class="sxs-lookup"><span data-stu-id="62813-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="62813-194">Zjistěte, jak Azure Monitor poskytuje hlubší pohled na metriky v rámci všech služeb Azure</span><span class="sxs-lookup"><span data-stu-id="62813-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="62813-195">Připojení webové aplikace pomocí Application Insights pro aplikace pro profilaci</span><span class="sxs-lookup"><span data-stu-id="62813-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="62813-196">Zapnutí protokolování a zjistíte, kde ke stažení protokolů</span><span class="sxs-lookup"><span data-stu-id="62813-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="62813-197">Stream protokolů v reálném čase</span><span class="sxs-lookup"><span data-stu-id="62813-197">Stream logs in real time</span></span>
* <span data-ttu-id="62813-198">Zjistíte, kde nastavit výstrahy</span><span class="sxs-lookup"><span data-stu-id="62813-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="62813-199">Další informace o vzdálené ladění Azure App Service web apps.</span><span class="sxs-lookup"><span data-stu-id="62813-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="62813-200">Další čtení</span><span class="sxs-lookup"><span data-stu-id="62813-200">Additional reading</span></span>

* [<span data-ttu-id="62813-201">Řešení potíží s ASP.NET Core ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="62813-201">Troubleshoot ASP.NET Core on Azure App Service</span></span>](https://docs.microsoft.com/aspnet/core/host-and-deploy/azure-apps/troubleshoot)
* [<span data-ttu-id="62813-202">Referenční informace o běžných chybách pro Azure App Service a IIS s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="62813-202">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/host-and-deploy/azure-iis-errors-reference)
* [<span data-ttu-id="62813-203">Monitorování výkonu webových aplikací Azure pomocí Application Insights</span><span class="sxs-lookup"><span data-stu-id="62813-203">Monitor Azure web app performance with Application Insights</span></span>](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="62813-204">Povolení protokolování diagnostiky pro webové aplikace ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="62813-204">Enable diagnostics logging for web apps in Azure App Service</span></span>](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="62813-205">Řešení potíží s webovou aplikací ve službě Azure App Service pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62813-205">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](https://docs.microsoft.com/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="62813-206">Vytvoření klasického upozornění metrik ve službě Azure Monitor pro Azure services – Azure portal</span><span class="sxs-lookup"><span data-stu-id="62813-206">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)