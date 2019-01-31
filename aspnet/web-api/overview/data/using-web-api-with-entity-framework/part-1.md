---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: Použití rozhraní Web API 2 s Entity Framework 6 | Dokumentace Microsoftu
author: MikeWasson
description: V tomto kurzu se dozvíte, že se se základy vytváření webovou aplikaci s webovým rozhraním API technologie ASP.NET back-endu. Tento kurz používá Entity Framework 6 pro uspořádání dat...
ms.author: riande
ms.date: 01/17/2019
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: 266c808e3525787181038d2de473194989039e02
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236520"
---
<a name="using-web-api-2-with-entity-framework-6"></a><span data-ttu-id="20cfa-104">Použití webového rozhraní API 2 se sadou Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="20cfa-104">Using Web API 2 with Entity Framework 6</span></span>
====================

[<span data-ttu-id="20cfa-105">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="20cfa-105">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

> <span data-ttu-id="20cfa-106">V tomto kurzu se naučíte, že se se základy vytváření webovou aplikaci s webovým rozhraním API technologie ASP.NET back-endu.</span><span class="sxs-lookup"><span data-stu-id="20cfa-106">This tutorial teaches you the basics of creating a web application with an ASP.NET Web API back end.</span></span> <span data-ttu-id="20cfa-107">Tento kurz používá Entity Framework 6 pro datová vrstva a knihovnou Knockout.js pro aplikace JavaScript na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="20cfa-107">The tutorial uses Entity Framework 6 for the data layer, and Knockout.js for the client-side JavaScript application.</span></span> <span data-ttu-id="20cfa-108">Tento kurz také ukazuje, jak nasadit aplikaci do Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="20cfa-108">The tutorial also shows how to deploy the app to Azure App Service Web Apps.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="20cfa-109">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="20cfa-109">Software versions used in the tutorial</span></span>
>
> - <span data-ttu-id="20cfa-110">Web API 2.1</span><span class="sxs-lookup"><span data-stu-id="20cfa-110">Web API 2.1</span></span>
> - <span data-ttu-id="20cfa-111">Visual Studio 2017 (stáhněte si Visual Studio 2017 [tady](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))</span><span class="sxs-lookup"><span data-stu-id="20cfa-111">Visual Studio 2017 (download Visual Studio 2017 [here](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))</span></span>
> - <span data-ttu-id="20cfa-112">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="20cfa-112">Entity Framework 6</span></span>
> - <span data-ttu-id="20cfa-113">.NET 4.7</span><span class="sxs-lookup"><span data-stu-id="20cfa-113">.NET 4.7</span></span>
> - <span data-ttu-id="20cfa-114">[Knockout.js](http://knockoutjs.com/) 3.1</span><span class="sxs-lookup"><span data-stu-id="20cfa-114">[Knockout.js](http://knockoutjs.com/) 3.1</span></span>

<span data-ttu-id="20cfa-115">Tento kurz používá prostředí ASP.NET Web API 2 s Entity Framework 6 a vytvořte webovou aplikaci, která zpracovává back-end databáze.</span><span class="sxs-lookup"><span data-stu-id="20cfa-115">This tutorial uses ASP.NET Web API 2 with Entity Framework 6 to create a web application that manipulates a back-end database.</span></span> <span data-ttu-id="20cfa-116">Zde je snímek obrazovky aplikace, kterou vytvoříte.</span><span class="sxs-lookup"><span data-stu-id="20cfa-116">Here is a screen shot of the application that you will create.</span></span>

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

<span data-ttu-id="20cfa-117">Aplikace používá návrhu jednostránkové aplikace (SPA).</span><span class="sxs-lookup"><span data-stu-id="20cfa-117">The app uses a single-page application (SPA) design.</span></span> <span data-ttu-id="20cfa-118">"Jednostránkovou aplikaci" je obecný termín pro webovou aplikaci, která načte jednu stránku HTML a následně aktualizuje dynamicky, místo načtení nové stránky na stránku.</span><span class="sxs-lookup"><span data-stu-id="20cfa-118">"Single-page application" is the general term for a web application that loads a single HTML page and then updates the page dynamically, instead of loading new pages.</span></span> <span data-ttu-id="20cfa-119">Po načtení počáteční stránky aplikace komunikuje se serverem přes odesílání požadavků AJAX.</span><span class="sxs-lookup"><span data-stu-id="20cfa-119">After the initial page load, the app talks with the server through AJAX requests.</span></span> <span data-ttu-id="20cfa-120">AJAX žádosti vrácená data JSON, který aplikace používá k aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="20cfa-120">The AJAX requests return JSON data, which the app uses to update the UI.</span></span>

<span data-ttu-id="20cfa-121">AJAX není novinka, ale ještě dnes existují architektury JavaScriptu, které usnadňují tvorbu a udržování rozsáhlé sofistikované aplikace SPA.</span><span class="sxs-lookup"><span data-stu-id="20cfa-121">AJAX isn't new, but today there are JavaScript frameworks that make it easier to build and maintain a large sophisticated SPA application.</span></span> <span data-ttu-id="20cfa-122">Tento kurz používá [knihovnou Knockout.js](http://knockoutjs.com/), ale můžete použít libovolnou architekturu JavaScript klienta.</span><span class="sxs-lookup"><span data-stu-id="20cfa-122">This tutorial uses [Knockout.js](http://knockoutjs.com/), but you can use any JavaScript client framework.</span></span>

<span data-ttu-id="20cfa-123">Tady jsou hlavní stavební bloky pro tuto aplikaci:</span><span class="sxs-lookup"><span data-stu-id="20cfa-123">Here are the main building blocks for this app:</span></span>

- <span data-ttu-id="20cfa-124">ASP.NET MVC vytvoří na stránce HTML.</span><span class="sxs-lookup"><span data-stu-id="20cfa-124">ASP.NET MVC creates the HTML page.</span></span>
- <span data-ttu-id="20cfa-125">Rozhraní ASP.NET Web API zpracovává požadavky AJAX a vrací JSON data.</span><span class="sxs-lookup"><span data-stu-id="20cfa-125">ASP.NET Web API handles the AJAX requests and returns JSON data.</span></span>
- <span data-ttu-id="20cfa-126">Rozhraní Knockout.js data vazbu elementů HTML JSON data.</span><span class="sxs-lookup"><span data-stu-id="20cfa-126">Knockout.js data-binds the HTML elements to the JSON data.</span></span>
- <span data-ttu-id="20cfa-127">Entity Framework a k databázi.</span><span class="sxs-lookup"><span data-stu-id="20cfa-127">Entity Framework talks to the database.</span></span>

## <a name="see-this-app-running-on-azure"></a><span data-ttu-id="20cfa-128">Zobrazit tuto aplikaci běžící v Azure</span><span class="sxs-lookup"><span data-stu-id="20cfa-128">See this app running on Azure</span></span>

<span data-ttu-id="20cfa-129">Chcete zobrazit dokončené web spuštěný jako živou webovou aplikaci?</span><span class="sxs-lookup"><span data-stu-id="20cfa-129">Would you like to see the finished site running as a live web app?</span></span> <span data-ttu-id="20cfa-130">Kompletní verze aplikace můžete nasadit ke svému účtu Azure pomocí následujícího tlačítka.</span><span class="sxs-lookup"><span data-stu-id="20cfa-130">You can deploy a complete version of the app to your Azure account by selecting the following button.</span></span>

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

<span data-ttu-id="20cfa-131">Potřebujete účet Azure k nasazení tohoto řešení do Azure.</span><span class="sxs-lookup"><span data-stu-id="20cfa-131">You need an Azure account to deploy this solution to Azure.</span></span> <span data-ttu-id="20cfa-132">Pokud ještě nemáte účet, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="20cfa-132">If you do not already have an account, you have the following options:</span></span>

- <span data-ttu-id="20cfa-133">[Zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.</span><span class="sxs-lookup"><span data-stu-id="20cfa-133">[Open an Azure account for free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services.</span></span>
- <span data-ttu-id="20cfa-134">[Aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.</span><span class="sxs-lookup"><span data-stu-id="20cfa-134">[Activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="20cfa-135">Vytvoření projektu</span><span class="sxs-lookup"><span data-stu-id="20cfa-135">Create the project</span></span>

<span data-ttu-id="20cfa-136">Otevřít Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="20cfa-136">Open Visual Studio.</span></span> <span data-ttu-id="20cfa-137">Z **souboru** nabídce vyberte možnost **nový**a pak vyberte **projektu**.</span><span class="sxs-lookup"><span data-stu-id="20cfa-137">From the **File** menu, select **New**, then select **Project**.</span></span> <span data-ttu-id="20cfa-138">(Nebo vyberte **nový projekt** na úvodní stránce.)</span><span class="sxs-lookup"><span data-stu-id="20cfa-138">(Or select **New Project** on the Start page.)</span></span>

<span data-ttu-id="20cfa-139">V **nový projekt** dialogového okna, vyberte **webové** v levém podokně a **webová aplikace ASP.NET (.NET Framework)** v prostředním podokně.</span><span class="sxs-lookup"><span data-stu-id="20cfa-139">In the **New Project** dialog, select **Web** in the left pane and **ASP.NET Web Application (.NET Framework)** in the middle pane.</span></span> <span data-ttu-id="20cfa-140">Pojmenujte projekt **BookService** a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="20cfa-140">Name the project **BookService** and select **OK**.</span></span>

[![](part-1/_static/image11.png)](part-1/_static/image11.png)

<span data-ttu-id="20cfa-141">V **nový projekt ASP.NET** dialogového okna, vyberte **webového rozhraní API** šablony.</span><span class="sxs-lookup"><span data-stu-id="20cfa-141">In the **New ASP.NET Project** dialog, select the **Web API** template.</span></span>

[![](part-1/_static/image12.png)](part-1/_static/image12.png)


<span data-ttu-id="20cfa-142">Vyberte **OK** pro vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="20cfa-142">Select **OK** to create the project.</span></span>

## <a name="configure-azure-settings-optional"></a><span data-ttu-id="20cfa-143">Konfigurace nastavení služby Azure (volitelné)</span><span class="sxs-lookup"><span data-stu-id="20cfa-143">Configure Azure settings (optional)</span></span>

<span data-ttu-id="20cfa-144">Po vytvoření projektu můžete nasadit do Azure App Service Web Apps v každém okamžiku.</span><span class="sxs-lookup"><span data-stu-id="20cfa-144">After you create the project, you can choose to deploy to Azure App Service Web Apps at any time.</span></span> 

1. <span data-ttu-id="20cfa-145">V Průzkumníku řešení klikněte pravým tlačítkem na projekt a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="20cfa-145">In Solution Explorer, right-click on your project and select **Publish**.</span></span>

2. <span data-ttu-id="20cfa-146">V zobrazeném okně vyberte **Start**.</span><span class="sxs-lookup"><span data-stu-id="20cfa-146">In the window that appears, select **Start**.</span></span> <span data-ttu-id="20cfa-147">**Vyberte cíl publikování** zobrazí se dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="20cfa-147">The **Pick a publish target** dialog box appears.</span></span>

   [![](part-1/_static/image14.png)](part-1/_static/image14.png)

3. <span data-ttu-id="20cfa-148">Vyberte **vytvořit profil**.</span><span class="sxs-lookup"><span data-stu-id="20cfa-148">Select **Create Profile**.</span></span> <span data-ttu-id="20cfa-149">**Vytvořit službu App Service** zobrazí se dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="20cfa-149">The **Create App Service** dialog box appears.</span></span>

   [![](part-1/_static/image15.png)](part-1/_static/image15.png)

   <span data-ttu-id="20cfa-150">Přijměte výchozí hodnoty, nebo zadat jiné hodnoty pro název aplikace, skupiny prostředků, hostování plán předplatného Azure a geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="20cfa-150">Accept the defaults, or enter different values for the application name, resource group, hosting plan, Azure subscription, and geographical region.</span></span> 

4. <span data-ttu-id="20cfa-151">Vyberte **vytvoření databáze SQL**.</span><span class="sxs-lookup"><span data-stu-id="20cfa-151">Select **Create a SQL database**.</span></span> <span data-ttu-id="20cfa-152">**Nakonfigurujte systém SQL Server** zobrazí se dialogové okno.</span><span class="sxs-lookup"><span data-stu-id="20cfa-152">The **Configure SQL Server** dialog box appears.</span></span> 

   [![](part-1/_static/image16.png)](part-1/_static/image16.png)

   <span data-ttu-id="20cfa-153">Přijměte výchozí hodnoty nebo zadat jiné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="20cfa-153">Accept the defaults or enter different values.</span></span> <span data-ttu-id="20cfa-154">Zadejte **uživatelské jméno správce** a **heslo správce** pro novou databázi.</span><span class="sxs-lookup"><span data-stu-id="20cfa-154">Enter an **Adminstrator Username** and **Administrator Password** for your new database.</span></span> <span data-ttu-id="20cfa-155">Vyberte **OK** po dokončení.</span><span class="sxs-lookup"><span data-stu-id="20cfa-155">Select **OK** when you're done.</span></span> <span data-ttu-id="20cfa-156">**Vytvořit službu App Service** se znovu zobrazí stránka.</span><span class="sxs-lookup"><span data-stu-id="20cfa-156">The **Create App Service** page reappears.</span></span>

5. <span data-ttu-id="20cfa-157">Vyberte **vytvořit** při vytváření profilu.</span><span class="sxs-lookup"><span data-stu-id="20cfa-157">Select **Create** to create your profile.</span></span> <span data-ttu-id="20cfa-158">Zpráva se zobrazí v pravém dolním rohu, která udává, že nasazování probíhá.</span><span class="sxs-lookup"><span data-stu-id="20cfa-158">A message appears in the lower-right corner indicating that deployment is in progress.</span></span> <span data-ttu-id="20cfa-159">Po nějakou dobu **publikovat** se znovu zobrazí okno.</span><span class="sxs-lookup"><span data-stu-id="20cfa-159">After a short while, the **Publish** window reappears.</span></span>

    [![](part-1/_static/image17.png)](part-1/_static/image17.png)
   
    <span data-ttu-id="20cfa-160">Profil, který jste vytvořili k nasazení aplikace je nyní k dispozici.</span><span class="sxs-lookup"><span data-stu-id="20cfa-160">The profile you created to deploy the app is now available.</span></span> 


> [!div class="step-by-step"]
> [<span data-ttu-id="20cfa-161">Next</span><span class="sxs-lookup"><span data-stu-id="20cfa-161">Next</span></span>](part-2.md)
