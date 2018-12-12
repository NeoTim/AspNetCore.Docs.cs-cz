---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Kurz: Vysokofrekvenční Reálný čas s knihovnou SignalR 2 | Dokumentace Microsoftu'
author: pfletcher
description: Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá knihovnu ASP.NET SignalR k zajištění funkce zasílání zpráv vysokou frekvencí. Vysoká frekvence zasílání zpráv v...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 04ce650509268ee63daafe24bc8dcc9725aea16b
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287712"
---
<a name="tutorial-high-frequency-realtime-with-signalr-2"></a><span data-ttu-id="2a32b-104">Kurz: Vysokofrekvenční Reálný čas s knihovnou SignalR 2</span><span class="sxs-lookup"><span data-stu-id="2a32b-104">Tutorial: High-Frequency Realtime with SignalR 2</span></span>
====================
<span data-ttu-id="2a32b-105">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="2a32b-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="2a32b-106">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="2a32b-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

> <span data-ttu-id="2a32b-107">Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 zasílání zpráv nakonfigurovánu vysokou frekvencí.</span><span class="sxs-lookup"><span data-stu-id="2a32b-107">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span> <span data-ttu-id="2a32b-108">Vysoká frekvence zasílání zpráv v tomto případě znamená, že aktualizace, které se odesílají s pevnou sazbou; v případě této aplikace, až 10 zpráv za sekundu.</span><span class="sxs-lookup"><span data-stu-id="2a32b-108">High-frequency messaging in this case means updates that are sent at a fixed rate; in the case of this application, up to 10 messages a second.</span></span>
>
> <span data-ttu-id="2a32b-109">Aplikace, kterou vytvoříte v tomto kurzu zobrazí obrazec, který můžete přetáhnout uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2a32b-109">The application you'll create in this tutorial displays a shape that users can drag.</span></span> <span data-ttu-id="2a32b-110">Umístění obrazce ve všech propojených prohlížečů pak aktualizuje tak, aby odpovídala pozici Přetahované tvaru použitím vypršel časový limit aktualizace.</span><span class="sxs-lookup"><span data-stu-id="2a32b-110">The position of the shape in all other connected browsers will then be updated to match the position of the dragged shape using timed updates.</span></span>
>
> <span data-ttu-id="2a32b-111">Koncepty představenými v tomto kurzu jste aplikací v reálném čase hry a další aplikace simulace.</span><span class="sxs-lookup"><span data-stu-id="2a32b-111">Concepts introduced in this tutorial have applications in real-time gaming and other simulation applications.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="2a32b-112">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="2a32b-112">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="2a32b-113">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="2a32b-113">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="2a32b-114">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="2a32b-114">.NET 4.5</span></span>
> - <span data-ttu-id="2a32b-115">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="2a32b-115">SignalR version 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="2a32b-116">V tomto kurzu pomocí sady Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="2a32b-116">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="2a32b-117">Pokud chcete použít Visual Studio 2012 s tímto kurzem, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="2a32b-117">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="2a32b-118">Aktualizace vašeho [Správce balíčků](http://docs.nuget.org/docs/start-here/installing-nuget) na nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="2a32b-118">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="2a32b-119">Nainstalujte [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="2a32b-119">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="2a32b-120">Instalace webové platformy, vyhledejte a nainstalujte **technologie ASP.NET a webové nástroje 2013.1 pro Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-120">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="2a32b-121">Tím se nainstaluje šablony sady Visual Studio pro funkci SignalR třídy jako **centra**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-121">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="2a32b-122">Některé šablony (jako například **třídy pro spuštění OWIN**) nebudou k dispozici; pro ty, použijte místo toho soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="2a32b-122">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="tutorial-versions"></a><span data-ttu-id="2a32b-123">Kurz verze</span><span class="sxs-lookup"><span data-stu-id="2a32b-123">Tutorial Versions</span></span>
>
> <span data-ttu-id="2a32b-124">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="2a32b-124">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="2a32b-125">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="2a32b-125">Questions and comments</span></span>
>
> <span data-ttu-id="2a32b-126">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="2a32b-126">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="2a32b-127">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="2a32b-127">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="2a32b-128">Přehled</span><span class="sxs-lookup"><span data-stu-id="2a32b-128">Overview</span></span>

<span data-ttu-id="2a32b-129">Tento kurz ukazuje, jak vytvořit aplikaci, která sdílí stav objektu se ostatní prohlížeče v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="2a32b-129">This tutorial demonstrates how to create an application that shares the state of an object with other browsers in real time.</span></span> <span data-ttu-id="2a32b-130">Aplikace, kterou vytvoříme, se nazývá MoveShape.</span><span class="sxs-lookup"><span data-stu-id="2a32b-130">The application we'll create is called MoveShape.</span></span> <span data-ttu-id="2a32b-131">Na stránce MoveShape se zobrazí element HTML Div, který uživatel můžete přetáhnout; Když uživatel přetáhne divu, pošle se na server, který vám potom sdělí, všechny ostatní připojené klienty se aktualizovat umístění obrazce tak, aby odpovídaly jeho nové pozice.</span><span class="sxs-lookup"><span data-stu-id="2a32b-131">The MoveShape page will display an HTML Div element that the user can drag; when the user drags the Div, its new position will be sent to the server, which will then tell all other connected clients to update the shape's position to match.</span></span>

![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

<span data-ttu-id="2a32b-133">Aplikace vytvořené v tomto kurzu je založena na ukázku Damianem Edwardsem.</span><span class="sxs-lookup"><span data-stu-id="2a32b-133">The application created in this tutorial is based on a demo by Damian Edwards.</span></span> <span data-ttu-id="2a32b-134">Můžou zobrazit videa obsahující tuto ukázku [tady](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).</span><span class="sxs-lookup"><span data-stu-id="2a32b-134">A video containing this demo can be seen [here](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).</span></span>

<span data-ttu-id="2a32b-135">Tento kurz se spustí prokazováním zasílání zpráv SignalR z každé události, který se aktivuje přetáhnout na obrazec.</span><span class="sxs-lookup"><span data-stu-id="2a32b-135">The tutorial will start by demonstrating how to send SignalR messages from each event that fires as the shape is dragged.</span></span> <span data-ttu-id="2a32b-136">Každý připojený klient aktualizuje umístění v místní verzi tvar pak pokaždé, když je přijata zpráva.</span><span class="sxs-lookup"><span data-stu-id="2a32b-136">Each connected client will then update the position of the local version of the shape each time a message is received.</span></span>

<span data-ttu-id="2a32b-137">I aplikace bude fungovat s touto metodou, není doporučenou programovací model, protože by existovat žádné horní omezení počtu zpráv, získávání odeslat, aby klienti a server může získat zahlcen zprávy a by dojít ke snížení výkonu .</span><span class="sxs-lookup"><span data-stu-id="2a32b-137">While the application will function using this method, this is not a recommended programming model, since there would be no upper limit to the number of messages getting sent, so the clients and server could get overwhelmed with messages and performance would degrade.</span></span> <span data-ttu-id="2a32b-138">Zobrazené animací na straně klienta by také odděleném jako tvar by okamžitě přesunout jednotlivé metody spíše než přesouvání plynule do každého nového umístění.</span><span class="sxs-lookup"><span data-stu-id="2a32b-138">The displayed animation on the client would also be disjointed, as the shape would be moved instantly by each method, rather than moving smoothly to each new location.</span></span> <span data-ttu-id="2a32b-139">V dalších částech tohoto kurzu vám ukáže, jak vytvořit funkci časovače, který omezí maximální rychlost přenosu, ve kterém jsou zprávy odesílány klienta nebo serveru a postup přesunutí tvar plynule mezi umístěními.</span><span class="sxs-lookup"><span data-stu-id="2a32b-139">Later sections of the tutorial will demonstrate how to create a timer function that restricts the maximum rate at which messages are sent by either the client or server, and how to move the shape smoothly between locations.</span></span> <span data-ttu-id="2a32b-140">Konečná verze aplikace vytvořené v tomto kurzu si můžete stáhnout z [Galerie kódu na](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).</span><span class="sxs-lookup"><span data-stu-id="2a32b-140">The final version of the application created in this tutorial can be downloaded from [Code Gallery](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).</span></span>

<span data-ttu-id="2a32b-141">Tento kurz obsahuje následující části:</span><span class="sxs-lookup"><span data-stu-id="2a32b-141">This tutorial contains the following sections:</span></span>

- [<span data-ttu-id="2a32b-142">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2a32b-142">Prerequisites</span></span>](#prerequisites)
- [<span data-ttu-id="2a32b-143">Vytvořte projekt a přidejte balíček SignalR a JQuery.UI NuGet</span><span class="sxs-lookup"><span data-stu-id="2a32b-143">Create the project and add the SignalR and JQuery.UI NuGet package</span></span>](#createtheproject2013)
- [<span data-ttu-id="2a32b-144">Vytvoření základní aplikace</span><span class="sxs-lookup"><span data-stu-id="2a32b-144">Create the base application</span></span>](#baseapp)
- [<span data-ttu-id="2a32b-145">Od centra při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="2a32b-145">Starting the hub when the application starts</span></span>](#startup2013)
- [<span data-ttu-id="2a32b-146">Přidat cyklus klienta</span><span class="sxs-lookup"><span data-stu-id="2a32b-146">Add the client loop</span></span>](#clientloop)
- [<span data-ttu-id="2a32b-147">Přidat cyklus serveru</span><span class="sxs-lookup"><span data-stu-id="2a32b-147">Add the server loop</span></span>](#serverloop)
- [<span data-ttu-id="2a32b-148">Přidat plynulou animaci na straně klienta</span><span class="sxs-lookup"><span data-stu-id="2a32b-148">Add smooth animation on the client</span></span>](#animation)
- [<span data-ttu-id="2a32b-149">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2a32b-149">Further Steps</span></span>](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="2a32b-150">Požadavky</span><span class="sxs-lookup"><span data-stu-id="2a32b-150">Prerequisites</span></span>

<span data-ttu-id="2a32b-151">Tento kurz vyžaduje Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="2a32b-151">This tutorial requires Visual Studio 2013.</span></span>

<a id="createtheproject2013"></a>

## <a name="create-the-project-and-add-the-signalr-and-jqueryui-nuget-package"></a><span data-ttu-id="2a32b-152">Vytvořte projekt a přidejte balíček SignalR a JQuery.UI NuGet</span><span class="sxs-lookup"><span data-stu-id="2a32b-152">Create the project and add the SignalR and JQuery.UI NuGet package</span></span>

<span data-ttu-id="2a32b-153">V této části vytvoříme projektu v sadě Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="2a32b-153">In this section, we'll create the project in Visual Studio 2013.</span></span>

<span data-ttu-id="2a32b-154">Následující postup slouží k vytvoření prázdná webová aplikace ASP.NET a přidejte knihovny SignalR a jQuery.UI Visual Studio 2013:</span><span class="sxs-lookup"><span data-stu-id="2a32b-154">The following steps use Visual Studio 2013 to create an ASP.NET Empty Web Application and add the SignalR and jQuery.UI libraries:</span></span>

1. <span data-ttu-id="2a32b-155">V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="2a32b-155">In Visual Studio create an ASP.NET Web Application.</span></span>

    ![Vytvořte web](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)
2. <span data-ttu-id="2a32b-157">V **nový projekt ASP.NET** okně, ponechejte tuto položku **prázdný** vybraný a klikněte na tlačítko **vytvořit projekt**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-157">In the **New ASP.NET Project** window, leave **Empty** selected and click **Create Project**.</span></span>

    ![Vytvoření prázdného webu](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
3. <span data-ttu-id="2a32b-159">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Třída rozbočovače SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-159">In **Solution Explorer**, right-click the project, select **Add | SignalR Hub Class (v2)**.</span></span> <span data-ttu-id="2a32b-160">Název třídy **MoveShapeHub.cs** a přidejte ho do projektu.</span><span class="sxs-lookup"><span data-stu-id="2a32b-160">Name the class **MoveShapeHub.cs** and add it to the project.</span></span> <span data-ttu-id="2a32b-161">Tento krok vytvoří **MoveShapeHub** třídy a přidá do projektu sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR.</span><span class="sxs-lookup"><span data-stu-id="2a32b-161">This step creates the **MoveShapeHub** class and adds to the project a set of script files and assembly references that support SignalR.</span></span>

    > [!NOTE]
    > <span data-ttu-id="2a32b-162">Funkce SignalR můžete také přidat do projektu kliknutím **nástroje > Správce balíčků NuGet > Konzola správce balíčků** a spuštění příkazu:</span><span class="sxs-lookup"><span data-stu-id="2a32b-162">You can also add SignalR to a project by clicking **Tools > NuGet Package Manager > Package Manager Console** and running a command:</span></span>

    <span data-ttu-id="2a32b-163">`install-package Microsoft.AspNet.SignalR`.</span><span class="sxs-lookup"><span data-stu-id="2a32b-163">`install-package Microsoft.AspNet.SignalR`.</span></span>

    <span data-ttu-id="2a32b-164">Pokud používáte konzolu pro přidání SignalR, vytvořte třída rozbočovače SignalR jako samostatný krok po přidání SignalR.</span><span class="sxs-lookup"><span data-stu-id="2a32b-164">If you use the console to add SignalR, create the SignalR hub class as a separate step after you add SignalR.</span></span>
4. <span data-ttu-id="2a32b-165">Klikněte na tlačítko **nástroje > Správce balíčků NuGet > Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-165">Click **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="2a32b-166">V okně Správce balíčku spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2a32b-166">In the package manager window, run the following command:</span></span>

    `Install-Package jQuery.UI.Combined`

    <span data-ttu-id="2a32b-167">Tím se nainstaluje knihovny uživatelského rozhraní jQuery, které budete používat pro animaci tvaru.</span><span class="sxs-lookup"><span data-stu-id="2a32b-167">This installs the jQuery UI library, which you'll use to animate the shape.</span></span>
5. <span data-ttu-id="2a32b-168">V **Průzkumníka řešení** rozbalte uzel skripty.</span><span class="sxs-lookup"><span data-stu-id="2a32b-168">In **Solution Explorer** expand the Scripts node.</span></span> <span data-ttu-id="2a32b-169">Knihovny skriptů pro funkci SignalR, jQuery a jQueryUI jsou viditelné v projektu.</span><span class="sxs-lookup"><span data-stu-id="2a32b-169">Script libraries for jQuery, jQueryUI, and SignalR are visible in the project.</span></span>

    ![Odkazy na knihovnu skriptu](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="baseapp"></a>

## <a name="create-the-base-application"></a><span data-ttu-id="2a32b-171">Vytvoření základní aplikace</span><span class="sxs-lookup"><span data-stu-id="2a32b-171">Create the base application</span></span>

<span data-ttu-id="2a32b-172">V této části vytvoříme aplikaci prohlížeče, která odešle umístění obrazce na server během každou událost pohybu myší.</span><span class="sxs-lookup"><span data-stu-id="2a32b-172">In this section, we'll create a browser application that sends the location of the shape to the server during each mouse move event.</span></span> <span data-ttu-id="2a32b-173">Po přijetí, server pak vyšle tyto informace do všech ostatních připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="2a32b-173">The server then broadcasts this information to all other connected clients as it is received.</span></span> <span data-ttu-id="2a32b-174">Jsme budete rozbalit na tuto aplikaci v předchozích částech.</span><span class="sxs-lookup"><span data-stu-id="2a32b-174">We'll expand on this application in later sections.</span></span>

1. <span data-ttu-id="2a32b-175">Pokud jste ještě nevytvořili třídu MoveShapeHub.cs v **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat**, **třídy...** . Název třídy **MoveShapeHub** a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-175">If you haven't already created the MoveShapeHub.cs class, in **Solution Explorer**, right-click on the project and select **Add**, **Class...**. Name the class **MoveShapeHub** and click **Add**.</span></span>
2. <span data-ttu-id="2a32b-176">Nahraďte kód v novém **MoveShapeHub** třídy následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="2a32b-176">Replace the code in the new **MoveShapeHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

    <span data-ttu-id="2a32b-177">`MoveShapeHub` Třídy výše je implementace rozbočovače SignalR.</span><span class="sxs-lookup"><span data-stu-id="2a32b-177">The `MoveShapeHub` class above is an implementation of a SignalR hub.</span></span> <span data-ttu-id="2a32b-178">Stejně jako [Začínáme s knihovnou SignalR](tutorial-getting-started-with-signalr.md) kurzu centra má metodu, která klientům se volat přímo.</span><span class="sxs-lookup"><span data-stu-id="2a32b-178">As in the [Getting Started with SignalR](tutorial-getting-started-with-signalr.md) tutorial, the hub has a method that the clients will call directly.</span></span> <span data-ttu-id="2a32b-179">V tomto případě klient odešle objekt, který obsahuje novou souřadnice X a Y obrazce na server, který pak získá vysílali pro všechny ostatní připojených klientů.</span><span class="sxs-lookup"><span data-stu-id="2a32b-179">In this case, the client will send an object containing the new X and Y coordinates of the shape to the server, which then gets broadcasted to all other connected clients.</span></span> <span data-ttu-id="2a32b-180">Funkce SignalR bude automaticky serializovat tento objekt pomocí formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="2a32b-180">SignalR will automatically serialize this object using JSON.</span></span>

    <span data-ttu-id="2a32b-181">Objekt, který se pošle na klientovi (`ShapeModel`) obsahuje členy k uložení umístění obrazce.</span><span class="sxs-lookup"><span data-stu-id="2a32b-181">The object that will be sent to the client (`ShapeModel`) contains members to store the position of the shape.</span></span> <span data-ttu-id="2a32b-182">Verze objektu na serveru také obsahuje členy, aby mohli sledovat data klientů, které ukládají, tak, aby daného klienta se neodešlou svá vlastní data.</span><span class="sxs-lookup"><span data-stu-id="2a32b-182">The version of the object on the server also contains a member to track which client's data is being stored, so that a given client won't be sent their own data.</span></span> <span data-ttu-id="2a32b-183">Používá tento člen `JsonIgnore` atribut zabránit serializována a může odeslat klientovi.</span><span class="sxs-lookup"><span data-stu-id="2a32b-183">This member uses the `JsonIgnore` attribute to keep it from being serialized and sent to the client.</span></span>

<a id="startup2013"></a>
## <a name="starting-the-hub-when-the-application-starts"></a><span data-ttu-id="2a32b-184">Od centra při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="2a32b-184">Starting the hub when the application starts</span></span>

1. <span data-ttu-id="2a32b-185">V dalším kroku nastavíme mapování k centru při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a32b-185">Next, we'll set up mapping to the hub when the application starts.</span></span> <span data-ttu-id="2a32b-186">V SignalR 2, je to tak, že přidáte třídy pro spuštění OWIN, který bude volat `MapSignalR` při spuštění třídy `Configuration` provedení metody při spuštění OWIN.</span><span class="sxs-lookup"><span data-stu-id="2a32b-186">In SignalR 2, this is done by adding an OWIN startup class, which will call `MapSignalR` when the startup class' `Configuration` method is executed when OWIN starts.</span></span> <span data-ttu-id="2a32b-187">Třída je přidán do vaší OWIN startup zpracovat pomocí `OwinStartup` atribut sestavení.</span><span class="sxs-lookup"><span data-stu-id="2a32b-187">The class is added to OWIN's startup process using the `OwinStartup` assembly attribute.</span></span>

    <span data-ttu-id="2a32b-188">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Třídy pro spuštění OWIN**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-188">In **Solution Explorer**, right-click the project, then click **Add | OWIN Startup Class**.</span></span> <span data-ttu-id="2a32b-189">Název třídy *spuštění* a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-189">Name the class *Startup* and click **OK**.</span></span>
2. <span data-ttu-id="2a32b-190">Změňte obsah souboru Startup.cs následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="2a32b-190">Change the contents of Startup.cs to the following:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

<a id="client"></a>
## <a name="adding-the-client"></a><span data-ttu-id="2a32b-191">Přidání klienta</span><span class="sxs-lookup"><span data-stu-id="2a32b-191">Adding the client</span></span>

1. <span data-ttu-id="2a32b-192">V dalším kroku přidáme klienta.</span><span class="sxs-lookup"><span data-stu-id="2a32b-192">Next, we'll add the client.</span></span> <span data-ttu-id="2a32b-193">V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Nová položka**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-193">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="2a32b-194">V **přidat novou položku** dialogového okna, vyberte **stránku Html**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-194">In the **Add New Item** dialog, select **Html Page**.</span></span> <span data-ttu-id="2a32b-195">Pojmenujte stránku **Default.html** a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-195">Name the page **Default.html** and click **Add**.</span></span>
2. <span data-ttu-id="2a32b-196">V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku, který jste právě vytvořili a klikněte na tlačítko **nastavit jako úvodní stránku**.</span><span class="sxs-lookup"><span data-stu-id="2a32b-196">In **Solution Explorer**, right-click the page you just created and click **Set as Start Page**.</span></span>
3. <span data-ttu-id="2a32b-197">Nahraďte kód výchozí stránku HTML následující fragment kódu.</span><span class="sxs-lookup"><span data-stu-id="2a32b-197">Replace the default code in the HTML page with the following code snippet.</span></span>

    > [!NOTE]
    > <span data-ttu-id="2a32b-198">Ověřte, že skript odkazuje pod shoda balíčky se přidaly do projektu ve složce Scripts.</span><span class="sxs-lookup"><span data-stu-id="2a32b-198">Verify that the script references below match the packages added to your project in the Scripts folder.</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html)]

    <span data-ttu-id="2a32b-199">Výše uvedený kód HTML a JavaScript vytvoří red Div volá tvar, umožňuje přetahování chování tvaru pomocí knihovny jQuery a používá obrazce `drag` události a umístění obrazce odeslat na server.</span><span class="sxs-lookup"><span data-stu-id="2a32b-199">The above HTML and JavaScript code creates a red Div called Shape, enables the shape's dragging behavior using the jQuery library, and uses the shape's `drag` event to send the shape's position to the server.</span></span>
4. <span data-ttu-id="2a32b-200">Stisknutím klávesy F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2a32b-200">Start the application by pressing F5.</span></span> <span data-ttu-id="2a32b-201">Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2a32b-201">Copy the page's URL, and paste it into a second browser window.</span></span> <span data-ttu-id="2a32b-202">Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout.</span><span class="sxs-lookup"><span data-stu-id="2a32b-202">Drag the shape in one of the browser windows; the shape in the other browser window should move.</span></span>

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a><span data-ttu-id="2a32b-204">Přidat cyklus klienta</span><span class="sxs-lookup"><span data-stu-id="2a32b-204">Add the client loop</span></span>

<span data-ttu-id="2a32b-205">Protože odesílání umístění tvar na každou událost pohybu myší vytvořit nepotřebných objemu síťových přenosů, zpráv od klienta je potřeba omezit.</span><span class="sxs-lookup"><span data-stu-id="2a32b-205">Since sending the location of the shape on every mouse move event will create an unneccesary amount of network traffic, the messages from the client need to be throttled.</span></span> <span data-ttu-id="2a32b-206">Použijeme javascript `setInterval` funkce nastavit smyčku, která odešle nové informace o umístění na server s pevnou sazbou.</span><span class="sxs-lookup"><span data-stu-id="2a32b-206">We'll use the javascript `setInterval` function to set up a loop that sends new position information to the server at a fixed rate.</span></span> <span data-ttu-id="2a32b-207">Smyčka je velmi základní reprezentace "herní cyklus" opakovaně volaná funkce, která řídí všechny funkce hru nebo jiných simulace.</span><span class="sxs-lookup"><span data-stu-id="2a32b-207">This loop is a very basic representation of a "game loop", a repeatedly called function that drives all of the functionality of a game or other simulation.</span></span>

1. <span data-ttu-id="2a32b-208">Aktualizace kódu klienta na stránce HTML tak, aby odpovídala následující fragment kódu.</span><span class="sxs-lookup"><span data-stu-id="2a32b-208">Update the client code in the HTML page to match the following code snippet.</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html)]

    <span data-ttu-id="2a32b-209">Výše uvedené aktualizace přidá `updateServerModel` funkce, který je volán na pevnou frekvencí.</span><span class="sxs-lookup"><span data-stu-id="2a32b-209">The above update adds the `updateServerModel` function, which gets called on a fixed frequency.</span></span> <span data-ttu-id="2a32b-210">Tato funkce odesílá umístění dat na server pokaždé, když `moved` příznak určuje, že je nová umístění dat k odeslání.</span><span class="sxs-lookup"><span data-stu-id="2a32b-210">This function sends the position data to the server whenever the `moved` flag indicates that there is new position data to send.</span></span>
2. <span data-ttu-id="2a32b-211">Stisknutím klávesy F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2a32b-211">Start the application by pressing F5.</span></span> <span data-ttu-id="2a32b-212">Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2a32b-212">Copy the page's URL, and paste it into a second browser window.</span></span> <span data-ttu-id="2a32b-213">Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout.</span><span class="sxs-lookup"><span data-stu-id="2a32b-213">Drag the shape in one of the browser windows; the shape in the other browser window should move.</span></span> <span data-ttu-id="2a32b-214">Protože se omezí počet zpráv, které odesílání na server, se nezobrazí animace pohodlné stejně jako v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="2a32b-214">Since the number of messages that get sent to the server will be throttled, the animation will not appear as smooth as in the previous section.</span></span>

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a><span data-ttu-id="2a32b-216">Přidat cyklus serveru</span><span class="sxs-lookup"><span data-stu-id="2a32b-216">Add the server loop</span></span>

<span data-ttu-id="2a32b-217">V aktuální aplikaci zprávy odeslané ze serveru klientovi chodit přímo tak často, jak jejich přijetí.</span><span class="sxs-lookup"><span data-stu-id="2a32b-217">In the current application, messages sent from the server to the client go out as often as they are received.</span></span> <span data-ttu-id="2a32b-218">To představuje podobný problém, jak je vidět na straně klienta; častěji, než jsou potřeba, a připojení může být zahlcenou díky tomu lze odesílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="2a32b-218">This presents a similar problem as was seen on the client; messages can be sent more often than they are needed, and the connection could become flooded as a result.</span></span> <span data-ttu-id="2a32b-219">Tato část popisuje postup aktualizace serveru pro implementaci časovač, který omezuje počet odchozích zpráv.</span><span class="sxs-lookup"><span data-stu-id="2a32b-219">This section describes how to update the server to implement a timer that throttles the rate of the outgoing messages.</span></span>

1. <span data-ttu-id="2a32b-220">Nahraďte obsah `MoveShapeHub.cs` následujícím fragmentem kódu.</span><span class="sxs-lookup"><span data-stu-id="2a32b-220">Replace the contents of `MoveShapeHub.cs` with the following code snippet.</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    <span data-ttu-id="2a32b-221">Výše uvedený kód rozšiřuje klienta k přidání `Broadcaster` třídu, která omezuje odchozí zprávy pomocí `Timer` třídy z rozhraní .NET framework.</span><span class="sxs-lookup"><span data-stu-id="2a32b-221">The above code expands the client to add the `Broadcaster` class, which throttles the outgoing messages using the `Timer` class from the .NET framework.</span></span>

    <span data-ttu-id="2a32b-222">Protože je Centrum samotné přechodné (ho vytvoří pokaždé, když je to potřeba), `Broadcaster` se vytvoří jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="2a32b-222">Since the hub itself is transitory (it is created every time it is needed), the `Broadcaster` will be created as a singleton.</span></span> <span data-ttu-id="2a32b-223">Opožděná inicializace (představíme v rozhraní .NET 4) slouží k jeho vytvoření odložit, dokud není potřeba, zajištění, že první instanci rozbočovače zcela vytvořil před spuštěním časovač.</span><span class="sxs-lookup"><span data-stu-id="2a32b-223">Lazy initialization (introduced in .NET 4) is used to defer its creation until it is needed, ensuring that the first hub instance is completely created before the timer is started.</span></span>

    <span data-ttu-id="2a32b-224">Volání klientů `UpdateShape` funkce je poté přesunut mimo centra `UpdateModel` metodu tak, že je volána již okamžitě pokaždé, když se přijme příchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="2a32b-224">The call to the clients' `UpdateShape` function is then moved out of the hub's `UpdateModel` method, so that it is no longer called immediately whenever incoming messages are received.</span></span> <span data-ttu-id="2a32b-225">Místo toho se pošle zprávy, které mají klienti ve výši 25 volání za sekundu, spravuje `_broadcastLoop` časovač v rámci `Broadcaster` třídy.</span><span class="sxs-lookup"><span data-stu-id="2a32b-225">Instead, the messages to the clients will be sent at a rate of 25 calls per second, managed by the `_broadcastLoop` timer from within the `Broadcaster` class.</span></span>

    <span data-ttu-id="2a32b-226">A konečně, namísto volání metody klienta z centra přímo, `Broadcaster` třídy je potřeba získat odkaz na aktuálně operačního centra (`_hubContext`) pomocí `GlobalHost`.</span><span class="sxs-lookup"><span data-stu-id="2a32b-226">Lastly, instead of calling the client method from the hub directly, the `Broadcaster` class needs to obtain a reference to the currently operating hub (`_hubContext`) using the `GlobalHost`.</span></span>
2. <span data-ttu-id="2a32b-227">Stisknutím klávesy F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2a32b-227">Start the application by pressing F5.</span></span> <span data-ttu-id="2a32b-228">Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2a32b-228">Copy the page's URL, and paste it into a second browser window.</span></span> <span data-ttu-id="2a32b-229">Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout.</span><span class="sxs-lookup"><span data-stu-id="2a32b-229">Drag the shape in one of the browser windows; the shape in the other browser window should move.</span></span> <span data-ttu-id="2a32b-230">Nesmí být vidět rozdíl v prohlížeči z předchozí části, ale se omezí počet zpráv, které odesílání do klienta.</span><span class="sxs-lookup"><span data-stu-id="2a32b-230">There will not be a visible difference in the browser from the previous section, but the number of messages that get sent to the client will be throttled.</span></span>

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a><span data-ttu-id="2a32b-232">Přidat plynulou animaci na straně klienta</span><span class="sxs-lookup"><span data-stu-id="2a32b-232">Add smooth animation on the client</span></span>

<span data-ttu-id="2a32b-233">Aplikace je skoro hotová, ale jsme dokonce vytvářet jeden další vylepšení v pohybu tvar na straně klienta jako je přesunut v reakci na zprávu serveru.</span><span class="sxs-lookup"><span data-stu-id="2a32b-233">The application is almost complete, but we could make one more improvement, in the motion of the shape on the client as it is moved in response to server messages.</span></span> <span data-ttu-id="2a32b-234">Namísto nastavení pozice tvar do nového umístění uvedena v každém serveru, použijeme knihovna uživatelského rozhraní JQuery `animate` funkce přesunout tvar plynule mezi jeho aktuální a nové pozice.</span><span class="sxs-lookup"><span data-stu-id="2a32b-234">Rather than setting the position of the shape to the new location given by the server, we'll use the JQuery UI library's `animate` function to move the shape smoothly between its current and new position.</span></span>

1. <span data-ttu-id="2a32b-235">Také aktualizovat klienta sady `updateShape` metoda vypadat jako následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="2a32b-235">Update the client's `updateShape` method to look like the highlighted code below:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

    <span data-ttu-id="2a32b-236">Výše uvedený kód přesune tvaru z původního umístění do nové Dal serveru v průběhu intervalu animace (v tomto případě 100 milisekund).</span><span class="sxs-lookup"><span data-stu-id="2a32b-236">The above code moves the shape from the old location to the new one given by the server over the course of the animation interval (in this case, 100 milliseconds).</span></span> <span data-ttu-id="2a32b-237">Všechny předchozí animace spuštěna s tvarem není zaškrtnuto, před zahájením nové animace.</span><span class="sxs-lookup"><span data-stu-id="2a32b-237">Any previous animation running on the shape is cleared before the new animation starts.</span></span>
2. <span data-ttu-id="2a32b-238">Stisknutím klávesy F5 spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2a32b-238">Start the application by pressing F5.</span></span> <span data-ttu-id="2a32b-239">Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2a32b-239">Copy the page's URL, and paste it into a second browser window.</span></span> <span data-ttu-id="2a32b-240">Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout.</span><span class="sxs-lookup"><span data-stu-id="2a32b-240">Drag the shape in one of the browser windows; the shape in the other browser window should move.</span></span> <span data-ttu-id="2a32b-241">Pohyb tvaru v druhém okně by se zobrazit méně přehrávat nepravidelně, jako je pohyb interpolovaných přes čas Místo nastavování jednou za příchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="2a32b-241">The movement of the shape in the other window should appear less jerky as its movement is interpolated over time rather than being set once per incoming message.</span></span>

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image8.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a><span data-ttu-id="2a32b-243">Další kroky</span><span class="sxs-lookup"><span data-stu-id="2a32b-243">Further Steps</span></span>

<span data-ttu-id="2a32b-244">V tomto kurzu jste zjistili, jak programovat aplikace SignalR, která odesílá zprávy s vysokou frekvencí mezi klienty a servery.</span><span class="sxs-lookup"><span data-stu-id="2a32b-244">In this tutorial, you've learned how to program a SignalR application that sends high-frequency messages between clients and servers.</span></span> <span data-ttu-id="2a32b-245">Toto paradigma komunikace je užitečné pro vývoj online hry a další simulace, jako například [ShootR hru vytvořili s knihovnou SignalR](https://shootr.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="2a32b-245">This communication paradigm is useful for developing online games and other simulations, such as [the ShootR game created with SignalR](https://shootr.azurewebsites.net/).</span></span>

<span data-ttu-id="2a32b-246">Hotové aplikace vytvořené v tomto kurzu si můžete stáhnout z [Galerie kódu na](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).</span><span class="sxs-lookup"><span data-stu-id="2a32b-246">The complete application created in this tutorial can be downloaded from [Code Gallery](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).</span></span>

<span data-ttu-id="2a32b-247">Další informace o konceptech vývoj SignalR, najdete na následujících webech pro funkci SignalR zdrojový kód a prostředky:</span><span class="sxs-lookup"><span data-stu-id="2a32b-247">To learn more about SignalR development concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="2a32b-248">Projekt SignalR</span><span class="sxs-lookup"><span data-stu-id="2a32b-248">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="2a32b-249">Funkce SignalR Githubu a ukázky</span><span class="sxs-lookup"><span data-stu-id="2a32b-249">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="2a32b-250">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="2a32b-250">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

<span data-ttu-id="2a32b-251">Návod k nasazení aplikace SignalR do Azure najdete v tématu [pomocí SignalR s webovými aplikacemi ve službě Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span><span class="sxs-lookup"><span data-stu-id="2a32b-251">For a walkthrough on how to deploy a SignalR application to Azure, see [Using SignalR with Web Apps in Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span></span> <span data-ttu-id="2a32b-252">Podrobné informace o tom, jak nasadit webový projekt sady Visual Studio na webu Windows Azure naleznete v tématu [vytvoření webové aplikace ASP.NET ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span><span class="sxs-lookup"><span data-stu-id="2a32b-252">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Create an ASP.NET web app in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span></span>
