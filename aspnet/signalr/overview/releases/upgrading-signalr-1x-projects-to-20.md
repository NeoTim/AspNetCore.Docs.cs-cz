---
uid: signalr/overview/releases/upgrading-signalr-1x-projects-to-20
title: Upgradování projektů SignalR 1.x na verzi 2 | Dokumentace Microsoftu
author: pfletcher
description: Toto téma popisuje, jak upgradovat existující projekt SignalR 1.x na SignalR 2.x a jak řešit problémy, které mohou nastat během procesu upgradu...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: adcfef99-9bc5-489d-a91b-9b7c2bc35e04
msc.legacyurl: /signalr/overview/releases/upgrading-signalr-1x-projects-to-20
msc.type: authoredcontent
ms.openlocfilehash: 84155a4c171a2ac2149dbbf4237b6561d2814aa0
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "41753027"
---
<a name="upgrading-signalr-1x-projects-to-version-2"></a><span data-ttu-id="c97be-103">Upgradování projektů SignalR 1.x na verzi 2</span><span class="sxs-lookup"><span data-stu-id="c97be-103">Upgrading SignalR 1.x Projects to version 2</span></span>
====================
<span data-ttu-id="c97be-104">podle [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="c97be-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

> <span data-ttu-id="c97be-105">Toto téma popisuje, jak upgradovat existující projekt SignalR 1.x na SignalR 2.x a jak řešit problémy, které mohou nastat během procesu upgradu.</span><span class="sxs-lookup"><span data-stu-id="c97be-105">This topic describes how to upgrade an existing SignalR 1.x project to SignalR 2.x, and how to troubleshoot issues that may arise during the upgrade process.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="c97be-106">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="c97be-106">Software versions used in the tutorial</span></span>
> 
> 
> - [<span data-ttu-id="c97be-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="c97be-107">Visual Studio 2013</span></span>](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - <span data-ttu-id="c97be-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="c97be-108">.NET 4.5</span></span>
> - <span data-ttu-id="c97be-109">Funkce SignalR verze 1 a 2</span><span class="sxs-lookup"><span data-stu-id="c97be-109">SignalR versions 1 and 2</span></span>
>   
> 
> 
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="c97be-110">V tomto kurzu pomocí sady Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="c97be-110">Using Visual Studio 2012 with this tutorial</span></span>
> 
> 
> <span data-ttu-id="c97be-111">Pokud chcete použít Visual Studio 2012 s tímto kurzem, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="c97be-111">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
> 
> - <span data-ttu-id="c97be-112">Aktualizace vašeho [Správce balíčků](http://docs.nuget.org/docs/start-here/installing-nuget) na nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="c97be-112">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="c97be-113">Nainstalujte [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="c97be-113">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="c97be-114">Instalace webové platformy, vyhledejte a nainstalujte **technologie ASP.NET a webové nástroje 2013.1 pro Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="c97be-114">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="c97be-115">Tím se nainstaluje šablony sady Visual Studio pro funkci SignalR třídy jako **centra**.</span><span class="sxs-lookup"><span data-stu-id="c97be-115">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="c97be-116">Některé šablony (jako například **třídy pro spuštění OWIN**) nebudou k dispozici; pro ty, použijte místo toho soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="c97be-116">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
> 
> 
> ## <a name="questions-and-comments"></a><span data-ttu-id="c97be-117">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="c97be-117">Questions and comments</span></span>
> 
> <span data-ttu-id="c97be-118">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="c97be-118">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="c97be-119">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="c97be-119">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="c97be-120">Funkce SignalR 2 nabízí konzistentními možnostmi vývoje napříč platformami server pomocí [OWIN](http://owin.org).</span><span class="sxs-lookup"><span data-stu-id="c97be-120">SignalR 2 offers a consistent development experience across server platforms using [OWIN](http://owin.org).</span></span> <span data-ttu-id="c97be-121">Tento článek popisuje několik kroků, které jsou potřeba k aktualizaci aplikace SignalR 1.x na verzi 2.</span><span class="sxs-lookup"><span data-stu-id="c97be-121">This article describes the few steps that are needed to update a SignalR 1.x application to version 2.</span></span>

<span data-ttu-id="c97be-122">Zatímco je podpořit inovace aplikací SignalR 2, SignalR 1.x, i nadále podporovat.</span><span class="sxs-lookup"><span data-stu-id="c97be-122">While it is encouraged to upgrade applications to SignalR 2, SignalR 1.x will still be supported.</span></span>

<span data-ttu-id="c97be-123">Tento kurz popisuje, jak upgradovat hostované webové aplikace knihovnou SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="c97be-123">This tutorial describes how to upgrade a web-hosted application to SignalR 2.</span></span> <span data-ttu-id="c97be-124">V místním prostředí aplikace (těch, které jsou hostiteli serveru do konzolové aplikace, služby Windows nebo jiné procesy) jsou nyní podporovány v rámci SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="c97be-124">Self-hosted applications (those that host a server in a console application, Windows service, or other process) are now supported under SignalR 2.</span></span> <span data-ttu-id="c97be-125">Informace o tom, jak hned začít vytvářet aplikace v místním prostředí s funkcí SignalR 2 najdete v tématu [kurz: SignalR v místním](../deployment/tutorial-signalr-self-host.md).</span><span class="sxs-lookup"><span data-stu-id="c97be-125">For information on how to get started creating a self-hosted application with SignalR 2, see [Tutorial: SignalR Self-Host](../deployment/tutorial-signalr-self-host.md).</span></span>

## <a name="contents"></a><span data-ttu-id="c97be-126">Obsah</span><span class="sxs-lookup"><span data-stu-id="c97be-126">Contents</span></span>

<span data-ttu-id="c97be-127">Následující části popisují úlohy, které jsou spojené s upgradování projektů SignalR a řešení potíží, které mohou nastat.</span><span class="sxs-lookup"><span data-stu-id="c97be-127">The following sections describe tasks involved with upgrading SignalR projects, and how to troubleshoot issues that may arise.</span></span>

- [<span data-ttu-id="c97be-128">Příklad: Upgrade kurzu Začínáme se SignalR 2</span><span class="sxs-lookup"><span data-stu-id="c97be-128">Example: Upgrading the Getting Started tutorial to SignalR 2</span></span>](#example)
- [<span data-ttu-id="c97be-129">Řešení potíží s chybami během upgradu</span><span class="sxs-lookup"><span data-stu-id="c97be-129">Troubleshooting errors encountered during upgrading</span></span>](#troubleshooting)

<a id="example"></a>

## <a name="example-upgrading-the-getting-started-tutorial-application-to-signalr-2"></a><span data-ttu-id="c97be-130">Příklad: Upgrade Začínáme kurz aplikace knihovnou SignalR 2</span><span class="sxs-lookup"><span data-stu-id="c97be-130">Example: Upgrading the Getting Started tutorial application to SignalR 2</span></span>

<span data-ttu-id="c97be-131">V této části budete aktualizovat aplikace vytvořená v [SignalR 1.x verzi kurzu Začínáme](../older-versions/index.md) použití funkcí SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="c97be-131">In this section, you'll update the application created in the [SignalR 1.x version of the Getting Started Tutorial](../older-versions/index.md) to use SignalR 2.</span></span>

1. <span data-ttu-id="c97be-132">Po dokončení kurzu Začínáme, klikněte pravým tlačítkem na projekt a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="c97be-132">Once you've finished the Getting Started tutorial, right-click on the project, and select **Properties**.</span></span> <span data-ttu-id="c97be-133">Ověřte, že **Cílová architektura** je nastavena na **rozhraní .NET Framework 4.5.**</span><span class="sxs-lookup"><span data-stu-id="c97be-133">Verify that the **Target framework** is set to **.NET Framework 4.5.**</span></span>
2. <span data-ttu-id="c97be-134">Otevřete konzolu Správce balíčků.</span><span class="sxs-lookup"><span data-stu-id="c97be-134">Open the Package Manager Console.</span></span> <span data-ttu-id="c97be-135">Odebrat SignalR 1.x z projektu pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="c97be-135">Remove SignalR 1.x from the project using the following command:</span></span>

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample1.ps1)]
3. <span data-ttu-id="c97be-136">Instalace funkcí SignalR 2 pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="c97be-136">Install SignalR 2 using the following command:</span></span>

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample2.ps1)]
4. <span data-ttu-id="c97be-137">Na stránce HTML aktualizujte odkaz na skript pro funkci SignalR tak, aby odpovídala verzi skriptů nyní zahrnutý v projektu.</span><span class="sxs-lookup"><span data-stu-id="c97be-137">In the HTML page, update the script reference for SignalR to match the version of the script now included in the project.</span></span>

    [!code-html[Main](upgrading-signalr-1x-projects-to-20/samples/sample3.html)]
5. <span data-ttu-id="c97be-138">Ve třídě globální aplikace odeberte volání MapHubs.</span><span class="sxs-lookup"><span data-stu-id="c97be-138">In the global application class, remove the call to MapHubs.</span></span>

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample4.cs)]
6. <span data-ttu-id="c97be-139">Klikněte pravým tlačítkem na řešení a vyberte **přidat**, **novou položku...** . V dialogovém okně vyberte **třídy pro spuštění Owin**.</span><span class="sxs-lookup"><span data-stu-id="c97be-139">Right-click the solution, and select **Add**, **New Item...**. In the dialog, select **Owin Startup Class**.</span></span> <span data-ttu-id="c97be-140">Pojmenujte novou třídu **Startup.cs**.</span><span class="sxs-lookup"><span data-stu-id="c97be-140">Name the new class **Startup.cs**.</span></span>

    ![](upgrading-signalr-1x-projects-to-20/_static/image1.png)
