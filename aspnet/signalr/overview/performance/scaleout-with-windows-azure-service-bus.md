---
uid: signalr/overview/performance/scaleout-with-windows-azure-service-bus
title: Škálování aplikace SignalR službou Azure Service Bus | Dokumentace Microsoftu
author: MikeWasson
description: Verze softwaru použít v této verzi tématu Visual Studio 2013 .NET 4.5 SignalR 2 dřívější verze tohoto tématu pro funkci SignalR 1.x verzi tohoto tématu...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: ce1305f9-30fd-49e3-bf38-d0a78dfb06c3
msc.legacyurl: /signalr/overview/performance/scaleout-with-windows-azure-service-bus
msc.type: authoredcontent
ms.openlocfilehash: 5cdb9b5eb6d3f5ebd5c96e4b0d89926c18bddadd
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287608"
---
<a name="signalr-scaleout-with-azure-service-bus"></a><span data-ttu-id="8d080-103">Škálování aplikace SignalR službou Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="8d080-103">SignalR Scaleout with Azure Service Bus</span></span>
====================
<span data-ttu-id="8d080-104">podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="8d080-104">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="8d080-105">V tomto kurzu se naučíte se nasadit aplikace SignalR webové Role Windows Azure, pomocí propojovací Service Bus rozhraní k distribuci zpráv do jednotlivých instancí rolí.</span><span class="sxs-lookup"><span data-stu-id="8d080-105">In this tutorial, you will deploy a SignalR application to a Windows Azure Web Role, using the Service Bus backplane to distribute messages to each role instance.</span></span> <span data-ttu-id="8d080-106">(Můžete také použít propojovací Service Bus rozhraní s [webové aplikace ve službě Azure App Service](https://docs.microsoft.com/azure/app-service-web/).)</span><span class="sxs-lookup"><span data-stu-id="8d080-106">(You can also use the Service Bus backplane with [web apps in Azure App Service](https://docs.microsoft.com/azure/app-service-web/).)</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image1.png)

<span data-ttu-id="8d080-107">Požadavky:</span><span class="sxs-lookup"><span data-stu-id="8d080-107">Prerequisites:</span></span>

- <span data-ttu-id="8d080-108">Účet Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="8d080-108">A Windows Azure account.</span></span>
- <span data-ttu-id="8d080-109">[Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409).</span><span class="sxs-lookup"><span data-stu-id="8d080-109">The [Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409).</span></span>
- <span data-ttu-id="8d080-110">Visual Studio 2012 nebo 2013.</span><span class="sxs-lookup"><span data-stu-id="8d080-110">Visual Studio 2012 or 2013.</span></span>

<span data-ttu-id="8d080-111">Propojovací service bus rozhraní je také kompatibilní s [sběrnice služby pro Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx), verze 1.1.</span><span class="sxs-lookup"><span data-stu-id="8d080-111">The service bus backplane is also compatible with [Service Bus for Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx), version 1.1.</span></span> <span data-ttu-id="8d080-112">To však není kompatibilní s verzí 1.0 sběrnice služby pro Windows Server.</span><span class="sxs-lookup"><span data-stu-id="8d080-112">However, it is not compatible with version 1.0 of Service Bus for Windows Server.</span></span>

## <a name="pricing"></a><span data-ttu-id="8d080-113">Ceny</span><span class="sxs-lookup"><span data-stu-id="8d080-113">Pricing</span></span>

<span data-ttu-id="8d080-114">Propojovací Service Bus rozhraní používá témata pro odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="8d080-114">The Service Bus backplane uses topics to send messages.</span></span> <span data-ttu-id="8d080-115">Nejnovější informace cenách, naleznete v tématu [služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).</span><span class="sxs-lookup"><span data-stu-id="8d080-115">For the latest pricing information, see [Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).</span></span> <span data-ttu-id="8d080-116">V době psaní tohoto návodu můžete odeslat 1 000 000 zpráv za měsíc pro menší než 1 USD.</span><span class="sxs-lookup"><span data-stu-id="8d080-116">At the time of this writing, you can send 1,000,000 messages per month for less than $1.</span></span> <span data-ttu-id="8d080-117">Propojovacího rozhraní pošle zprávu služby Service bus pro každé volání metody rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="8d080-117">The backplane sends a service bus message for each invocation of a SignalR hub method.</span></span> <span data-ttu-id="8d080-118">Existují také některé řídicí zprávy pro připojení, odpojení, spojovacího nebo odejít ze skupiny a tak dále.</span><span class="sxs-lookup"><span data-stu-id="8d080-118">There are also some control messages for connections, disconnections, joining or leaving groups, and so forth.</span></span> <span data-ttu-id="8d080-119">Ve většině aplikací bude většinu přenos zpráv volání metod rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="8d080-119">In most applications, the majority of the message traffic will be hub method invocations.</span></span>

## <a name="overview"></a><span data-ttu-id="8d080-120">Přehled</span><span class="sxs-lookup"><span data-stu-id="8d080-120">Overview</span></span>

<span data-ttu-id="8d080-121">Předtím, než získáme podrobný kurz, zde je rychlý přehled toho, co budete dělat.</span><span class="sxs-lookup"><span data-stu-id="8d080-121">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="8d080-122">Chcete-li vytvořit nový obor názvů služby Service Bus pomocí portálu Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="8d080-122">Use the Windows Azure portal to create a new Service Bus namespace.</span></span>
2. <span data-ttu-id="8d080-123">Přidejte tyto balíčky NuGet pro vaši aplikaci:</span><span class="sxs-lookup"><span data-stu-id="8d080-123">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="8d080-124">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="8d080-124">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - <span data-ttu-id="8d080-125">[Microsoft.AspNet.SignalR.ServiceBus3](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.ServiceBus3) nebo [Microsoft.AspNet.SignalR.ServiceBus](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.ServiceBus)</span><span class="sxs-lookup"><span data-stu-id="8d080-125">[Microsoft.AspNet.SignalR.ServiceBus3](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.ServiceBus3) or [Microsoft.AspNet.SignalR.ServiceBus](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.ServiceBus)</span></span>
3. <span data-ttu-id="8d080-126">Vytvoření aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="8d080-126">Create a SignalR application.</span></span>
4. <span data-ttu-id="8d080-127">Přidejte následující kód do souboru Startup.cs konfigurace propojovacího rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8d080-127">Add the following code to Startup.cs to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample1.cs)]

<span data-ttu-id="8d080-128">Tento kód nastaví propojovacího rozhraní s výchozími hodnotami u [TopicCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.servicebusscaleoutconfiguration.topiccount(v=vs.118).aspx) a [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span><span class="sxs-lookup"><span data-stu-id="8d080-128">This code configures the backplane with the default values for [TopicCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.servicebusscaleoutconfiguration.topiccount(v=vs.118).aspx) and [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span></span> <span data-ttu-id="8d080-129">Informace o změně těchto hodnot najdete v tématu [výkon aplikace SignalR: Škálování metriky](signalr-performance.md#scaleout_metrics).</span><span class="sxs-lookup"><span data-stu-id="8d080-129">For information on changing these values, see [SignalR Performance: Scaleout Metrics](signalr-performance.md#scaleout_metrics).</span></span>

<span data-ttu-id="8d080-130">Pro každou aplikaci vyberte jinou hodnotu pro "YourAppName".</span><span class="sxs-lookup"><span data-stu-id="8d080-130">For each application, pick a different value for "YourAppName".</span></span> <span data-ttu-id="8d080-131">Nepoužívejte stejnou hodnotu napříč více aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="8d080-131">Do not use the same value across multiple applications.</span></span>

## <a name="create-the-azure-services"></a><span data-ttu-id="8d080-132">Vytvoření služby Azure</span><span class="sxs-lookup"><span data-stu-id="8d080-132">Create the Azure Services</span></span>

<span data-ttu-id="8d080-133">Vytvoření cloudové služby, jak je popsáno v [jak vytvořit a nasadit Cloudovou službu](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span><span class="sxs-lookup"><span data-stu-id="8d080-133">Create a Cloud Service, as described in [How to Create and Deploy a Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span></span> <span data-ttu-id="8d080-134">Postupujte podle kroků v části "jak: Vytvořit cloudovou službu pomocí metody rychlého vytvoření".</span><span class="sxs-lookup"><span data-stu-id="8d080-134">Follow the steps in the section "How to: Create a cloud service using Quick Create".</span></span> <span data-ttu-id="8d080-135">Pro účely tohoto kurzu není potřeba nahrát certifikát.</span><span class="sxs-lookup"><span data-stu-id="8d080-135">For this tutorial, you do not need to upload a certificate.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image2.png)

<span data-ttu-id="8d080-136">Vytvořte nový obor názvů služby Service Bus, jak je popsáno v [způsob použití služby Service Bus témata/předplatná](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions).</span><span class="sxs-lookup"><span data-stu-id="8d080-136">Create a new Service Bus namespace, as described in [How to Use Service Bus Topics/Subscriptions](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions).</span></span> <span data-ttu-id="8d080-137">Postupujte podle kroků v části "Vytvoření služby Namespace".</span><span class="sxs-lookup"><span data-stu-id="8d080-137">Follow the steps in the section "Create a Service Namespace".</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image3.png)

> [!NOTE]
> <span data-ttu-id="8d080-138">Je nutné vybrat stejné oblasti pro cloudové služby a obor názvů služby Service Bus.</span><span class="sxs-lookup"><span data-stu-id="8d080-138">Make sure to select the same region for the cloud service and the Service Bus namespace.</span></span>


## <a name="create-the-visual-studio-project"></a><span data-ttu-id="8d080-139">Vytvoření projektu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d080-139">Create the Visual Studio Project</span></span>

<span data-ttu-id="8d080-140">Spusťte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8d080-140">Start Visual Studio.</span></span> <span data-ttu-id="8d080-141">Z **souboru** nabídky, klikněte na tlačítko **nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="8d080-141">From the **File** menu, click **New Project**.</span></span>

<span data-ttu-id="8d080-142">V **nový projekt** dialogového okna rozbalte **Visual C#**.</span><span class="sxs-lookup"><span data-stu-id="8d080-142">In the **New Project** dialog box, expand **Visual C#**.</span></span> <span data-ttu-id="8d080-143">V části **nainstalované šablony**vyberte **cloudu** a pak vyberte **Windows Azure Cloud Service**.</span><span class="sxs-lookup"><span data-stu-id="8d080-143">Under **Installed Templates**, select **Cloud** and then select **Windows Azure Cloud Service**.</span></span> <span data-ttu-id="8d080-144">Ponechte výchozí rozhraní .NET Framework 4.5.</span><span class="sxs-lookup"><span data-stu-id="8d080-144">Keep the default .NET Framework 4.5.</span></span> <span data-ttu-id="8d080-145">Pojmenujte aplikaci ChatService a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="8d080-145">Name the application ChatService and click **OK**.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image4.png)

<span data-ttu-id="8d080-146">V **nová Cloudová služba Windows Azure** dialogového okna, vyberte webovou roli ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="8d080-146">In the **New Windows Azure Cloud Service** dialog, select ASP.NET Web Role.</span></span> <span data-ttu-id="8d080-147">Klikněte na tlačítko se šipkou vpravo (**&gt;**) Chcete-li přidat roli do řešení.</span><span class="sxs-lookup"><span data-stu-id="8d080-147">Click the right-arrow button (**&gt;**) to add the role to your solution.</span></span>

<span data-ttu-id="8d080-148">Najeďte myší na novou roli proto na ikonu tužky viditelné.</span><span class="sxs-lookup"><span data-stu-id="8d080-148">Hover the mouse over the new role, so the pencil icon visible.</span></span> <span data-ttu-id="8d080-149">Kliknutím na tuto ikonu přejmenujte roli.</span><span class="sxs-lookup"><span data-stu-id="8d080-149">Click this icon to rename the role.</span></span> <span data-ttu-id="8d080-150">Název role "SignalRChat" a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="8d080-150">Name the role "SignalRChat" and click **OK**.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image5.png)

<span data-ttu-id="8d080-151">V **nový projekt ASP.NET** dialogového okna, vyberte **MVC**a klikněte na tlačítko OK.</span><span class="sxs-lookup"><span data-stu-id="8d080-151">In the **New ASP.NET Project** dialog, select **MVC**, and click OK.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image6.png)

<span data-ttu-id="8d080-152">Průvodce projektem vytvoří dva projekty:</span><span class="sxs-lookup"><span data-stu-id="8d080-152">The project wizard creates two projects:</span></span>

- <span data-ttu-id="8d080-153">ChatService: Tento projekt je aplikace Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="8d080-153">ChatService: This project is the Windows Azure application.</span></span> <span data-ttu-id="8d080-154">Definuje role Azure a další možnosti konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8d080-154">It defines the Azure roles and other configuration options.</span></span>
- <span data-ttu-id="8d080-155">SignalRChat: Tento projekt není projekt ASP.NET MVC 5.</span><span class="sxs-lookup"><span data-stu-id="8d080-155">SignalRChat: This project is your ASP.NET MVC 5 project.</span></span>

## <a name="create-the-signalr-chat-application"></a><span data-ttu-id="8d080-156">Vytvoření chatovací SignalR aplikace</span><span class="sxs-lookup"><span data-stu-id="8d080-156">Create the SignalR Chat Application</span></span>

<span data-ttu-id="8d080-157">Vytvoření chatovací aplikace, postupujte podle kroků v tomto kurzu [Začínáme s knihovnou SignalR a MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span><span class="sxs-lookup"><span data-stu-id="8d080-157">To create the chat application, follow the steps in the tutorial [Getting Started with SignalR and MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

<span data-ttu-id="8d080-158">Instalace potřebných knihoven pomocí NuGet.</span><span class="sxs-lookup"><span data-stu-id="8d080-158">Use NuGet to install the required libraries.</span></span> <span data-ttu-id="8d080-159">Z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="8d080-159">From the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="8d080-160">V **Konzola správce balíčků** okno, zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8d080-160">In the **Package Manager Console** window, enter the following commands:</span></span>

[!code-powershell[Main](scaleout-with-windows-azure-service-bus/samples/sample2.ps1)]

<span data-ttu-id="8d080-161">Použití `-ProjectName` možnost nainstalovat balíčky do projektu ASP.NET MVC, nikoli projekt Windows Azure.</span><span class="sxs-lookup"><span data-stu-id="8d080-161">Use the `-ProjectName` option to install the packages to the ASP.NET MVC project, rather than the Windows Azure project.</span></span>

## <a name="configure-the-backplane"></a><span data-ttu-id="8d080-162">Konfigurace propojovacího rozhraní</span><span class="sxs-lookup"><span data-stu-id="8d080-162">Configure the Backplane</span></span>

<span data-ttu-id="8d080-163">V souboru Startup.cs vaší aplikace přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="8d080-163">In your application's Startup.cs file, add the following code:</span></span>

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample3.cs)]

<span data-ttu-id="8d080-164">Teď budete muset získat připojovací řetězec service bus.</span><span class="sxs-lookup"><span data-stu-id="8d080-164">Now you need to get your service bus connection string.</span></span> <span data-ttu-id="8d080-165">Na webu Azure Portal vyberte obor názvů služby Service bus, který jste vytvořili a klikněte na ikonu přístupový klíč.</span><span class="sxs-lookup"><span data-stu-id="8d080-165">In the Azure portal, select the service bus namespace that you created and click the Access Key icon.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image7.png)

<span data-ttu-id="8d080-166">Zkopírujte připojovací řetězec do schránky a vložte ho do *connectionString* proměnné.</span><span class="sxs-lookup"><span data-stu-id="8d080-166">Copy the connection string to the clipboard, then paste it into the *connectionString* variable.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image8.png)

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample4.cs)]

