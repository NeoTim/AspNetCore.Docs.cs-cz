---
uid: signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
title: Použití čítačů výkonu SignalR webové role Azure | Dokumentace Microsoftu
author: guardrex
description: Postup instalace a použití čítačů výkonu SignalR webové role Azure.
keywords: Čítač ASP.NET,signalr,Performance, webové role azure
ms.author: bradyg
ms.date: 10/03/2018
ms.assetid: 2a127d3b-21ed-4cc9-bec0-cdab4e742a25
msc.legacyurl: /signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
msc.type: authoredcontent
ms.openlocfilehash: 8e17e945bc144731dd149bd7ddfc9e29160eaf0b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836480"
---
# <a name="using-signalr-performance-counters-in-an-azure-web-role"></a><span data-ttu-id="5addd-104">Použití čítačů výkonu SignalR webové role Azure</span><span class="sxs-lookup"><span data-stu-id="5addd-104">Using SignalR performance counters in an Azure Web Role</span></span>

<span data-ttu-id="5addd-105">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5addd-105">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="5addd-106">Čítače výkonu SignalR slouží k monitorování výkonu vaší aplikace do webové Role Azure.</span><span class="sxs-lookup"><span data-stu-id="5addd-106">SignalR performance counters are used to monitor your app's performance in an Azure Web Role.</span></span> <span data-ttu-id="5addd-107">Čítače jsou zachyceny na základě Microsoft Azure Diagnostics.</span><span class="sxs-lookup"><span data-stu-id="5addd-107">The counters are captured by Microsoft Azure Diagnostics.</span></span> <span data-ttu-id="5addd-108">Instalace čítačů výkonu SignalR v Azure s *signalr.exe*, nástroje, který používá pro samostatnou službu nebo místní aplikace.</span><span class="sxs-lookup"><span data-stu-id="5addd-108">You install SignalR performance counters on Azure with *signalr.exe*, the same tool used for standalone or on-premises apps.</span></span> <span data-ttu-id="5addd-109">Protože role služby Azure jsou přechodné, konfigurace aplikace pro instalaci a registraci čítačů výkonu SignalR při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5addd-109">Since Azure roles are transient, you configure an app to install and register SignalR performance counters upon startup.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5addd-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="5addd-110">Prerequisites</span></span>

