---
uid: web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
title: Použití rozhraní OWIN k samoobslužnému hostování webového rozhraní API ASP.NET | Dokumentace Microsoftu
author: rick-anderson
description: Tento kurz ukazuje, jak hostovat v konzolové aplikaci, používá OWIN k samoobslužnému hostování webového rozhraní API rozhraní ASP.NET Web API. Otevřít Web Interface pro .NET (OWIN) d...
ms.author: riande
ms.date: 07/09/2013
ms.assetid: a90a04ce-9d07-43ad-8250-8a92fb2bd3d5
msc.legacyurl: /web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
msc.type: authoredcontent
ms.openlocfilehash: 59ce24aa47ca590fbe9b617dbbe8bc6b3711849e
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667385"
---
<a name="use-owin-to-self-host-aspnet-web-api"></a><span data-ttu-id="9b81b-104">Použití rozhraní OWIN k samoobslužnému hostování webového rozhraní API ASP.NET</span><span class="sxs-lookup"><span data-stu-id="9b81b-104">Use OWIN to Self-Host ASP.NET Web API</span></span> 
====================

> <span data-ttu-id="9b81b-105">Tento kurz ukazuje, jak hostovat v konzolové aplikaci, používá OWIN k samoobslužnému hostování webového rozhraní API rozhraní ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="9b81b-105">This tutorial shows how to host ASP.NET Web API in a console application, using OWIN to self-host the Web API framework.</span></span>
>
> <span data-ttu-id="9b81b-106">[Otevřete Web Interface pro .NET](http://owin.org) (OWIN) definuje abstrakce mezi .NET webové servery a webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9b81b-106">[Open Web Interface for .NET](http://owin.org) (OWIN) defines an abstraction between .NET web servers and web applications.</span></span> <span data-ttu-id="9b81b-107">OWIN odpojí webové aplikace ze serveru, který je ideální pro webové aplikace ve vašem vlastním procesu mimo službu IIS s vlastním hostováním OWIN.</span><span class="sxs-lookup"><span data-stu-id="9b81b-107">OWIN decouples the web application from the server, which makes OWIN ideal for self-hosting a web application in your own process, outside of IIS.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="9b81b-108">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="9b81b-108">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="9b81b-109">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="9b81b-109">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/) 
> - <span data-ttu-id="9b81b-110">Web API 5.2.7</span><span class="sxs-lookup"><span data-stu-id="9b81b-110">Web API 5.2.7</span></span>


> [!NOTE]
> <span data-ttu-id="9b81b-111">Úplný zdrojový kód můžete najít v tomto kurzu na [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).</span><span class="sxs-lookup"><span data-stu-id="9b81b-111">You can find the complete source code for this tutorial at [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).</span></span>


## <a name="create-a-console-application"></a><span data-ttu-id="9b81b-112">Vytvoření konzolové aplikace</span><span class="sxs-lookup"><span data-stu-id="9b81b-112">Create a console application</span></span>

<span data-ttu-id="9b81b-113">Na **souboru** nabídce **nový**a pak vyberte **projektu**.</span><span class="sxs-lookup"><span data-stu-id="9b81b-113">On the **File** menu,  **New**, then select **Project**.</span></span> <span data-ttu-id="9b81b-114">Z **nainstalováno**v části **Visual C#** vyberte **Windows Desktop** a pak vyberte **Konzolová aplikace (.Net Framework)**.</span><span class="sxs-lookup"><span data-stu-id="9b81b-114">From **Installed**, under **Visual C#**, select **Windows Desktop** and then select **Console App (.Net Framework)**.</span></span> <span data-ttu-id="9b81b-115">Zadejte název projektu "OwinSelfhostSample" a vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="9b81b-115">Name the project "OwinSelfhostSample" and select **OK**.</span></span>

[![](use-owin-to-self-host-web-api/_static/image7.png)](use-owin-to-self-host-web-api/_static/image7.png)

## <a name="add-the-web-api-and-owin-packages"></a><span data-ttu-id="9b81b-116">Přidejte balíčky webového rozhraní API a OWIN</span><span class="sxs-lookup"><span data-stu-id="9b81b-116">Add the Web API and OWIN packages</span></span>

<span data-ttu-id="9b81b-117">Z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="9b81b-117">From the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="9b81b-118">V okně konzoly Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9b81b-118">In the Package Manager Console window, enter the following command:</span></span>

`Install-Package Microsoft.AspNet.WebApi.OwinSelfHost`

<span data-ttu-id="9b81b-119">Tím se nainstaluje balíček selfhost WebAPI OWIN a všechny požadované balíčky OWIN.</span><span class="sxs-lookup"><span data-stu-id="9b81b-119">This will install the WebAPI OWIN selfhost package and all the required OWIN packages.</span></span>

[![](use-owin-to-self-host-web-api/_static/image4.png)](use-owin-to-self-host-web-api/_static/image3.png)

## <a name="configure-web-api-for-self-host"></a><span data-ttu-id="9b81b-120">Konfigurace webového rozhraní API pro hostování na vlastním serveru</span><span class="sxs-lookup"><span data-stu-id="9b81b-120">Configure Web API for self-host</span></span>

<span data-ttu-id="9b81b-121">V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat** / **třídy** přidání nové třídy.</span><span class="sxs-lookup"><span data-stu-id="9b81b-121">In Solution Explorer, right-click the project and select **Add** / **Class** to add a new class.</span></span> <span data-ttu-id="9b81b-122">Název třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="9b81b-122">Name the class `Startup`.</span></span>

![](use-owin-to-self-host-web-api/_static/image5.png)

<span data-ttu-id="9b81b-123">Nahraďte všechny často používaný kód v tomto souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9b81b-123">Replace all of the boilerplate code in this file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample1.cs)]

## <a name="add-a-web-api-controller"></a><span data-ttu-id="9b81b-124">Přidat kontroler Web API</span><span class="sxs-lookup"><span data-stu-id="9b81b-124">Add a Web API controller</span></span>

<span data-ttu-id="9b81b-125">Dále přidejte třídu kontroleru webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9b81b-125">Next, add a Web API controller class.</span></span> <span data-ttu-id="9b81b-126">V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat** / **třídy** přidání nové třídy.</span><span class="sxs-lookup"><span data-stu-id="9b81b-126">In Solution Explorer, right-click the project and select **Add** / **Class** to add a new class.</span></span> <span data-ttu-id="9b81b-127">Název třídy `ValuesController`.</span><span class="sxs-lookup"><span data-stu-id="9b81b-127">Name the class `ValuesController`.</span></span>

<span data-ttu-id="9b81b-128">Nahraďte všechny často používaný kód v tomto souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="9b81b-128">Replace all of the boilerplate code in this file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample2.cs)]

