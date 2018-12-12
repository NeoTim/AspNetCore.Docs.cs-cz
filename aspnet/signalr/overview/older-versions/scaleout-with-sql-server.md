---
uid: signalr/overview/older-versions/scaleout-with-sql-server
title: Škálování aplikace SignalR SQL serverem (SignalR 1.x) | Dokumentace Microsoftu
author: MikeWasson
description: ''
ms.author: riande
ms.date: 05/01/2013
ms.assetid: 1dca7967-8296-444a-9533-837eb284e78c
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 682aa837ed991cbf5d78dcb304e2c1bce905c52c
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287525"
---
<a name="signalr-scaleout-with-sql-server-signalr-1x"></a><span data-ttu-id="1a714-102">Škálování aplikace SignalR SQL Serverem (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="1a714-102">SignalR Scaleout with SQL Server (SignalR 1.x)</span></span>
====================
<span data-ttu-id="1a714-103">podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="1a714-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="1a714-104">V tomto kurzu použijete SQL Server k distribuci zpráv do aplikace SignalR, která je nasazená ve dvou samostatných instancí služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1a714-104">In this tutorial, you will use SQL Server to distribute messages across a SignalR application that is deployed in two separate IIS instances.</span></span> <span data-ttu-id="1a714-105">V tomto kurzu můžete spustit také na jeden testovací počítač, ale chcete-li získat plný vliv, je třeba nasadit aplikace SignalR pro dva nebo víc serverů.</span><span class="sxs-lookup"><span data-stu-id="1a714-105">You can also run this tutorial on a single test machine, but to get the full effect, you need to deploy the SignalR application to two or more servers.</span></span> <span data-ttu-id="1a714-106">SQL Server musíte nainstalovat také na některý server nebo na samostatný vyhrazený server.</span><span class="sxs-lookup"><span data-stu-id="1a714-106">You must also install SQL Server on one of the servers, or on a separate dedicated server.</span></span> <span data-ttu-id="1a714-107">Další možností je spustit kurz pomocí virtuálních počítačů v Azure.</span><span class="sxs-lookup"><span data-stu-id="1a714-107">Another option is to run the tutorial using VMs on Azure.</span></span>

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a><span data-ttu-id="1a714-108">Požadavky</span><span class="sxs-lookup"><span data-stu-id="1a714-108">Prerequisites</span></span>

<span data-ttu-id="1a714-109">Microsoft SQL Server 2005 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="1a714-109">Microsoft SQL Server 2005 or later.</span></span> <span data-ttu-id="1a714-110">Propojovacího rozhraní podporuje počítače a serverové edice systému SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1a714-110">The backplane supports both desktop and server editions of SQL Server.</span></span> <span data-ttu-id="1a714-111">Nepodporuje se SQL Server Compact Edition nebo Azure SQL Database.</span><span class="sxs-lookup"><span data-stu-id="1a714-111">It does not support SQL Server Compact Edition or Azure SQL Database.</span></span> <span data-ttu-id="1a714-112">(Pokud je vaše aplikace hostovaná v Azure, zvažte propojovací Service Bus rozhraní místo.)</span><span class="sxs-lookup"><span data-stu-id="1a714-112">(If your application is hosted on Azure, consider the Service Bus backplane instead.)</span></span>

## <a name="overview"></a><span data-ttu-id="1a714-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="1a714-113">Overview</span></span>

<span data-ttu-id="1a714-114">Předtím, než získáme podrobný kurz, zde je rychlý přehled toho, co budete dělat.</span><span class="sxs-lookup"><span data-stu-id="1a714-114">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="1a714-115">Vytvoření nové prázdné databáze.</span><span class="sxs-lookup"><span data-stu-id="1a714-115">Create a new empty database.</span></span> <span data-ttu-id="1a714-116">Propojovacího rozhraní se vytvoří nezbytné tabulky v této databázi.</span><span class="sxs-lookup"><span data-stu-id="1a714-116">The backplane will create the necessary tables in this database.</span></span>
2. <span data-ttu-id="1a714-117">Přidejte tyto balíčky NuGet pro vaši aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1a714-117">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="1a714-118">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="1a714-118">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="1a714-119">Microsoft.AspNet.SignalR.SqlServer</span><span class="sxs-lookup"><span data-stu-id="1a714-119">Microsoft.AspNet.SignalR.SqlServer</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. <span data-ttu-id="1a714-120">Vytvoření aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="1a714-120">Create a SignalR application.</span></span>
4. <span data-ttu-id="1a714-121">Přidejte následující kód do souboru Global.asax konfigurace propojovacího rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1a714-121">Add the following code to Global.asax to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

## <a name="configure-the-database"></a><span data-ttu-id="1a714-122">Konfigurace databáze</span><span class="sxs-lookup"><span data-stu-id="1a714-122">Configure the Database</span></span>

<span data-ttu-id="1a714-123">Rozhodněte, zda bude aplikace používat ověřování Windows nebo ověřování systému SQL Server pro přístup k databázi.</span><span class="sxs-lookup"><span data-stu-id="1a714-123">Decide whether the application will use Windows authentication or SQL Server authentication to access the database.</span></span> <span data-ttu-id="1a714-124">Bez ohledu na to Ujistěte se, že uživatel databáze má oprávnění k přihlášení, vytvoření schémat a vytvoření tabulky.</span><span class="sxs-lookup"><span data-stu-id="1a714-124">Regardless, make sure the database user has permissions to log in, create schemas, and create tables.</span></span>

<span data-ttu-id="1a714-125">Vytvořte novou databázi pro propojovací rozhraní systému k použití.</span><span class="sxs-lookup"><span data-stu-id="1a714-125">Create a new database for the backplane to use.</span></span> <span data-ttu-id="1a714-126">Databázi můžete přiřadit libovolný název.</span><span class="sxs-lookup"><span data-stu-id="1a714-126">You can give the database any name.</span></span> <span data-ttu-id="1a714-127">Není nutné vytvářet všechny tabulky v databázi. propojovacího rozhraní se vytvoří nezbytné tabulky.</span><span class="sxs-lookup"><span data-stu-id="1a714-127">You don't need to create any tables in the database; the backplane will create the necessary tables.</span></span>

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a><span data-ttu-id="1a714-128">Povolí službu Service Broker</span><span class="sxs-lookup"><span data-stu-id="1a714-128">Enable Service Broker</span></span>

<span data-ttu-id="1a714-129">Doporučuje se povolí službu Service Broker pro databázi propojovacího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a714-129">It is recommended to enable Service Broker for the backplane database.</span></span> <span data-ttu-id="1a714-130">Služba Service Broker poskytuje nativní podporu pro zasílání zpráv nebo řazení do fronty v systému SQL Server, které vám umožní efektivněji přijímat aktualizace propojovacího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a714-130">Service Broker provides native support for messaging and queuing in SQL Server, which lets the backplane receive updates more efficiently.</span></span> <span data-ttu-id="1a714-131">(Ale propojovacího rozhraní také funguje bez služby Service Broker.)</span><span class="sxs-lookup"><span data-stu-id="1a714-131">(However, the backplane also works without Service Broker.)</span></span>

<span data-ttu-id="1a714-132">Pokud chcete zkontrolovat, zda je povolena služba Service Broker, dotazování **je\_zprostředkovatele\_povolené** sloupec v **zobrazení sys.databases** zobrazení katalogu.</span><span class="sxs-lookup"><span data-stu-id="1a714-132">To check whether Service Broker is enabled, query the **is\_broker\_enabled** column in the **sys.databases** catalog view.</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

<span data-ttu-id="1a714-133">Povolí službu Service Broker, pomocí následujícího dotazu SQL:</span><span class="sxs-lookup"><span data-stu-id="1a714-133">To enable Service Broker, use the following SQL query:</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> <span data-ttu-id="1a714-134">Pokud tento dotaz se zdá, zablokování, ujistěte se, že nejsou žádné aplikace, připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="1a714-134">If this query appears to deadlock, make sure there are no applications connected to the DB.</span></span>

<span data-ttu-id="1a714-135">Pokud jste povolili trasování, trasování zobrazí také, zda je povolena služba Service Broker.</span><span class="sxs-lookup"><span data-stu-id="1a714-135">If you have enabled tracing, the traces will also show whether Service Broker is enabled.</span></span>

## <a name="create-a-signalr-application"></a><span data-ttu-id="1a714-136">Vytvoření aplikace SignalR</span><span class="sxs-lookup"><span data-stu-id="1a714-136">Create a SignalR Application</span></span>

<span data-ttu-id="1a714-137">Vytvoření aplikace SignalR pomocí některé z těchto kurzů:</span><span class="sxs-lookup"><span data-stu-id="1a714-137">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="1a714-138">Začínáme s knihovnou SignalR</span><span class="sxs-lookup"><span data-stu-id="1a714-138">Getting Started with SignalR</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="1a714-139">Začínáme s knihovnou SignalR a MVC 4</span><span class="sxs-lookup"><span data-stu-id="1a714-139">Getting Started with SignalR and MVC 4</span></span>](tutorial-getting-started-with-signalr-and-mvc-4.md)