7. <span data-ttu-id="c97be-141">Nahraďte obsah souboru Startup.cs následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c97be-141">Replace the contents of Startup.cs with the following code:</span></span>

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample5.cs)]

    <span data-ttu-id="c97be-142">Atribut sestavení přidá třídu do vaší Owin při spuštění procesu, který provede `Configuration` metoda při spuštění Owin.</span><span class="sxs-lookup"><span data-stu-id="c97be-142">The assembly attribute adds the class to Owin's startup process, which executes the `Configuration` method when Owin starts up.</span></span> <span data-ttu-id="c97be-143">To zase vyžaduje `MapSignalR` metodu, která vytvoří trasy pro všechny rozbočovače SignalR v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c97be-143">This in turn calls the `MapSignalR` method, which creates routes for all SignalR hubs in the application.</span></span>
8. <span data-ttu-id="c97be-144">Spuštění projektu a zkopírujte adresu URL na hlavní stránku do jiného prohlížeče nebo podokně prohlížeče jako předtím.</span><span class="sxs-lookup"><span data-stu-id="c97be-144">Run the project, and copy the URL of the main page into another browser or browser pane, as before.</span></span> <span data-ttu-id="c97be-145">Každá stránka vyzve k zadání uživatelského jména a zprávy odeslané z každé stránky by se zobrazovat v oběma podokny prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c97be-145">Each page will ask for a username, and messages sent from each page should be visible in both browser panes.</span></span>