## <a name="start-the-owin-host-and-make-a-request-with-httpclient"></a><span data-ttu-id="9b81b-129">Spuštění hostitele OWIN a poslat žádost s HttpClient</span><span class="sxs-lookup"><span data-stu-id="9b81b-129">Start the OWIN Host and make a request with HttpClient</span></span>

<span data-ttu-id="9b81b-130">Nahraďte všechny často používaný kód v souboru Program.cs následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="9b81b-130">Replace all of the boilerplate code in the Program.cs file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample3.cs)]

## <a name="run-the-application"></a><span data-ttu-id="9b81b-131">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="9b81b-131">Run the application</span></span>

<span data-ttu-id="9b81b-132">Spusťte aplikaci stisknutím klávesy F5 v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b81b-132">To run the application, press F5 in Visual Studio.</span></span> <span data-ttu-id="9b81b-133">Výstup by měl vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="9b81b-133">The output should look like the following:</span></span>

[!code-console[Main](use-owin-to-self-host-web-api/samples/sample4.cmd)]

![](use-owin-to-self-host-web-api/_static/image6.png)

## <a name="additional-resources"></a><span data-ttu-id="9b81b-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9b81b-134">Additional resources</span></span>

[<span data-ttu-id="9b81b-135">Přehled projektu Katana</span><span class="sxs-lookup"><span data-stu-id="9b81b-135">An Overview of Project Katana</span></span>](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md)

[<span data-ttu-id="9b81b-136">Hostování webového rozhraní API ASP.NET v roli pracovního procesu Azure</span><span class="sxs-lookup"><span data-stu-id="9b81b-136">Host ASP.NET Web API in an Azure Worker Role</span></span>](host-aspnet-web-api-in-an-azure-worker-role.md)