## <a name="deploy-to-azure"></a><span data-ttu-id="8d080-167">Nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="8d080-167">Deploy to Azure</span></span>

<span data-ttu-id="8d080-168">V Průzkumníku řešení rozbalte **role** složky v projektu ChatService.</span><span class="sxs-lookup"><span data-stu-id="8d080-168">In Solution Explorer, expand the **Roles** folder inside the ChatService project.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image9.png)

<span data-ttu-id="8d080-169">Klikněte pravým tlačítkem na roli SignalRChat a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="8d080-169">Right-click the SignalRChat role and select **Properties**.</span></span> <span data-ttu-id="8d080-170">Vyberte **konfigurace** kartu. V části **instance** vyberte 2.</span><span class="sxs-lookup"><span data-stu-id="8d080-170">Select the **Configuration** tab. Under **Instances** select 2.</span></span> <span data-ttu-id="8d080-171">Můžete také nastavit velikost virtuálního počítače **nejmenším instancím**.</span><span class="sxs-lookup"><span data-stu-id="8d080-171">You can also set the VM size to **Extra Small**.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image10.png)

<span data-ttu-id="8d080-172">Uložte změny.</span><span class="sxs-lookup"><span data-stu-id="8d080-172">Save the changes.</span></span>

<span data-ttu-id="8d080-173">V Průzkumníku řešení klikněte pravým tlačítkem na projekt ChatService.</span><span class="sxs-lookup"><span data-stu-id="8d080-173">In Solution Explorer, right-click the ChatService project.</span></span> <span data-ttu-id="8d080-174">Vyberte **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="8d080-174">Select **Publish**.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image11.png)