<a id="troubleshooting"></a>

## <a name="troubleshooting-errors-encountered-during-upgrading"></a><span data-ttu-id="c97be-146">Řešení potíží s chybami během upgradu</span><span class="sxs-lookup"><span data-stu-id="c97be-146">Troubleshooting errors encountered during upgrading</span></span>

<span data-ttu-id="c97be-147">Tato část popisuje problémy, které mohou nastat během upgradu.</span><span class="sxs-lookup"><span data-stu-id="c97be-147">This section describes issues that may arise during upgrading.</span></span> <span data-ttu-id="c97be-148">Úplnější seznam chyb a problémů, kterým může dojít u aplikace SignalR naleznete v tématu [řešení potíží s knihovnou SignalR](../testing-and-debugging/troubleshooting.md).</span><span class="sxs-lookup"><span data-stu-id="c97be-148">For a more comprehensive list of errors and issues that may occur with a SignalR application, see [SignalR Troubleshooting](../testing-and-debugging/troubleshooting.md).</span></span>

### <a name="the-call-is-ambiguous-between-the-following-methods-or-properties"></a><span data-ttu-id="c97be-149">"Volání je nejednoznačné mezi následující metody nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c97be-149">'The call is ambiguous between the following methods or properties'</span></span>

<span data-ttu-id="c97be-150">Tato chyba nastane, pokud odkaz na `Microsoft.AspNet.SignalR.Owin` se neodebere.</span><span class="sxs-lookup"><span data-stu-id="c97be-150">This error will occur if a reference to `Microsoft.AspNet.SignalR.Owin` is not removed.</span></span> <span data-ttu-id="c97be-151">Tento balíček je zastaralý; odkaz musí být odstraněny a verzi 1.x balíčku hostitel ve vlastním procesu musí být odinstalován.</span><span class="sxs-lookup"><span data-stu-id="c97be-151">This package is deprecated; the reference must be removed and the 1.x version of the SelfHost package must be uninstalled.</span></span>

### <a name="hub-methods-fail-silently"></a><span data-ttu-id="c97be-152">Bez upozornění nezdaří metod rozbočovače na</span><span class="sxs-lookup"><span data-stu-id="c97be-152">Hub methods fail silently</span></span>

<span data-ttu-id="c97be-153">Ověřte, zda jsou odkazy na skript v klientovi, a který `OwinStartup` atribut pro třídu pro spuštění má správné třídy a názvy sestavení pro projekt.</span><span class="sxs-lookup"><span data-stu-id="c97be-153">Verify that the script references in your client are up to date, and that the `OwinStartup` attribute for your Startup class has the correct class and assembly names for your project.</span></span> <span data-ttu-id="c97be-154">Také zkuste otevřít adresu rozbočovače (/ signalr/hubs) v prohlížeči. Další informace o co je špatně nabízejí případnou chybu, která se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="c97be-154">Also, try opening up the hubs address (/signalr/hubs) in your browser; any error that appears will offer more information about what's going wrong.</span></span>