* <span data-ttu-id="5addd-111">Visual Studio 2015 nebo [2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)</span><span class="sxs-lookup"><span data-stu-id="5addd-111">Visual Studio 2015 or [2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)</span></span>
* <span data-ttu-id="5addd-112">[Microsoft Azure SDK pro Visual Studio](https://azure.microsoft.com/downloads/) **Poznámka: Restartujte počítač po instalaci sady SDK.**</span><span class="sxs-lookup"><span data-stu-id="5addd-112">[Microsoft Azure SDK for Visual Studio](https://azure.microsoft.com/downloads/) **Note: Restart your machine after installing the SDK.**</span></span>
* <span data-ttu-id="5addd-113">Předplatné Microsoft Azure: Zaregistrujte si bezplatný účet Azure zkušební, najdete v tématu [bezplatná zkušební verze Azure](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="5addd-113">Microsoft Azure subscription: To sign up for a free Azure trial account, see [Azure Free Trial](https://azure.microsoft.com/free/).</span></span>

## <a name="creating-an-azure-web-role-application-that-exposes-signalr-performance-counters"></a><span data-ttu-id="5addd-114">Vytvoření webové Role Azure aplikace, která zveřejňuje čítačů výkonu SignalR</span><span class="sxs-lookup"><span data-stu-id="5addd-114">Creating an Azure Web Role application that exposes SignalR performance counters</span></span>

1. <span data-ttu-id="5addd-115">Otevřít Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5addd-115">Open Visual Studio.</span></span>

2. <span data-ttu-id="5addd-116">V sadě Visual Studio, vyberte **souboru** > **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="5addd-116">In Visual Studio, select **File** > **New** > **Project**.</span></span>

3. <span data-ttu-id="5addd-117">V **nový projekt** dialogové okno, vyberte **Visual C#** > **cloudu** kategorii na levé straně a pak vyberte **cloudovéslužbyAzure** šablony.</span><span class="sxs-lookup"><span data-stu-id="5addd-117">In the **New Project** dialog box, select the **Visual C#** > **Cloud** category on the left, and then select the **Azure Cloud Service** template.</span></span> <span data-ttu-id="5addd-118">Pojmenujte aplikaci **SignalRPerfCounters** a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="5addd-118">Name the app **SignalRPerfCounters** and select **OK**.</span></span>

   ![Nové cloudové aplikace](using-signalr-performance-counters-in-an-azure-web-role/_static/image1.png)

   > [!NOTE]
   > <span data-ttu-id="5addd-120">Pokud se nezobrazí **cloudu** kategorii šablon nebo **cloudové služby Azure** šablony, je nutné nainstalovat **vývoj pro Azure** úloh pro Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="5addd-120">If you don't see the **Cloud** template category or the **Azure Cloud Service** template, you need to install the **Azure development** workload for Visual Studio 2017.</span></span> <span data-ttu-id="5addd-121">Zvolte **otevřít instalační program Visual Studio** odkaz na levém okraji **nový projekt** dialogové okno otevřít instalační program sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5addd-121">Choose the **Open Visual Studio Installer** link on the bottom-left side of the **New Project** dialog to open Visual Studio Installer.</span></span> <span data-ttu-id="5addd-122">Vyberte **vývoj pro Azure** úlohy a klikněte na tlačítko **změnit** spusťte při instalaci úlohy.</span><span class="sxs-lookup"><span data-stu-id="5addd-122">Select the **Azure development** workload, and then choose **Modify** to start installing the workload.</span></span>
   >
   > ![Úlohy pro vývoj pro Azure v instalačním programu Visual Studio](using-signalr-performance-counters-in-an-azure-web-role/_static/azure-development-workload.png)

4. <span data-ttu-id="5addd-124">V **nová Cloudová služba Microsoft Azure** dialogového okna, vyberte **webovou roli ASP.NET** a vyberte > a přidejte do projektu role.</span><span class="sxs-lookup"><span data-stu-id="5addd-124">In the **New Microsoft Azure Cloud Service** dialog, select **ASP.NET Web Role** and select the > button to add the role to the project.</span></span> <span data-ttu-id="5addd-125">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="5addd-125">Select **OK**.</span></span>

   ![Přidat webová Role ASP.NET](using-signalr-performance-counters-in-an-azure-web-role/_static/image2.png)

5. <span data-ttu-id="5addd-127">V **nová webová aplikace ASP.NET - WebRole1** dialogového okna, vyberte **MVC** šablonu a pak vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="5addd-127">In the **New ASP.NET Web Application - WebRole1** dialog, select the **MVC** template, and then select **OK**.</span></span>

   ![Přidat MVC a webového rozhraní API](using-signalr-performance-counters-in-an-azure-web-role/_static/image3.png)

6. <span data-ttu-id="5addd-129">V **Průzkumníka řešení**, otevřete *diagnostics.wadcfgx* soubor **WebRole1**.</span><span class="sxs-lookup"><span data-stu-id="5addd-129">In **Solution Explorer**, open the *diagnostics.wadcfgx* file under **WebRole1**.</span></span>

   ![Diagnostics.wadcfgx Průzkumníka řešení](using-signalr-performance-counters-in-an-azure-web-role/_static/image4.png)

7. <span data-ttu-id="5addd-131">Nahraďte obsah souboru s následující konfigurací a soubor uložte:</span><span class="sxs-lookup"><span data-stu-id="5addd-131">Replace the contents of the file with the following configuration and save the file:</span></span>

   [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample1.xml)]

8. <span data-ttu-id="5addd-132">Otevřít **Konzola správce balíčků** z **nástroje** > **Správce balíčků NuGet**.</span><span class="sxs-lookup"><span data-stu-id="5addd-132">Open the **Package Manager Console** from **Tools** > **NuGet Package Manager**.</span></span> <span data-ttu-id="5addd-133">Zadejte následující příkazy, abyste nainstalovali nejnovější verzi SignalR a balíček nástroje SignalR:</span><span class="sxs-lookup"><span data-stu-id="5addd-133">Enter the following commands to install the latest version of SignalR and the SignalR utilities package:</span></span>

   [!code-powershell[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample2.ps1)]

9. <span data-ttu-id="5addd-134">Konfigurace aplikace k instalaci čítačů výkonu SignalR do role instance při spuštění nebo recykluje.</span><span class="sxs-lookup"><span data-stu-id="5addd-134">Configure the app to install the SignalR performance counters into the role instance when it starts up or recycles.</span></span> <span data-ttu-id="5addd-135">V **Průzkumníka řešení**, klikněte pravým tlačítkem na **WebRole1** projektu a vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5addd-135">In **Solution Explorer**, right-click on the **WebRole1** project and select **Add** > **New Folder**.</span></span> <span data-ttu-id="5addd-136">Název nové složky *spuštění*.</span><span class="sxs-lookup"><span data-stu-id="5addd-136">Name the new folder *Startup*.</span></span>

   ![Přidat složka po spuštění](using-signalr-performance-counters-in-an-azure-web-role/_static/image5.png)

10. <span data-ttu-id="5addd-138">Kopírovat *signalr.exe* souboru (přidá s **Microsoft.AspNet.SignalR.Utils** balíček) z \<složky projektu > / SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils.\< verze > / nástrojů k *spuštění* složku, kterou jste vytvořili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="5addd-138">Copy the *signalr.exe* file (added with the **Microsoft.AspNet.SignalR.Utils** package) from \<project folder>/SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils.\<version>/tools to the *Startup* folder you created in the previous step.</span></span>

11. <span data-ttu-id="5addd-139">V **Průzkumníku řešení**, klikněte pravým tlačítkem myši *spuštění* a pak zvolte položku **přidat** > **existující položku**.</span><span class="sxs-lookup"><span data-stu-id="5addd-139">In **Solution Explorer**, right-click the *Startup* folder and select **Add** > **Existing Item**.</span></span> <span data-ttu-id="5addd-140">V zobrazeném dialogu vyberte *signalr.exe* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="5addd-140">In the dialog that appears, select *signalr.exe* and select **Add**.</span></span>

    ![Přidat signalr.exe do projektu](using-signalr-performance-counters-in-an-azure-web-role/_static/image6.png)

12. <span data-ttu-id="5addd-142">Klikněte pravým tlačítkem na *spuštění* složky, které jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="5addd-142">Right-click on the *Startup* folder you created.</span></span> <span data-ttu-id="5addd-143">Vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="5addd-143">Select **Add** > **New Item**.</span></span> <span data-ttu-id="5addd-144">Vyberte **Obecné** uzlu, vyberte **textový soubor**a pojmenujte novou položku *SignalRPerfCounterInstall.cmd*.</span><span class="sxs-lookup"><span data-stu-id="5addd-144">Select the **General** node, select **Text File**, and name the new item *SignalRPerfCounterInstall.cmd*.</span></span> <span data-ttu-id="5addd-145">Tento soubor příkaz nainstaluje čítačů výkonu SignalR do webové role.</span><span class="sxs-lookup"><span data-stu-id="5addd-145">This command file will install the SignalR performance counters into the web role.</span></span>

    ![Vytvořit soubor batch instalace čítače výkonu SignalR](using-signalr-performance-counters-in-an-azure-web-role/_static/image7.png)

13. <span data-ttu-id="5addd-147">Když Visual Studio vytvoří *SignalRPerfCounterInstall.cmd* soubor, automaticky otevře v hlavním okně.</span><span class="sxs-lookup"><span data-stu-id="5addd-147">When Visual Studio creates the *SignalRPerfCounterInstall.cmd* file, it will automatically open in the main window.</span></span> <span data-ttu-id="5addd-148">Nahraďte obsah souboru následující skript, pak uložte a zavřete soubor.</span><span class="sxs-lookup"><span data-stu-id="5addd-148">Replace the contents of the file with the following script, then save and close the file.</span></span> <span data-ttu-id="5addd-149">Tento skript spustí *signalr.exe*, který přidá čítačů výkonu SignalR k instanci role.</span><span class="sxs-lookup"><span data-stu-id="5addd-149">This script executes *signalr.exe*, which adds the SignalR performance counters to the role instance.</span></span>

    [!code-console[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample3.cmd)]

14. <span data-ttu-id="5addd-150">Vyberte *signalr.exe* ve **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="5addd-150">Select the *signalr.exe* file in **Solution Explorer**.</span></span> <span data-ttu-id="5addd-151">V souboru **vlastnosti**, nastavte **kopírovat do výstupního adresáře** k **vždy Kopírovat**.</span><span class="sxs-lookup"><span data-stu-id="5addd-151">In the file's **Properties**, set **Copy to Output Directory** to **Copy Always**.</span></span>

    ![Nastavte kopírovat do výstupního adresáře vždy kopírovat](using-signalr-performance-counters-in-an-azure-web-role/_static/image8.png)

15. <span data-ttu-id="5addd-153">Opakujte předchozí krok u *SignalRPerfCounterInstall.cmd* souboru.</span><span class="sxs-lookup"><span data-stu-id="5addd-153">Repeat the previous step for the *SignalRPerfCounterInstall.cmd* file.</span></span>


16. <span data-ttu-id="5addd-154">Klikněte pravým tlačítkem na *SignalRPerfCounterInstall.cmd* a vyberte možnost **otevřít v**.</span><span class="sxs-lookup"><span data-stu-id="5addd-154">Right-click on the *SignalRPerfCounterInstall.cmd* file and select **Open With**.</span></span> <span data-ttu-id="5addd-155">V zobrazeném dialogu vyberte **binární Editor** a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="5addd-155">In the dialog that appears, select **Binary Editor** and select **OK**.</span></span>

    ![Otevřít pomocí editoru binárního souboru](using-signalr-performance-counters-in-an-azure-web-role/_static/image9.png)

17. <span data-ttu-id="5addd-157">V binárním editoru vyberte všechny úvodní bajty v souboru a odstraňte je.</span><span class="sxs-lookup"><span data-stu-id="5addd-157">In the binary editor, select any leading bytes in the file and delete them.</span></span> <span data-ttu-id="5addd-158">Soubor uložte a zavřete.</span><span class="sxs-lookup"><span data-stu-id="5addd-158">Save and close the file.</span></span>

    ![Odstranit úvodní bajty](using-signalr-performance-counters-in-an-azure-web-role/_static/image10.png)

18. <span data-ttu-id="5addd-160">Otevřít *ServiceDefinition.csdef* a přidání úlohy po spuštění, který se spustí *SignalrPerfCounterInstall.cmd* souboru při spuštění služby:</span><span class="sxs-lookup"><span data-stu-id="5addd-160">Open *ServiceDefinition.csdef* and add a startup task that executes the *SignalrPerfCounterInstall.cmd* file when the service starts up:</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample4.xml?highlight=4-7)]