<span data-ttu-id="8d080-175">Pokud je toto vaše první čas publikování na platformě Windows Azure, je nutné stáhnout svoje přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="8d080-175">If this is your first time publishing to Windows Azure, you must download your credentials.</span></span> <span data-ttu-id="8d080-176">V **publikovat** průvodce, klikněte na tlačítko "Přihlásit a stáhnout přihlašovací údaje".</span><span class="sxs-lookup"><span data-stu-id="8d080-176">In the **Publish** wizard, click "Sign in to download credentials".</span></span> <span data-ttu-id="8d080-177">To vás vyzve k přihlášení na webu Windows Azure portal a stáhněte si soubor nastavení publikování.</span><span class="sxs-lookup"><span data-stu-id="8d080-177">This will prompt you to sign into the Windows Azure portal and download a publish settings file.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image12.png)

<span data-ttu-id="8d080-178">Klikněte na tlačítko **Import** a vyberte soubor nastavení publikování, který jste stáhli.</span><span class="sxs-lookup"><span data-stu-id="8d080-178">Click **Import** and select the publish settings file that you downloaded.</span></span>

<span data-ttu-id="8d080-179">Klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="8d080-179">Click **Next**.</span></span> <span data-ttu-id="8d080-180">V **nastavení publikování** dialogového okna, v části **Cloudovou službu**, vyberte cloudovou službu, kterou jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="8d080-180">In the **Publish Settings** dialog, under **Cloud Service**, select the cloud service that you created earlier.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image13.png)

