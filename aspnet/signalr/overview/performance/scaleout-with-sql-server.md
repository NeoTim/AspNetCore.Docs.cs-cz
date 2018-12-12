---
uid: signalr/overview/performance/scaleout-with-sql-server
title: Škálování aplikace SignalR SQL serverem | Dokumentace Microsoftu
author: MikeWasson
description: Verze softwaru použitým v tomto tématu Visual Studio 2013 .NET 4.5 SignalR 2 předchozí verze tohoto tématu informace o předchozích verzích...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 98358b6e-9139-4239-ba3a-2d7dd74dd664
msc.legacyurl: /signalr/overview/performance/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 73b16ecf141732f0adc362dc5167073491cc2dea
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287010"
---
<a name="signalr-scaleout-with-sql-server"></a><span data-ttu-id="caeb7-103">Šklálování aplikace SignalR SQL Serverem</span><span class="sxs-lookup"><span data-stu-id="caeb7-103">SignalR Scaleout with SQL Server</span></span>
====================
<span data-ttu-id="caeb7-104">podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="caeb7-104">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="caeb7-105">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="caeb7-105">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="caeb7-106">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="caeb7-106">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="caeb7-107">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="caeb7-107">.NET 4.5</span></span>
> - <span data-ttu-id="caeb7-108">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="caeb7-108">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="caeb7-109">Předchozích verzích tohoto tématu</span><span class="sxs-lookup"><span data-stu-id="caeb7-109">Previous versions of this topic</span></span>
>
> <span data-ttu-id="caeb7-110">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="caeb7-110">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="caeb7-111">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="caeb7-111">Questions and comments</span></span>
>
> <span data-ttu-id="caeb7-112">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="caeb7-112">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="caeb7-113">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="caeb7-113">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="caeb7-114">V tomto kurzu použijete SQL Server k distribuci zpráv do aplikace SignalR, která je nasazená ve dvou samostatných instancí služby IIS.</span><span class="sxs-lookup"><span data-stu-id="caeb7-114">In this tutorial, you will use SQL Server to distribute messages across a SignalR application that is deployed in two separate IIS instances.</span></span> <span data-ttu-id="caeb7-115">V tomto kurzu můžete spustit také na jeden testovací počítač, ale chcete-li získat plný vliv, je třeba nasadit aplikace SignalR pro dva nebo víc serverů.</span><span class="sxs-lookup"><span data-stu-id="caeb7-115">You can also run this tutorial on a single test machine, but to get the full effect, you need to deploy the SignalR application to two or more servers.</span></span> <span data-ttu-id="caeb7-116">SQL Server musíte nainstalovat také na některý server nebo na samostatný vyhrazený server.</span><span class="sxs-lookup"><span data-stu-id="caeb7-116">You must also install SQL Server on one of the servers, or on a separate dedicated server.</span></span> <span data-ttu-id="caeb7-117">Další možností je spustit kurz pomocí virtuálních počítačů v Azure.</span><span class="sxs-lookup"><span data-stu-id="caeb7-117">Another option is to run the tutorial using VMs on Azure.</span></span>

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a><span data-ttu-id="caeb7-118">Požadavky</span><span class="sxs-lookup"><span data-stu-id="caeb7-118">Prerequisites</span></span>

<span data-ttu-id="caeb7-119">Microsoft SQL Server 2005 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="caeb7-119">Microsoft SQL Server 2005 or later.</span></span> <span data-ttu-id="caeb7-120">Propojovacího rozhraní podporuje počítače a serverové edice systému SQL Server.</span><span class="sxs-lookup"><span data-stu-id="caeb7-120">The backplane supports both desktop and server editions of SQL Server.</span></span> <span data-ttu-id="caeb7-121">Nepodporuje se SQL Server Compact Edition nebo Azure SQL Database.</span><span class="sxs-lookup"><span data-stu-id="caeb7-121">It does not support SQL Server Compact Edition or Azure SQL Database.</span></span> <span data-ttu-id="caeb7-122">(Pokud je vaše aplikace hostovaná v Azure, zvažte propojovací Service Bus rozhraní místo.)</span><span class="sxs-lookup"><span data-stu-id="caeb7-122">(If your application is hosted on Azure, consider the Service Bus backplane instead.)</span></span>