19. <span data-ttu-id="5addd-161">Otevřít `Views/Shared/_Layout.cshtml` a odebrat skript sady jQuery od konce souboru.</span><span class="sxs-lookup"><span data-stu-id="5addd-161">Open `Views/Shared/_Layout.cshtml` and remove the jQuery bundle script from the end of the file.</span></span>

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample5.cshtml)]

20. <span data-ttu-id="5addd-162">Přidejte javascriptový klient, který nepřetržitě volá `increment` metoda na serveru.</span><span class="sxs-lookup"><span data-stu-id="5addd-162">Add a JavaScript client that continuously calls the `increment` method on the server.</span></span> <span data-ttu-id="5addd-163">Otevřít `Views/Home/Index.cshtml` a nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5addd-163">Open `Views/Home/Index.cshtml` and replace the contents with the following code:</span></span>

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample6.cshtml)]

21. <span data-ttu-id="5addd-164">Vytvořte novou složku v **WebRole1** projekt s názvem *rozbočovače*.</span><span class="sxs-lookup"><span data-stu-id="5addd-164">Create a new folder in the **WebRole1** project named *Hubs*.</span></span> <span data-ttu-id="5addd-165">Klikněte pravým tlačítkem myši *rozbočovače* složky v **Průzkumníku řešení** a vyberte **přidat** > **nová položka**.</span><span class="sxs-lookup"><span data-stu-id="5addd-165">Right-click the *Hubs* folder in **Solution Explorer** and select **Add** > **New Item**.</span></span> <span data-ttu-id="5addd-166">V **přidat novou položku** dialogové okno, vyberte **webové** > **SignalR** kategorie a pak vyberte **třída rozbočovače SignalR (v2)** šablony položky.</span><span class="sxs-lookup"><span data-stu-id="5addd-166">In the **Add New Item** dialog box, select the **Web** > **SignalR** category, and then select the **SignalR Hub Class (v2)** item template.</span></span> <span data-ttu-id="5addd-167">Název nového centra *MyHub.cs* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="5addd-167">Name the new hub *MyHub.cs* and select **Add**.</span></span>

    ![Přidání třídy rozbočovače SignalR do rozbočovače složky v dialogovém okně Přidat novou položku](using-signalr-performance-counters-in-an-azure-web-role/_static/image13.png)