<span data-ttu-id="1a714-140">V dalším kroku upravíme, aby byl chatovací aplikaci, aby podporovala škálování s využitím SQL serveru.</span><span class="sxs-lookup"><span data-stu-id="1a714-140">Next, we'll modify the chat application to support scaleout with SQL Server.</span></span> <span data-ttu-id="1a714-141">Nejprve přidejte balíček SignalR.SqlServer NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="1a714-141">First, add the SignalR.SqlServer NuGet package to your project.</span></span> <span data-ttu-id="1a714-142">V aplikaci Visual Studio z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="1a714-142">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="1a714-143">V okně konzoly Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1a714-143">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

<span data-ttu-id="1a714-144">Dále otevřete soubor Global.asax.</span><span class="sxs-lookup"><span data-stu-id="1a714-144">Next, open the Global.asax file.</span></span> <span data-ttu-id="1a714-145">Přidejte následující kód, který **aplikace\_Start** metody:</span><span class="sxs-lookup"><span data-stu-id="1a714-145">Add the following code to the **Application\_Start** method:</span></span>

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="1a714-146">Nasazení a spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1a714-146">Deploy and Run the Application</span></span>

<span data-ttu-id="1a714-147">Příprava vašich instancí Windows serveru k nasazení aplikace SignalR.</span><span class="sxs-lookup"><span data-stu-id="1a714-147">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="1a714-148">Přidejte roli služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1a714-148">Add the IIS role.</span></span> <span data-ttu-id="1a714-149">Zahrnují funkce "Vývoj aplikací", včetně protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1a714-149">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-sql-server/_static/image4.png)