## <a name="overview"></a><span data-ttu-id="caeb7-123">Přehled</span><span class="sxs-lookup"><span data-stu-id="caeb7-123">Overview</span></span>

<span data-ttu-id="caeb7-124">Předtím, než získáme podrobný kurz, zde je rychlý přehled toho, co budete dělat.</span><span class="sxs-lookup"><span data-stu-id="caeb7-124">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="caeb7-125">Vytvoření nové prázdné databáze.</span><span class="sxs-lookup"><span data-stu-id="caeb7-125">Create a new empty database.</span></span> <span data-ttu-id="caeb7-126">Propojovacího rozhraní se vytvoří nezbytné tabulky v této databázi.</span><span class="sxs-lookup"><span data-stu-id="caeb7-126">The backplane will create the necessary tables in this database.</span></span>
2. <span data-ttu-id="caeb7-127">Přidejte tyto balíčky NuGet pro vaši aplikaci:</span><span class="sxs-lookup"><span data-stu-id="caeb7-127">Add these NuGet packages to your application:</span></span>

    - [<span data-ttu-id="caeb7-128">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="caeb7-128">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="caeb7-129">Microsoft.AspNet.SignalR.SqlServer</span><span class="sxs-lookup"><span data-stu-id="caeb7-129">Microsoft.AspNet.SignalR.SqlServer</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. <span data-ttu-id="caeb7-130">Vytvoření aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="caeb7-130">Create a SignalR application.</span></span>
4. <span data-ttu-id="caeb7-131">Přidejte následující kód do souboru Startup.cs konfigurace propojovacího rozhraní:</span><span class="sxs-lookup"><span data-stu-id="caeb7-131">Add the following code to Startup.cs to configure the backplane:</span></span>

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

   <span data-ttu-id="caeb7-132">Tento kód nastaví propojovacího rozhraní s výchozími hodnotami u [TableCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.sqlscaleoutconfiguration.tablecount(v=vs.118).aspx) a [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span><span class="sxs-lookup"><span data-stu-id="caeb7-132">This code configures the backplane with the default values for [TableCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.sqlscaleoutconfiguration.tablecount(v=vs.118).aspx) and [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span></span> <span data-ttu-id="caeb7-133">Informace o změně těchto hodnot najdete v tématu [výkon aplikace SignalR: Škálování metriky](signalr-performance.md#scaleout_metrics).</span><span class="sxs-lookup"><span data-stu-id="caeb7-133">For information on changing these values, see [SignalR Performance: Scaleout Metrics](signalr-performance.md#scaleout_metrics).</span></span>

## <a name="configure-the-database"></a><span data-ttu-id="caeb7-134">Konfigurace databáze</span><span class="sxs-lookup"><span data-stu-id="caeb7-134">Configure the Database</span></span>

<span data-ttu-id="caeb7-135">Rozhodněte, zda bude aplikace používat ověřování Windows nebo ověřování systému SQL Server pro přístup k databázi.</span><span class="sxs-lookup"><span data-stu-id="caeb7-135">Decide whether the application will use Windows authentication or SQL Server authentication to access the database.</span></span> <span data-ttu-id="caeb7-136">Bez ohledu na to Ujistěte se, že uživatel databáze má oprávnění k přihlášení, vytvoření schémat a vytvoření tabulky.</span><span class="sxs-lookup"><span data-stu-id="caeb7-136">Regardless, make sure the database user has permissions to log in, create schemas, and create tables.</span></span>

<span data-ttu-id="caeb7-137">Vytvořte novou databázi pro propojovací rozhraní systému k použití.</span><span class="sxs-lookup"><span data-stu-id="caeb7-137">Create a new database for the backplane to use.</span></span> <span data-ttu-id="caeb7-138">Databázi můžete přiřadit libovolný název.</span><span class="sxs-lookup"><span data-stu-id="caeb7-138">You can give the database any name.</span></span> <span data-ttu-id="caeb7-139">Není nutné vytvářet všechny tabulky v databázi. propojovacího rozhraní se vytvoří nezbytné tabulky.</span><span class="sxs-lookup"><span data-stu-id="caeb7-139">You don't need to create any tables in the database; the backplane will create the necessary tables.</span></span>

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a><span data-ttu-id="caeb7-140">Povolí službu Service Broker</span><span class="sxs-lookup"><span data-stu-id="caeb7-140">Enable Service Broker</span></span>

<span data-ttu-id="caeb7-141">Doporučuje se povolí službu Service Broker pro databázi propojovacího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="caeb7-141">It is recommended to enable Service Broker for the backplane database.</span></span> <span data-ttu-id="caeb7-142">Služba Service Broker poskytuje nativní podporu pro zasílání zpráv nebo řazení do fronty v systému SQL Server, které vám umožní efektivněji přijímat aktualizace propojovacího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="caeb7-142">Service Broker provides native support for messaging and queuing in SQL Server, which lets the backplane receive updates more efficiently.</span></span> <span data-ttu-id="caeb7-143">(Ale propojovacího rozhraní také funguje bez služby Service Broker.)</span><span class="sxs-lookup"><span data-stu-id="caeb7-143">(However, the backplane also works without Service Broker.)</span></span>

<span data-ttu-id="caeb7-144">Pokud chcete zkontrolovat, zda je povolena služba Service Broker, dotazování **je\_zprostředkovatele\_povolené** sloupec v **zobrazení sys.databases** zobrazení katalogu.</span><span class="sxs-lookup"><span data-stu-id="caeb7-144">To check whether Service Broker is enabled, query the **is\_broker\_enabled** column in the **sys.databases** catalog view.</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

<span data-ttu-id="caeb7-145">Povolí službu Service Broker, pomocí následujícího dotazu SQL:</span><span class="sxs-lookup"><span data-stu-id="caeb7-145">To enable Service Broker, use the following SQL query:</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> <span data-ttu-id="caeb7-146">Pokud tento dotaz se zdá, zablokování, ujistěte se, že nejsou žádné aplikace, připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="caeb7-146">If this query appears to deadlock, make sure there are no applications connected to the DB.</span></span>


<span data-ttu-id="caeb7-147">Pokud jste povolili trasování, trasování zobrazí také, zda je povolena služba Service Broker.</span><span class="sxs-lookup"><span data-stu-id="caeb7-147">If you have enabled tracing, the traces will also show whether Service Broker is enabled.</span></span>

## <a name="create-a-signalr-application"></a><span data-ttu-id="caeb7-148">Vytvoření aplikace SignalR</span><span class="sxs-lookup"><span data-stu-id="caeb7-148">Create a SignalR Application</span></span>

<span data-ttu-id="caeb7-149">Vytvoření aplikace SignalR pomocí některé z těchto kurzů:</span><span class="sxs-lookup"><span data-stu-id="caeb7-149">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="caeb7-150">Začínáme s knihovnou SignalR 2.0</span><span class="sxs-lookup"><span data-stu-id="caeb7-150">Getting Started with SignalR 2.0</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="caeb7-151">Začínáme s knihovnou SignalR 2.0 a MVC 5</span><span class="sxs-lookup"><span data-stu-id="caeb7-151">Getting Started with SignalR 2.0 and MVC 5</span></span>](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)

<span data-ttu-id="caeb7-152">V dalším kroku upravíme, aby byl chatovací aplikaci, aby podporovala škálování s využitím SQL serveru.</span><span class="sxs-lookup"><span data-stu-id="caeb7-152">Next, we'll modify the chat application to support scaleout with SQL Server.</span></span> <span data-ttu-id="caeb7-153">Nejprve přidejte balíček SignalR.SqlServer NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="caeb7-153">First, add the SignalR.SqlServer NuGet package to your project.</span></span> <span data-ttu-id="caeb7-154">V aplikaci Visual Studio z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="caeb7-154">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="caeb7-155">V okně konzoly Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="caeb7-155">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

<span data-ttu-id="caeb7-156">Dále otevřete Startup.cs souboru.</span><span class="sxs-lookup"><span data-stu-id="caeb7-156">Next, open the Startup.cs file.</span></span> <span data-ttu-id="caeb7-157">Přidejte následující kód, který **konfigurovat** metody:</span><span class="sxs-lookup"><span data-stu-id="caeb7-157">Add the following code to the **Configure** method:</span></span>

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="caeb7-158">Nasazení a spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="caeb7-158">Deploy and Run the Application</span></span>

<span data-ttu-id="caeb7-159">Příprava vašich instancí Windows serveru k nasazení aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="caeb7-159">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="caeb7-160">Přidejte roli služby IIS.</span><span class="sxs-lookup"><span data-stu-id="caeb7-160">Add the IIS role.</span></span> <span data-ttu-id="caeb7-161">Zahrnují funkce "Vývoj aplikací", včetně protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="caeb7-161">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-sql-server/_static/image4.png)

<span data-ttu-id="caeb7-162">Zahrnují taky službu správy (uvedené v části "Nástroje pro správu").</span><span class="sxs-lookup"><span data-stu-id="caeb7-162">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-sql-server/_static/image5.png)

<span data-ttu-id="caeb7-163">**Nainstalovat Web Deploy 3.0.**</span><span class="sxs-lookup"><span data-stu-id="caeb7-163">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="caeb7-164">Když spustíte Správce služby IIS, vás vyzve k instalaci webové platformy společnosti Microsoft, nebo se dají [stáhnout intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="caeb7-164">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="caeb7-165">Do platformy hledání pro nasazení webu a nainstalovat nasazení webu 3.0</span><span class="sxs-lookup"><span data-stu-id="caeb7-165">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-sql-server/_static/image6.png)

<span data-ttu-id="caeb7-166">Zkontrolujte, že je spuštěná služby webové správy.</span><span class="sxs-lookup"><span data-stu-id="caeb7-166">Check that the Web Management Service is running.</span></span> <span data-ttu-id="caeb7-167">Pokud ne, spusťte službu.</span><span class="sxs-lookup"><span data-stu-id="caeb7-167">If not, start the service.</span></span> <span data-ttu-id="caeb7-168">(Pokud nevidíte v seznamu služeb Windows služby webové správy, ujistěte se, že jste nainstalovali službu pro správu při přidání IIS role.)</span><span class="sxs-lookup"><span data-stu-id="caeb7-168">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="caeb7-169">A konečně otevřete port 8172 pro protokol TCP.</span><span class="sxs-lookup"><span data-stu-id="caeb7-169">Finally, open port 8172 for TCP.</span></span> <span data-ttu-id="caeb7-170">Toto je port, který používá nástroj nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="caeb7-170">This is the port that the Web Deploy tool uses.</span></span>

<span data-ttu-id="caeb7-171">Nyní jste připraveni k nasazení projektu sady Visual Studio z vývojového počítače k serveru.</span><span class="sxs-lookup"><span data-stu-id="caeb7-171">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="caeb7-172">V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a klikněte na tlačítko **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="caeb7-172">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="caeb7-173">Podrobnější dokumentaci k nasazení webu, najdete v článku [mapa obsahu webové nasazení pro Visual Studio a ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="caeb7-173">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="caeb7-174">Pokud nasadíte aplikaci do dvou serverů, můžete otevřít každou instanci v samostatném okně prohlížeče a zobrazit, že každá z jiných příjem zpráv SignalR.</span><span class="sxs-lookup"><span data-stu-id="caeb7-174">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="caeb7-175">(Samozřejmě v produkčním prostředí, dva servery strávil za nástrojem pro vyrovnávání zatížení.)</span><span class="sxs-lookup"><span data-stu-id="caeb7-175">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-sql-server/_static/image7.png)

<span data-ttu-id="caeb7-176">Po spuštění aplikace, uvidíte, že SignalR vytvořila automaticky tabulky v databázi:</span><span class="sxs-lookup"><span data-stu-id="caeb7-176">After you run the application, you can see that SignalR has automatically created tables in the database:</span></span>

![](scaleout-with-sql-server/_static/image8.png)

<span data-ttu-id="caeb7-177">Funkce SignalR spravuje tabulky.</span><span class="sxs-lookup"><span data-stu-id="caeb7-177">SignalR manages the tables.</span></span> <span data-ttu-id="caeb7-178">Tak dlouho, dokud je vaše aplikace nasazena, nemusíte odstraňovat řádky, úprava v tabulce a tak dále.</span><span class="sxs-lookup"><span data-stu-id="caeb7-178">As long as your application is deployed, don't delete rows, modify the table, and so forth.</span></span>