22. <span data-ttu-id="5addd-169">*MyHub.cs* se automaticky otevře v hlavním okně.</span><span class="sxs-lookup"><span data-stu-id="5addd-169">*MyHub.cs* will automatically open in the main window.</span></span> <span data-ttu-id="5addd-170">Nahraďte obsah následujícím kódem, pak uložte a zavřete soubor:</span><span class="sxs-lookup"><span data-stu-id="5addd-170">Replace the contents with the following code, then save and close the file:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample7.cs)]

23. <span data-ttu-id="5addd-171">*[Crank.exe](signalr-connection-density-testing-with-crank.md)*  je testování nástroj, opatřeného SignalR codebase hustoty připojení.</span><span class="sxs-lookup"><span data-stu-id="5addd-171">*[Crank.exe](signalr-connection-density-testing-with-crank.md)* is a connection density testing tool provided with the SignalR codebase.</span></span> <span data-ttu-id="5addd-172">Protože Crank vyžaduje trvalé připojení, přidáte jej do vaší lokality pro použití při testování.</span><span class="sxs-lookup"><span data-stu-id="5addd-172">Since Crank requires a persistent connection, you add one to your site for use when testing.</span></span> <span data-ttu-id="5addd-173">Přidat novou složku **WebRole1** projekt s názvem *PersistentConnections*.</span><span class="sxs-lookup"><span data-stu-id="5addd-173">Add a new folder to the **WebRole1** project called *PersistentConnections*.</span></span> <span data-ttu-id="5addd-174">Klikněte pravým tlačítkem na tuto složku a vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="5addd-174">Right-click this folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5addd-175">Pojmenujte nový soubor třídy *MyPersistentConnections.cs* a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="5addd-175">Name the new class file *MyPersistentConnections.cs* and select **Add**.</span></span>

