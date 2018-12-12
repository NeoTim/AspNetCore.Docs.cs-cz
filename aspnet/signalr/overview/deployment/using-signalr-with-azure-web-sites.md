---
uid: signalr/overview/deployment/using-signalr-with-azure-web-sites
title: Použití aplikace SignalR s webovými aplikacemi ve službě Azure App Service | Dokumentace Microsoftu
author: pfletcher
description: Tento dokument popisuje, jak nakonfigurovat aplikaci s knihovnou SignalR, která běží na Microsoft Azure. V tomto kurzu použili verze softwaru, Visual Studio 2013 nebo Vis....
ms.author: riande
ms.date: 07/01/2015
ms.assetid: 2a7517a0-b88c-4162-ade3-9bf6ca7062fd
msc.legacyurl: /signalr/overview/deployment/using-signalr-with-azure-web-sites
msc.type: authoredcontent
ms.openlocfilehash: da69e1aba1b56d69ad8e710cddd2b492168f1255
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287752"
---
<a name="using-signalr-with-web-apps-in-azure-app-service"></a><span data-ttu-id="5e03b-104">Použití aplikace SignalR s webovými aplikacemi ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5e03b-104">Using SignalR with Web Apps in Azure App Service</span></span>
====================
<span data-ttu-id="5e03b-105">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="5e03b-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="5e03b-106">Tento dokument popisuje, jak nakonfigurovat aplikaci s knihovnou SignalR, která běží na Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="5e03b-106">This document describes how to configure a SignalR application that runs on Microsoft Azure.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="5e03b-107">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="5e03b-107">Software versions used in the tutorial</span></span>
>
>
> - <span data-ttu-id="5e03b-108">[Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013) nebo Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="5e03b-108">[Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013) or Visual Studio 2012</span></span>
> - <span data-ttu-id="5e03b-109">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="5e03b-109">.NET 4.5</span></span>
> - <span data-ttu-id="5e03b-110">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="5e03b-110">SignalR version 2</span></span>
> - <span data-ttu-id="5e03b-111">Azure SDK 2.3 pro Visual Studio 2013 nebo 2012</span><span class="sxs-lookup"><span data-stu-id="5e03b-111">Azure SDK 2.3 for Visual Studio 2013 or 2012</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="5e03b-112">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="5e03b-112">Questions and comments</span></span>
>
> <span data-ttu-id="5e03b-113">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="5e03b-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="5e03b-114">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR), [StackOverflow.com](http://stackoverflow.com/), nebo [fóra Microsoft Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).</span><span class="sxs-lookup"><span data-stu-id="5e03b-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR), [StackOverflow.com](http://stackoverflow.com/), or the [Microsoft Azure forums](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).</span></span>


## <a name="table-of-contents"></a><span data-ttu-id="5e03b-115">Obsah</span><span class="sxs-lookup"><span data-stu-id="5e03b-115">Table of Contents</span></span>

- [<span data-ttu-id="5e03b-116">Úvod</span><span class="sxs-lookup"><span data-stu-id="5e03b-116">Introduction</span></span>](#introduction)
- [<span data-ttu-id="5e03b-117">Nasazení funkce SignalR webové aplikace do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5e03b-117">Deploying a SignalR Web App to Azure App Service</span></span>](#deploying)
- [<span data-ttu-id="5e03b-118">Povolení WebSockets ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5e03b-118">Enabling WebSockets on Azure App Service</span></span>](#websocket)
- [<span data-ttu-id="5e03b-119">Pomocí propojovací rozhraní Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5e03b-119">Using the Azure Redis Cache Backplane</span></span>](#backplane)
- [<span data-ttu-id="5e03b-120">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5e03b-120">Next Steps</span></span>](#nextsteps)

<a id="introduction"></a>
## <a name="introduction"></a><span data-ttu-id="5e03b-121">Úvod</span><span class="sxs-lookup"><span data-stu-id="5e03b-121">Introduction</span></span>

<span data-ttu-id="5e03b-122">Funkce SignalR technologie ASP.NET je možné uvést novou úroveň interakce mezi serverem a web nebo klienty .NET.</span><span class="sxs-lookup"><span data-stu-id="5e03b-122">ASP.NET SignalR can be used to bring a new level of interactivity between servers and web or .NET clients.</span></span> <span data-ttu-id="5e03b-123">Když jsou hostované v Azure, SignalR můžou aplikace využít vysoce dostupných, škálovatelných a výkonných prostředí, který běží v cloudu poskytuje.</span><span class="sxs-lookup"><span data-stu-id="5e03b-123">When hosted in Azure, SignalR applications can take advantage of the highly available, scalable, and performant environment that running in the cloud provides.</span></span>

<a id="deploying"></a>
## <a name="deploying-a-signalr-web-app-to-azure-app-service"></a><span data-ttu-id="5e03b-124">Nasazení funkce SignalR webové aplikace do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5e03b-124">Deploying a SignalR Web App to Azure App Service</span></span>

<span data-ttu-id="5e03b-125">Funkce SignalR nepřidává žádné konkrétní komplikací pro nasazení aplikace do Azure a nasazením na místním serveru.</span><span class="sxs-lookup"><span data-stu-id="5e03b-125">SignalR doesn't add any particular complications to deploying an application to Azure versus deploying to an on-premises server.</span></span> <span data-ttu-id="5e03b-126">Aplikace, která používá SignalR je možné hostovat v Azure bez jakýchkoli změn v konfiguraci nebo jiná nastavení (i když protokoly Websocket podporu najdete na webu [WebSockets umožňuje ve službě Azure App Service](#websocket) níže.) Pro účely tohoto kurzu budete nasazovat aplikace vytvořená v [kurz Začínáme](../getting-started/tutorial-getting-started-with-signalr.md) do Azure.</span><span class="sxs-lookup"><span data-stu-id="5e03b-126">An application that uses SignalR can be hosted in Azure without any changes in configuration or other settings (though for WebSockets support, see [Enabling WebSockets on Azure App Service](#websocket) below.) For this tutorial, you'll deploy the application created in the [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md) to Azure.</span></span>

<span data-ttu-id="5e03b-127">**Požadavky**</span><span class="sxs-lookup"><span data-stu-id="5e03b-127">**Prerequisites**</span></span>

- <span data-ttu-id="5e03b-128">Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="5e03b-128">Visual Studio 2013.</span></span> <span data-ttu-id="5e03b-129">Pokud nemáte Visual Studio, Visual Studio 2013 Express pro Web je součástí instalace sady Azure SDK.</span><span class="sxs-lookup"><span data-stu-id="5e03b-129">If you don't have Visual Studio, Visual Studio 2013 Express for Web is included in the install of the Azure SDK.</span></span>
- <span data-ttu-id="5e03b-130">[Azure SDK 2.3 pro Visual Studio 2013](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) nebo [Azure SDK 2.3 pro sadu Visual Studio 2012](https://go.microsoft.com/fwlink/p/?linkid=323511).</span><span class="sxs-lookup"><span data-stu-id="5e03b-130">[Azure SDK 2.3 for Visual Studio 2013](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) or [Azure SDK 2.3 for Visual Studio 2012](https://go.microsoft.com/fwlink/p/?linkid=323511).</span></span>
- <span data-ttu-id="5e03b-131">K dokončení tohoto kurzu, budete potřebovat předplatné Azure.</span><span class="sxs-lookup"><span data-stu-id="5e03b-131">To complete this tutorial, you will need an Azure subscription.</span></span> <span data-ttu-id="5e03b-132">Je možné [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), nebo [si zaregistrovat zkušební předplatné](https://azure.microsoft.com/pricing/free-trial/).</span><span class="sxs-lookup"><span data-stu-id="5e03b-132">You can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), or [sign up for a trial subscription](https://azure.microsoft.com/pricing/free-trial/).</span></span>

### <a name="deploying-a-signalr-web-app-to-azure"></a><span data-ttu-id="5e03b-133">Nasazení funkce SignalR webové aplikace do Azure</span><span class="sxs-lookup"><span data-stu-id="5e03b-133">Deploying a SignalR web app to Azure</span></span>

1. <span data-ttu-id="5e03b-134">Dokončení [kurz Začínáme](../getting-started/tutorial-getting-started-with-signalr.md), nebo stáhnout dokončený projekt z [Galerie kódu na](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span><span class="sxs-lookup"><span data-stu-id="5e03b-134">Complete the [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md), or download the finished project from [Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span></span>
2. <span data-ttu-id="5e03b-135">V sadě Visual Studio, vyberte **sestavení**, **publikovat SignalR Chat**.</span><span class="sxs-lookup"><span data-stu-id="5e03b-135">In Visual Studio, select **Build**, **Publish SignalR Chat**.</span></span>
3. <span data-ttu-id="5e03b-136">V dialogovém okně "Publikovat Web" Vyberte "Windows Azure Web Sites".</span><span class="sxs-lookup"><span data-stu-id="5e03b-136">In the "Publish Web" dialog, select "Windows Azure Web Sites".</span></span>

    ![Vyberte model weby Azure](using-signalr-with-azure-web-sites/_static/image1.png)
4. <span data-ttu-id="5e03b-138">Pokud nejste přihlášení k účtu Microsoft, klikněte na tlačítko **přihlásit...**  v dialogovém okně "Vyberte existující web" a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="5e03b-138">If you aren't signed in to your Microsoft account, click **Sign In...** in the "Select Existing Web Site" dialog, and sign in.</span></span>

    ![Vyberte existující web](using-signalr-with-azure-web-sites/_static/image2.png)    ![Přihlášení k Azure](using-signalr-with-azure-web-sites/_static/image3.png)
5. <span data-ttu-id="5e03b-141">V dialogovém okně "Vyberte existující webovou stránku" klikněte na tlačítko **nový**.</span><span class="sxs-lookup"><span data-stu-id="5e03b-141">In the "Select Existing Web Site" dialog, click **New**.</span></span>

    ![Nový web](using-signalr-with-azure-web-sites/_static/image4.png)
6. <span data-ttu-id="5e03b-143">V dialogovém okně "Při vytváření webu v Windows Azure" Zadejte jedinečný název aplikace.</span><span class="sxs-lookup"><span data-stu-id="5e03b-143">In the "Create site on Windows Azure" dialog, enter a unique app name.</span></span> <span data-ttu-id="5e03b-144">Vyberte oblast co nejblíže k vám v rozevírací nabídce oblasti.</span><span class="sxs-lookup"><span data-stu-id="5e03b-144">Select the region closest to you in the Region dropdown.</span></span> <span data-ttu-id="5e03b-145">Klikněte na možnost **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="5e03b-145">Click **Create**.</span></span>

    ![Vytvoření webu v Azure](using-signalr-with-azure-web-sites/_static/image5.png)
7. <span data-ttu-id="5e03b-147">V dialogovém okně "Publikovat Web" klikněte na tlačítko **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="5e03b-147">In the "Publish Web" dialog, click **Publish**.</span></span>

    ![publikování webu](using-signalr-with-azure-web-sites/_static/image6.png)
8. <span data-ttu-id="5e03b-149">Po dokončení publikování aplikace SignalR chatovací aplikaci hostované v Azure App Service Web Apps se otevře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5e03b-149">When the app has completed publishing, the SignalR Chat application hosted in Azure App Service Web Apps will open in a browser.</span></span>

    ![Web se otevře v prohlížeči](using-signalr-with-azure-web-sites/_static/image7.png)

<a id="websocket"></a>
### <a name="enabling-websockets-on-azure-app-service-web-apps"></a><span data-ttu-id="5e03b-151">Povolení WebSockets ve službě Azure App Service Web Apps</span><span class="sxs-lookup"><span data-stu-id="5e03b-151">Enabling WebSockets on Azure App Service Web Apps</span></span>

<span data-ttu-id="5e03b-152">Objekty Websocket je potřeba explicitně povolit ve webové aplikaci pro použití v aplikace SignalR. v opačném případě se použije jiné protokoly (viz [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports) podrobnosti).</span><span class="sxs-lookup"><span data-stu-id="5e03b-152">WebSockets needs to be explicitly enabled in your web app to be used in a SignalR application; otherwise, other protocols will be used (See [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports) for details).</span></span>

<span data-ttu-id="5e03b-153">Chcete-li použít objekty Websocket na Azure App Service Web Apps, povolte v části Konfigurace webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="5e03b-153">In order to use WebSockets on Azure App Service Web Apps, enable it in the configuration section of the web app.</span></span> <span data-ttu-id="5e03b-154">Chcete-li to provést, otevřete vaší webové aplikace v [portálu pro správu Azure](https://manage.windowsazure.com/)a vyberte konfigurovat.</span><span class="sxs-lookup"><span data-stu-id="5e03b-154">To do this, open your web app in the [Azure Management Portal](https://manage.windowsazure.com/), and select Configure.</span></span>

![Karta Konfigurace](using-signalr-with-azure-web-sites/_static/image8.png)

<span data-ttu-id="5e03b-156">V horní části stránky konfigurace zajistěte, aby používal rozhraní .NET 4.5 pro vaši webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5e03b-156">At the top of the configuration page, ensure that .NET 4.5 is used for your web app.</span></span>

![Nastavení rozhraní .NET framework verze 4.5](using-signalr-with-azure-web-sites/_static/image9.png)

<span data-ttu-id="5e03b-158">Na stránce konfigurace v **objekty Websocket** vyberte **na**.</span><span class="sxs-lookup"><span data-stu-id="5e03b-158">On the configuration page, in the **WebSockets** setting, select **On**.</span></span>

![Protokoly Websocket nastavení: On](using-signalr-with-azure-web-sites/_static/image10.png)

<span data-ttu-id="5e03b-160">V dolní části stránky konfigurace, vyberte **Uložit** uložte provedené změny.</span><span class="sxs-lookup"><span data-stu-id="5e03b-160">At the bottom of the Configuration page, select **Save** to save your changes.</span></span>

![Uložit nastavení](using-signalr-with-azure-web-sites/_static/image11.png)

<a id="backplane"></a>
## <a name="using-the-azure-redis-cache-backplane"></a><span data-ttu-id="5e03b-162">Pomocí propojovací rozhraní Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5e03b-162">Using the Azure Redis Cache Backplane</span></span>

<span data-ttu-id="5e03b-163">Pokud používáte více instancí pro vaši webovou aplikaci a uživatelé tyto instance musí komunikovat mezi sebou (tak, aby zprávy chatu vytvořené v jedné instanci může například dosáhnout uživatelé připojení k jiné instance), [Azure Redis Cache Propojovací rozhraní systému](../performance/scaleout-with-redis.md) musí být implementován ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5e03b-163">If you use multiple instances for your web app, and the users of those instances need to interact with one another (so that, for instance, chat messages created in one instance can reach the users connected to other instances), the [Azure Redis Cache backplane](../performance/scaleout-with-redis.md) must be implemented in your application.</span></span>

<a id="nextsteps"></a>
## <a name="next-steps"></a><span data-ttu-id="5e03b-164">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5e03b-164">Next Steps</span></span>

<span data-ttu-id="5e03b-165">Další informace o Web Apps ve službě Azure App Service najdete v tématu [přehled Web Apps](https://azure.microsoft.com/documentation/articles/app-service-web-overview/).</span><span class="sxs-lookup"><span data-stu-id="5e03b-165">For more information on Web Apps in Azure App Service, see [Web Apps overview](https://azure.microsoft.com/documentation/articles/app-service-web-overview/).</span></span>
