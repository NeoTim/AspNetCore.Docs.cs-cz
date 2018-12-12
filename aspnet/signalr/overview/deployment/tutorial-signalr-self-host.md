---
uid: signalr/overview/deployment/tutorial-signalr-self-host
title: 'Kurz: SignalR v místním | Dokumentace Microsoftu'
author: pfletcher
description: Tento kurz ukazuje, jak vytvořit v místním prostředí serveru SignalR 2 a jak se připojit k němu pomocí klienta jazyka JavaScript. V tomto kurzu V použili verze softwaru...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 400db427-27af-4f2f-abf0-5486d5e024b5
msc.legacyurl: /signalr/overview/deployment/tutorial-signalr-self-host
msc.type: authoredcontent
ms.openlocfilehash: 48cb3d4d71c33ac3382b2b35b5a19fa1c4958874
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287400"
---
<a name="tutorial-signalr-self-host"></a><span data-ttu-id="26d04-104">Kurz: Hostování na vlastním serveru funkce SignalR</span><span class="sxs-lookup"><span data-stu-id="26d04-104">Tutorial: SignalR Self-Host</span></span>
====================
<span data-ttu-id="26d04-105">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="26d04-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="26d04-106">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="26d04-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-Self-Host-Sample-6da0f383)

> <span data-ttu-id="26d04-107">Tento kurz ukazuje, jak vytvořit v místním prostředí serveru SignalR 2 a jak se připojit k němu pomocí klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="26d04-107">This tutorial shows how to create a self-hosted SignalR 2 server, and how to connect to it with a JavaScript client.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="26d04-108">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="26d04-108">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="26d04-109">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="26d04-109">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="26d04-110">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="26d04-110">.NET 4.5</span></span>
> - <span data-ttu-id="26d04-111">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="26d04-111">SignalR version 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="26d04-112">V tomto kurzu pomocí sady Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="26d04-112">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="26d04-113">Pokud chcete použít Visual Studio 2012 s tímto kurzem, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="26d04-113">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="26d04-114">Aktualizace vašeho [Správce balíčků](http://docs.nuget.org/docs/start-here/installing-nuget) na nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="26d04-114">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="26d04-115">Nainstalujte [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="26d04-115">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="26d04-116">Instalace webové platformy, vyhledejte a nainstalujte **technologie ASP.NET a webové nástroje 2013.1 pro Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="26d04-116">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="26d04-117">Tím se nainstaluje šablony sady Visual Studio pro funkci SignalR třídy jako **centra**.</span><span class="sxs-lookup"><span data-stu-id="26d04-117">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="26d04-118">Některé šablony (jako například **třídy pro spuštění OWIN**) nebudou k dispozici; pro ty, použijte místo toho soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="26d04-118">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="26d04-119">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="26d04-119">Questions and comments</span></span>
>
> <span data-ttu-id="26d04-120">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="26d04-120">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="26d04-121">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="26d04-121">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="26d04-122">Přehled</span><span class="sxs-lookup"><span data-stu-id="26d04-122">Overview</span></span>

<span data-ttu-id="26d04-123">SignalR server je obvykle hostované v aplikaci ASP.NET ve službě IIS, ale může být v místním prostředí (například konzolové aplikace nebo služby Windows) pomocí knihovny hostování na vlastním serveru.</span><span class="sxs-lookup"><span data-stu-id="26d04-123">A SignalR server is usually hosted in an ASP.NET application in IIS, but it can also be self-hosted (such as in a console application or Windows service) using the self-host library.</span></span> <span data-ttu-id="26d04-124">Tato knihovna, jako jsou všechny funkce SignalR 2, je založená na OWIN ([Open Web Interface pro .NET](http://owin.org)).</span><span class="sxs-lookup"><span data-stu-id="26d04-124">This library, like all of SignalR 2, is built on OWIN ([Open Web Interface for .NET](http://owin.org)).</span></span> <span data-ttu-id="26d04-125">OWIN definuje abstrakce mezi .NET webové servery a webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="26d04-125">OWIN defines an abstraction between .NET web servers and web applications.</span></span> <span data-ttu-id="26d04-126">OWIN odpojí webové aplikace ze serveru, který je ideální pro webové aplikace ve vašem vlastním procesu mimo službu IIS s vlastním hostováním OWIN.</span><span class="sxs-lookup"><span data-stu-id="26d04-126">OWIN decouples the web application from the server, which makes OWIN ideal for self-hosting a web application in your own process, outside of IIS.</span></span>

<span data-ttu-id="26d04-127">Mezi důvody pro hostování není ve službě IIS patří:</span><span class="sxs-lookup"><span data-stu-id="26d04-127">Reasons for not hosting in IIS include:</span></span>

- <span data-ttu-id="26d04-128">Prostředí, ve kterém služby IIS není k dispozici nebo není žádoucí, jako je například existující serverové farmy bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="26d04-128">Environments where IIS is not available or desirable, such as an existing server farm without IIS.</span></span>
- <span data-ttu-id="26d04-129">Nároky na výkon služby IIS je třeba se jim vyhnout.</span><span class="sxs-lookup"><span data-stu-id="26d04-129">The performance overhead of IIS needs to be avoided.</span></span>
- <span data-ttu-id="26d04-130">Funkce SignalR je přidat do existující instanci služby aplikace, která běží ve službě Windows, rolí pracovního procesu systému Azure nebo jiný proces.</span><span class="sxs-lookup"><span data-stu-id="26d04-130">SignalR functionality is to be added to an exising application that runs in a Windows Service, Azure worker role, or other process.</span></span>

<span data-ttu-id="26d04-131">Pokud řešení je vyvíjen jako hostování na vlastním serveru z důvodů výkonu, doporučujeme také testovací aplikace hostované ve službě IIS a určí, zlepšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="26d04-131">If a solution is being developed as self-host for performance reasons, it's recommended to also test the application hosted in IIS to determine the performance benefit.</span></span>

<span data-ttu-id="26d04-132">Tento kurz obsahuje následující části:</span><span class="sxs-lookup"><span data-stu-id="26d04-132">This tutorial contains the following sections:</span></span>

- [<span data-ttu-id="26d04-133">Vytvoření serveru</span><span class="sxs-lookup"><span data-stu-id="26d04-133">Creating the server</span></span>](#server)
- [<span data-ttu-id="26d04-134">Přístup k serveru pomocí jazyka JavaScript klienta</span><span class="sxs-lookup"><span data-stu-id="26d04-134">Accessing the server with a JavaScript client</span></span>](#js)

<a id="server"></a>

## <a name="creating-the-server"></a><span data-ttu-id="26d04-135">Vytvoření serveru</span><span class="sxs-lookup"><span data-stu-id="26d04-135">Creating the server</span></span>

<span data-ttu-id="26d04-136">V tomto kurzu vytvoříte server, který je hostován v konzolové aplikaci, ale server je možné hostovat v jakýkoli druh procesu, například služby Windows nebo role pracovního procesu Azure.</span><span class="sxs-lookup"><span data-stu-id="26d04-136">In this tutorial, you'll create a server that's hosted in a console application, but the server can be hosted in any sort of process, such as a Windows service or Azure worker role.</span></span> <span data-ttu-id="26d04-137">Ukázkový kód pro hostování serveru SignalR ve službě Windows, naleznete v tématu [Self-Hosting SignalR ve službě Windows](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).</span><span class="sxs-lookup"><span data-stu-id="26d04-137">For sample code for hosting a SignalR server in a Windows Service, see [Self-Hosting SignalR in a Windows Service](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).</span></span>

1. <span data-ttu-id="26d04-138">Otevřete Visual Studio 2013 s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="26d04-138">Open Visual Studio 2013 with administrator privileges.</span></span> <span data-ttu-id="26d04-139">Vyberte **souboru**, **nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="26d04-139">Select **File**, **New Project**.</span></span> <span data-ttu-id="26d04-140">Vyberte **Windows** pod **Visual C#** uzlu v **šablony** podokně a vyberte **konzolovou aplikaci** šablony.</span><span class="sxs-lookup"><span data-stu-id="26d04-140">Select **Windows** under the **Visual C#** node in the **Templates** pane, and select the **Console Application** template.</span></span> <span data-ttu-id="26d04-141">Název nového projektu "SignalRSelfHost" a klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="26d04-141">Name the new project "SignalRSelfHost" and click **OK**.</span></span>

    ![](tutorial-signalr-self-host/_static/image1.png)
2. <span data-ttu-id="26d04-142">Otevřete konzoly Správce balíčků NuGet tak, že vyberete **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="26d04-142">Open the NuGet package manager console by selecting **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>
3. <span data-ttu-id="26d04-143">V konzole Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="26d04-143">In the package manager console, enter the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample1.ps1)]

    <span data-ttu-id="26d04-144">Tento příkaz přidá do projektu knihovny SignalR 2 Self-Host.</span><span class="sxs-lookup"><span data-stu-id="26d04-144">This command adds the SignalR 2 Self-Host libraries to the project.</span></span>
4. <span data-ttu-id="26d04-145">V konzole Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="26d04-145">In the package manager console, enter the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample2.ps1)]

    <span data-ttu-id="26d04-146">Tento příkaz přidá Microsoft.Owin.Cors knihovny do projektu.</span><span class="sxs-lookup"><span data-stu-id="26d04-146">This command adds the Microsoft.Owin.Cors library to the project.</span></span> <span data-ttu-id="26d04-147">Tato knihovna se použije pro podporu napříč doménami, které je potřeba pro aplikace, které jsou hostiteli SignalR a webové stránky klienta v různých doménách.</span><span class="sxs-lookup"><span data-stu-id="26d04-147">This library will be used for cross-domain support, which is required for applications that host SignalR and a web page client in different domains.</span></span> <span data-ttu-id="26d04-148">Protože je budete hostování serveru SignalR a webový klient na jiném portu, to znamená, že mezi doménami musí být povolené pro komunikaci mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="26d04-148">Since you'll be hosting the SignalR server and the web client on different ports, this means that cross-domain must be enabled for communication between these components.</span></span>
5. <span data-ttu-id="26d04-149">Nahraďte obsah Program.cs následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="26d04-149">Replace the contents of Program.cs with the following code.</span></span>

    [!code-csharp[Main](tutorial-signalr-self-host/samples/sample3.cs)]

    <span data-ttu-id="26d04-150">Ve výše uvedeném kódu obsahuje tři třídy:</span><span class="sxs-lookup"><span data-stu-id="26d04-150">The above code includes three classes:</span></span>

    - <span data-ttu-id="26d04-151">**Program**, včetně **hlavní** metoda definování primární cesty spuštění.</span><span class="sxs-lookup"><span data-stu-id="26d04-151">**Program**, including the **Main** method defining the primary path of execution.</span></span> <span data-ttu-id="26d04-152">V této metodě, webové aplikace typu **spuštění** je spuštěn na zadané adrese URL (`http://localhost:8080`).</span><span class="sxs-lookup"><span data-stu-id="26d04-152">In this method, a web application of type **Startup** is started at the specified URL (`http://localhost:8080`).</span></span> <span data-ttu-id="26d04-153">Pro potřeby zabezpečení na koncový bod je možné implementovat SSL.</span><span class="sxs-lookup"><span data-stu-id="26d04-153">If security is required on the endpoint, SSL can be implemented.</span></span> <span data-ttu-id="26d04-154">Zobrazit [jak: Konfigurace portu s certifikátem SSL](https://msdn.microsoft.com/library/ms733791.aspx) Další informace.</span><span class="sxs-lookup"><span data-stu-id="26d04-154">See [How to: Configure a Port with an SSL Certificate](https://msdn.microsoft.com/library/ms733791.aspx) for more information.</span></span>
    - <span data-ttu-id="26d04-155">**Po spuštění**, třída obsahující konfiguraci serveru funkce SignalR (Tento kurz používá pouze konfigurace je volání `UseCors`) a volání `MapSignalR`, která vytvoří trasy pro všechny objekty centra v projektu.</span><span class="sxs-lookup"><span data-stu-id="26d04-155">**Startup**, the class containing the configuration for the SignalR server (the only configuration this tutorial uses is the call to `UseCors`), and the call to `MapSignalR`, which creates routes for any Hub objects in the project.</span></span>
    - <span data-ttu-id="26d04-156">**MyHub**, třída rozbočovače SignalR, která aplikace bude poskytovat klientům.</span><span class="sxs-lookup"><span data-stu-id="26d04-156">**MyHub**, the SignalR Hub class that the application will provide to clients.</span></span> <span data-ttu-id="26d04-157">Tato třída obsahuje jedinou metodu **odeslat**, že klienti zavolá k vysílání zprávy do všech ostatních připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="26d04-157">This class has a single method, **Send**, that clients will call to broadcast a message to all other connected clients.</span></span>
6. <span data-ttu-id="26d04-158">Kompilace a spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="26d04-158">Compile and run the application.</span></span> <span data-ttu-id="26d04-159">Adresa, na kterém běží server by měl zobrazit v okně konzoly.</span><span class="sxs-lookup"><span data-stu-id="26d04-159">The address that the server is running should show in a console window.</span></span>

    ![](tutorial-signalr-self-host/_static/image2.png)
7. <span data-ttu-id="26d04-160">Pokud se nezdaří spuštění s výjimkou `System.Reflection.TargetInvocationException was unhandled`, budete muset restartovat Visual Studio s oprávněními správce.</span><span class="sxs-lookup"><span data-stu-id="26d04-160">If execution fails with the exception `System.Reflection.TargetInvocationException was unhandled`, you will need to restart Visual Studio with administrator privileges.</span></span>
8. <span data-ttu-id="26d04-161">Zastavte aplikaci, než budete pokračovat k další části.</span><span class="sxs-lookup"><span data-stu-id="26d04-161">Stop the application before proceeding to the next section.</span></span>

<a id="js"></a>

## <a name="accessing-the-server-with-a-javascript-client"></a><span data-ttu-id="26d04-162">Přístup k serveru pomocí jazyka JavaScript klienta</span><span class="sxs-lookup"><span data-stu-id="26d04-162">Accessing the server with a JavaScript client</span></span>

<span data-ttu-id="26d04-163">V této části použijete stejného klienta JavaScript z [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="26d04-163">In this section, you'll use the same JavaScript client from the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md).</span></span> <span data-ttu-id="26d04-164">Teď uděláme pouze jeden změny klienta, který je explicitně definují adresu URL centra.</span><span class="sxs-lookup"><span data-stu-id="26d04-164">We'll only make one modification to the client, which is to explicitly define the hub URL.</span></span> <span data-ttu-id="26d04-165">Pomocí aplikace v místním prostředí serveru nemusí být nutně na stejné adrese jako adresu URL připojení (z důvodu reverzních proxy serverů a nástroje pro vyrovnávání zatížení), tak adresy URL musí být definované explicitně.</span><span class="sxs-lookup"><span data-stu-id="26d04-165">With a self-hosted application, the server may not necessarily be at the same address as the connection URL (due to reverse proxies and load balancers), so the URL needs to be defined explicitly.</span></span>

1. <span data-ttu-id="26d04-166">V **Průzkumníka řešení**, klikněte pravým tlačítkem na řešení a vyberte **přidat**, **nový projekt**.</span><span class="sxs-lookup"><span data-stu-id="26d04-166">In **Solution Explorer**, right-click on the solution and select **Add**, **New Project**.</span></span> <span data-ttu-id="26d04-167">Vyberte **webové** uzel a vyberte **webová aplikace ASP.NET** šablony.</span><span class="sxs-lookup"><span data-stu-id="26d04-167">Select the **Web** node, and select the **ASP.NET Web Application** template.</span></span> <span data-ttu-id="26d04-168">Pojmenujte projekt "JavascriptClient" a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="26d04-168">Name the project "JavascriptClient" and click **OK**.</span></span>

    ![](tutorial-signalr-self-host/_static/image3.png)
2. <span data-ttu-id="26d04-169">Vyberte **prázdný** šablony a nechte nevybranou zbývající možnosti.</span><span class="sxs-lookup"><span data-stu-id="26d04-169">Select the **Empty** template, and leave the remaining options unselected.</span></span> <span data-ttu-id="26d04-170">Vyberte **vytvoření projektu**.</span><span class="sxs-lookup"><span data-stu-id="26d04-170">Select **Create Project**.</span></span>

    ![](tutorial-signalr-self-host/_static/image4.png)
3. <span data-ttu-id="26d04-171">V konzole Správce balíčků, vyberte projekt "JavascriptClient" v **výchozí projekt** rozevíracího seznamu a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="26d04-171">In the package manager console, select the "JavascriptClient" project in the **Default project** drop-down, and execute the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample4.ps1)]

    <span data-ttu-id="26d04-172">Tento příkaz nainstaluje knihovny SignalR a JQuery, které budete potřebovat v klientovi.</span><span class="sxs-lookup"><span data-stu-id="26d04-172">This command installs the SignalR and JQuery libraries that you'll need in the client.</span></span>
4. <span data-ttu-id="26d04-173">Klikněte pravým tlačítkem na projekt a vyberte **přidat**, **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="26d04-173">Right-click on your project and select **Add**, **New Item**.</span></span> <span data-ttu-id="26d04-174">Vyberte **webové** uzel a vyberte stránku HTML.</span><span class="sxs-lookup"><span data-stu-id="26d04-174">Select the **Web** node, and select HTML Page.</span></span> <span data-ttu-id="26d04-175">Pojmenujte stránku **Default.html**.</span><span class="sxs-lookup"><span data-stu-id="26d04-175">Name the page **Default.html**.</span></span>

    ![](tutorial-signalr-self-host/_static/image5.png)
5. <span data-ttu-id="26d04-176">Nahraďte obsah novou stránku HTML s následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="26d04-176">Replace the contents of the new HTML page with the following code.</span></span> <span data-ttu-id="26d04-177">Ověřte, že tady skriptových odkazů odpovídají skripty ve složce Scripts projektu.</span><span class="sxs-lookup"><span data-stu-id="26d04-177">Verify that the script references here match the scripts in the Scripts folder of the project.</span></span>

    [!code-html[Main](tutorial-signalr-self-host/samples/sample5.html?highlight=31-32)]

    <span data-ttu-id="26d04-178">Následující kód (zvýrazněné v ukázkovém kódu výše) je, jež jste udělali klient použitý v tomto kurzu Začínáme Stared (navíc k upgradu kód na beta verzi 2 SignalR).</span><span class="sxs-lookup"><span data-stu-id="26d04-178">The following code (highlighted in the code sample above) is the addition that you've made to the client used in the Getting Stared tutorial (in addition to upgrading the code to SignalR version 2 beta).</span></span> <span data-ttu-id="26d04-179">Tento řádek kódu explicitně nastaví adresu URL připojení databáze pro funkci SignalR na serveru.</span><span class="sxs-lookup"><span data-stu-id="26d04-179">This line of code explicitly sets the base connection URL for SignalR on the server.</span></span>

    [!code-javascript[Main](tutorial-signalr-self-host/samples/sample6.js)]
6. <span data-ttu-id="26d04-180">Klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění...** . Vyberte **více projektů po spuštění** přepínač a nastavte oba projekty **akce** k **Start**.</span><span class="sxs-lookup"><span data-stu-id="26d04-180">Right-click on the solution, and select **Set Startup Projects...**. Select the **Multiple startup projects** radio button, and set both projects' **Action** to **Start**.</span></span>

    ![](tutorial-signalr-self-host/_static/image6.png)
7. <span data-ttu-id="26d04-181">Klikněte pravým tlačítkem na "Default.html" a vyberte **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="26d04-181">Right-click on "Default.html" and select **Set As Start Page**.</span></span>
8. <span data-ttu-id="26d04-182">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="26d04-182">Run the application.</span></span> <span data-ttu-id="26d04-183">Spustí se server a stránky.</span><span class="sxs-lookup"><span data-stu-id="26d04-183">The server and page will launch.</span></span> <span data-ttu-id="26d04-184">Možná budete muset znovu načíst webové stránky (nebo vyberte **pokračovat** v ladicím programu) Pokud se stránka načte předtím, než je server spuštěn.</span><span class="sxs-lookup"><span data-stu-id="26d04-184">You may need to reload the web page (or select **Continue** in the debugger) if the page loads before the server is started.</span></span>
9. <span data-ttu-id="26d04-185">V prohlížeči zadejte uživatelské jméno, po zobrazení výzvy.</span><span class="sxs-lookup"><span data-stu-id="26d04-185">In the browser, provide a username when prompted.</span></span> <span data-ttu-id="26d04-186">Zkopírujte adresu URL stránky v jiném okně nebo kartě prohlížeče a zadejte jiné uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="26d04-186">Copy the page's URL into another browser tab or window and provide a different username.</span></span> <span data-ttu-id="26d04-187">Budete moct posílat zprávy z podokna jeden prohlížeč do jiné, jako v kurzu Začínáme.</span><span class="sxs-lookup"><span data-stu-id="26d04-187">You will be able to send messages from one browser pane to the other, as in the Getting Started tutorial.</span></span>