24. <span data-ttu-id="5addd-176">Visual Studio otevře *MyPersistentConnections.cs* soubor v hlavním okně.</span><span class="sxs-lookup"><span data-stu-id="5addd-176">Visual Studio will open the *MyPersistentConnections.cs* file in the main window.</span></span> <span data-ttu-id="5addd-177">Nahraďte obsah následujícím kódem, pak uložte a zavřete soubor:</span><span class="sxs-lookup"><span data-stu-id="5addd-177">Replace the contents with the following code, then save and close the file:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample8.cs)]

25. <span data-ttu-id="5addd-178">Použití `Startup` třídu, objekty SignalR spustit při spuštění OWIN.</span><span class="sxs-lookup"><span data-stu-id="5addd-178">Using the `Startup` class, the SignalR objects start when OWIN starts up.</span></span> <span data-ttu-id="5addd-179">Otevřete nebo vytvořte *Startup.cs* a nahraďte jeho obsah následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5addd-179">Open or create *Startup.cs* and replace the contents with the following code:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample9.cs)]

    <span data-ttu-id="5addd-180">Ve výše uvedeném kódu `OwinStartup` atribut určí tuto třídu pro spuštění OWIN.</span><span class="sxs-lookup"><span data-stu-id="5addd-180">In the code above, the `OwinStartup` attribute marks this class to start OWIN.</span></span> <span data-ttu-id="5addd-181">`Configuration` Metoda začíná SignalR.</span><span class="sxs-lookup"><span data-stu-id="5addd-181">The `Configuration` method starts SignalR.</span></span>

26. <span data-ttu-id="5addd-182">Otestujte aplikaci stisknutím klávesy v Microsoft Azure Emulator **F5**.</span><span class="sxs-lookup"><span data-stu-id="5addd-182">Test your application in the Microsoft Azure Emulator by pressing **F5**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="5addd-183">Pokud narazíte **FileLoadException –** na **MapSignalR**, změnit přesměrování vazby v *web.config* takto:</span><span class="sxs-lookup"><span data-stu-id="5addd-183">If you encounter a **FileLoadException** at **MapSignalR**, change the binding redirects in *web.config* to the following:</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample12.xml?highlight=3,7)]