<span data-ttu-id="8d080-181">Klikněte na tlačítko **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="8d080-181">Click **Publish**.</span></span> <span data-ttu-id="8d080-182">Může trvat pár minut a nasaďte aplikaci a spustit virtuální počítače.</span><span class="sxs-lookup"><span data-stu-id="8d080-182">It can take a few minutes to deploy the application and start the VMs.</span></span>

<span data-ttu-id="8d080-183">Nyní při spuštění aplikace chat instance rolí komunikovat přes Azure Service Bus pomocí tématu služby Service Bus.</span><span class="sxs-lookup"><span data-stu-id="8d080-183">Now when you run the chat application, the role instances communicate through Azure Service Bus, using a Service Bus topic.</span></span> <span data-ttu-id="8d080-184">Téma je fronty zpráv, který umožňuje několik předplatitelů.</span><span class="sxs-lookup"><span data-stu-id="8d080-184">A topic is a message queue that allows multiple subscribers.</span></span>

<span data-ttu-id="8d080-185">Propojovacího rozhraní automaticky vytvoří téma a předplatná.</span><span class="sxs-lookup"><span data-stu-id="8d080-185">The backplane automatically creates the topic and the subscriptions.</span></span> <span data-ttu-id="8d080-186">Pokud chcete zobrazit předplatná a zprávu aktivity, otevřete na webu Azure portal, vyberte obor názvů služby Service Bus a klikněte na "Tématech".</span><span class="sxs-lookup"><span data-stu-id="8d080-186">To see the subscriptions and message activity, open the Azure portal, select the Service Bus namespace, and click on "Topics".</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image14.png)