<span data-ttu-id="1a714-150">Zahrnují taky službu správy (uvedené v části "Nástroje pro správu").</span><span class="sxs-lookup"><span data-stu-id="1a714-150">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-sql-server/_static/image5.png)

<span data-ttu-id="1a714-151">**Nainstalovat Web Deploy 3.0.**</span><span class="sxs-lookup"><span data-stu-id="1a714-151">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="1a714-152">Když spustíte Správce služby IIS, vás vyzve k instalaci webové platformy společnosti Microsoft, nebo se dají [stáhnout intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="1a714-152">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="1a714-153">Do platformy hledání pro nasazení webu a nainstalovat nasazení webu 3.0</span><span class="sxs-lookup"><span data-stu-id="1a714-153">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-sql-server/_static/image6.png)

<span data-ttu-id="1a714-154">Zkontrolujte, že je spuštěná služby webové správy.</span><span class="sxs-lookup"><span data-stu-id="1a714-154">Check that the Web Management Service is running.</span></span> <span data-ttu-id="1a714-155">Pokud ne, spusťte službu.</span><span class="sxs-lookup"><span data-stu-id="1a714-155">If not, start the service.</span></span> <span data-ttu-id="1a714-156">(Pokud nevidíte v seznamu služeb Windows služby webové správy, ujistěte se, že jste nainstalovali službu pro správu při přidání IIS role.)</span><span class="sxs-lookup"><span data-stu-id="1a714-156">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="1a714-157">A konečně otevřete port 8172 pro protokol TCP.</span><span class="sxs-lookup"><span data-stu-id="1a714-157">Finally, open port 8172 for TCP.</span></span> <span data-ttu-id="1a714-158">Toto je port, který používá nástroj nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="1a714-158">This is the port that the Web Deploy tool uses.</span></span>

<span data-ttu-id="1a714-159">Nyní jste připraveni k nasazení projektu sady Visual Studio z vývojového počítače k serveru.</span><span class="sxs-lookup"><span data-stu-id="1a714-159">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="1a714-160">V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a klikněte na tlačítko **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="1a714-160">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="1a714-161">Podrobnější dokumentaci k nasazení webu, najdete v článku [mapa obsahu webové nasazení pro Visual Studio a ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="1a714-161">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="1a714-162">Pokud nasadíte aplikaci do dvou serverů, můžete otevřít každou instanci v samostatném okně prohlížeče a zobrazit, že každá z jiných příjem zpráv SignalR.</span><span class="sxs-lookup"><span data-stu-id="1a714-162">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="1a714-163">(Samozřejmě v produkčním prostředí, dva servery strávil za nástrojem pro vyrovnávání zatížení.)</span><span class="sxs-lookup"><span data-stu-id="1a714-163">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-sql-server/_static/image7.png)

<span data-ttu-id="1a714-164">Po spuštění aplikace, uvidíte, že SignalR vytvořila automaticky tabulky v databázi:</span><span class="sxs-lookup"><span data-stu-id="1a714-164">After you run the application, you can see that SignalR has automatically created tables in the database:</span></span>

![](scaleout-with-sql-server/_static/image8.png)

<span data-ttu-id="1a714-165">Funkce SignalR spravuje tabulky.</span><span class="sxs-lookup"><span data-stu-id="1a714-165">SignalR manages the tables.</span></span> <span data-ttu-id="1a714-166">Tak dlouho, dokud je vaše aplikace nasazena, nemusíte odstraňovat řádky, úprava v tabulce a tak dále.</span><span class="sxs-lookup"><span data-stu-id="1a714-166">As long as your application is deployed, don't delete rows, modify the table, and so forth.</span></span>