27. <span data-ttu-id="5addd-184">Počkejte přibližně jednu minutu.</span><span class="sxs-lookup"><span data-stu-id="5addd-184">Wait about one minute.</span></span> <span data-ttu-id="5addd-185">Otevřete panel nástrojů Průzkumník cloudu v sadě Visual Studio (**zobrazení** > **Průzkumníka cloudu**) a rozbalte cestu `(Local)/Storage Accounts/(Development)/Tables`.</span><span class="sxs-lookup"><span data-stu-id="5addd-185">Open the Cloud Explorer tool window in Visual Studio (**View** > **Cloud Explorer**) and expand the path `(Local)/Storage Accounts/(Development)/Tables`.</span></span> <span data-ttu-id="5addd-186">Dvakrát klikněte na panel **WADPerformanceCountersTable**.</span><span class="sxs-lookup"><span data-stu-id="5addd-186">Double-click **WADPerformanceCountersTable**.</span></span> <span data-ttu-id="5addd-187">Měli byste vidět čítače SignalR v datech tabulky.</span><span class="sxs-lookup"><span data-stu-id="5addd-187">You should see SignalR counters in the table data.</span></span> <span data-ttu-id="5addd-188">Pokud se nezobrazí v tabulce, budete muset znovu zadat svoje přihlašovací údaje služby Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="5addd-188">If you don't see the table, you may need to re-enter your Azure Storage credentials.</span></span> <span data-ttu-id="5addd-189">Budete muset vybrat **aktualizovat** tlačítko naleznete v tabulce v **Průzkumníka cloudu** nebo vyberte **aktualizovat** tlačítka v okně Otevřít tabulku k zobrazení dat v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5addd-189">You may need to select the **Refresh** button to see the table in **Cloud Explorer** or select the **Refresh** button in the open table window to see data in the table.</span></span>

    ![Výběr tabulky WAD čítače výkonu v Průzkumníku cloudu sady Visual Studio](using-signalr-performance-counters-in-an-azure-web-role/_static/image11.png)

    ![Zobrazuje čítače shromažďují v tabulce čítače výkonu využitím WAD](using-signalr-performance-counters-in-an-azure-web-role/_static/image12.png)

28. <span data-ttu-id="5addd-192">Chcete-li testovat aplikace v cloudu, aktualizujte **ServiceConfiguration.Cloud.cscfg** souboru a nastavit `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` na platný připojovací řetězec účtu úložiště Azure.</span><span class="sxs-lookup"><span data-stu-id="5addd-192">To test your application in the cloud, update the **ServiceConfiguration.Cloud.cscfg** file and set the `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` to a valid Azure Storage account connection string.</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample10.xml)]

29. <span data-ttu-id="5addd-193">Nasaďte aplikaci do vašeho předplatného Azure.</span><span class="sxs-lookup"><span data-stu-id="5addd-193">Deploy the application to your Azure subscription.</span></span> <span data-ttu-id="5addd-194">Podrobnosti o tom, jak nasadit aplikaci do Azure najdete v tématu [jak vytvořit a nasadit Cloudovou službu](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span><span class="sxs-lookup"><span data-stu-id="5addd-194">For details on how to deploy an application to Azure, see [How to Create and Deploy a Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span></span>

30. <span data-ttu-id="5addd-195">Počkejte několik minut.</span><span class="sxs-lookup"><span data-stu-id="5addd-195">Wait a few minutes.</span></span> <span data-ttu-id="5addd-196">V **Průzkumníka cloudu**, vyhledejte účet úložiště, který je nakonfigurován výše a najít `WADPerformanceCountersTable` tabulky v ní.</span><span class="sxs-lookup"><span data-stu-id="5addd-196">In **Cloud Explorer**, locate the storage account you configured above and find the `WADPerformanceCountersTable` table in it.</span></span> <span data-ttu-id="5addd-197">Měli byste vidět čítače SignalR v datech tabulky.</span><span class="sxs-lookup"><span data-stu-id="5addd-197">You should see SignalR counters in the table data.</span></span> <span data-ttu-id="5addd-198">Pokud se nezobrazí v tabulce, budete muset znovu zadat svoje přihlašovací údaje služby Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="5addd-198">If you don't see the table, you may need to re-enter your Azure Storage credentials.</span></span> <span data-ttu-id="5addd-199">Budete muset vybrat **aktualizovat** tlačítko naleznete v tabulce v **Průzkumníka cloudu** nebo vyberte **aktualizovat** tlačítka v okně Otevřít tabulku k zobrazení dat v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5addd-199">You may need to select the **Refresh** button to see the table in **Cloud Explorer** or select the **Refresh** button in the open table window to see data in the table.</span></span>

<span data-ttu-id="5addd-200">Speciální k [Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard) pro původní obsah použitý v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="5addd-200">Special thanks to [Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard) for the original content used in this tutorial.</span></span>