<span data-ttu-id="8d080-187">Ujistěte se, trvat několik minut, než se aktivita zpráva se zobrazí na řídicím panelu.</span><span class="sxs-lookup"><span data-stu-id="8d080-187">It make take a few minutes for the message activity to show up in the dashboard.</span></span>

![](scaleout-with-windows-azure-service-bus/_static/image15.png)

<span data-ttu-id="8d080-188">Funkce SignalR spravuje životnost tématu.</span><span class="sxs-lookup"><span data-stu-id="8d080-188">SignalR manages the topic lifetime.</span></span> <span data-ttu-id="8d080-189">Tak dlouho, dokud je vaše aplikace nasazena, nepokoušejte ručně odstraňte témata nebo změnit nastavení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="8d080-189">As long as your application is deployed, don't try to manually delete topics or change settings on the topic.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8d080-190">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="8d080-190">Troubleshooting</span></span>

<span data-ttu-id="8d080-191">**System.InvalidOperationException "Pouze podporované IsolationLevel je"IsolationLevel.Serializable"."**</span><span class="sxs-lookup"><span data-stu-id="8d080-191">**System.InvalidOperationException "The only supported IsolationLevel is 'IsolationLevel.Serializable'."**</span></span>

<span data-ttu-id="8d080-192">Této chybě může dojít, pokud je úroveň transakce pro operace nastavená na něco jiného než `Serializable`.</span><span class="sxs-lookup"><span data-stu-id="8d080-192">This error can occur if the transaction level for an operation is set to something other than `Serializable`.</span></span> <span data-ttu-id="8d080-193">Ověřte, že jsou prováděny žádné operace s další úrovní transakcí.</span><span class="sxs-lookup"><span data-stu-id="8d080-193">Verify that no operations are being performed with other transaction levels.</span></span>
