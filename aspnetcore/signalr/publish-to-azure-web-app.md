---
title: Publikování ASP.NET Core SignalR aplikace do webové aplikace Azure
author: bradygaster
description: Publikování ASP.NET Core SignalR aplikace do webové aplikace Azure
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/20/2018
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 1c472711a86edae8dc6e207734aa54e48c02d47d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087687"
---
# <a name="publish-an-aspnet-core-signalr-app-to-an-azure-web-app"></a><span data-ttu-id="cc6cb-103">Publikování ASP.NET Core aplikace SignalR pro webovou aplikaci Azure</span><span class="sxs-lookup"><span data-stu-id="cc6cb-103">Publish an ASP.NET Core SignalR app to an Azure Web App</span></span>

<span data-ttu-id="cc6cb-104">[Služba Azure Web Apps](/azure/app-service/app-service-web-overview) je [Microsoft cloud computingu](https://azure.microsoft.com/) služba platformy pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-104">[Azure Web App](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="cc6cb-105">Tento článek se týká publikování aplikace SignalR technologie ASP.NET Core v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-105">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="cc6cb-106">Navštivte [služby SignalR pro Azure](https://azure.microsoft.com/services/signalr-service) Další informace o použití aplikace SignalR v Azure.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-106">Visit [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service) for more information about using SignalR on Azure.</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="cc6cb-107">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="cc6cb-107">Publish the app</span></span>

<span data-ttu-id="cc6cb-108">Visual Studio obsahuje integrované nástroje pro publikování do webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-108">Visual Studio provides built-in tools for publishing to an Azure Web App.</span></span> <span data-ttu-id="cc6cb-109">Můžete použít Visual Studio Code uživatele [rozhraní příkazového řádku Azure](/cli/azure) příkazy k publikování aplikací do Azure.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-109">Visual Studio Code user can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="cc6cb-110">Tento článek se týká publikování pomocí nástrojů v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-110">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="cc6cb-111">Publikování aplikace pomocí Azure CLI, najdete v článku [publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="cc6cb-111">To publish an app using Azure CLI, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

<span data-ttu-id="cc6cb-112">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span> <span data-ttu-id="cc6cb-113">Ujistěte se, že **vytvořit nový** se změnami **vyberte cíl publikování** dialogového okna a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-113">Confirm that **Create new** is checked in the **Pick a publish target** dialog, and select **Publish**.</span></span>

![Cíl publikování výběru](publish-to-azure-web-app/_static/pick-publish-target-dialog.png)

<span data-ttu-id="cc6cb-115">Zadejte následující informace **vytvořit službu App Service** dialogového okna a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-115">Enter the following information in the **Create App Service** dialog and select **Create**.</span></span>

| <span data-ttu-id="cc6cb-116">Položka</span><span class="sxs-lookup"><span data-stu-id="cc6cb-116">Item</span></span> | <span data-ttu-id="cc6cb-117">Popis</span><span class="sxs-lookup"><span data-stu-id="cc6cb-117">Description</span></span> |
| ---- | ----------- |
| <span data-ttu-id="cc6cb-118">**Název aplikace**</span><span class="sxs-lookup"><span data-stu-id="cc6cb-118">**App name**</span></span> | <span data-ttu-id="cc6cb-119">Jedinečný název aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-119">A unique name of the app.</span></span> |
| <span data-ttu-id="cc6cb-120">**Předplatné**</span><span class="sxs-lookup"><span data-stu-id="cc6cb-120">**Subscription**</span></span> | <span data-ttu-id="cc6cb-121">Předplatné Azure, které aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-121">The Azure subscription that the app uses.</span></span> |
| <span data-ttu-id="cc6cb-122">**Skupina prostředků**</span><span class="sxs-lookup"><span data-stu-id="cc6cb-122">**Resource Group**</span></span> | <span data-ttu-id="cc6cb-123">Skupina souvisejících prostředků, do kterých patří aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-123">The group of related resources to which the app belongs.</span></span>  |
| <span data-ttu-id="cc6cb-124">**Plán hostování**</span><span class="sxs-lookup"><span data-stu-id="cc6cb-124">**Hosting Plan**</span></span> | <span data-ttu-id="cc6cb-125">Cenový plán pro webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-125">The pricing plan for the web app.</span></span> |

![Vytvořit službu app service](publish-to-azure-web-app/_static/create-app-service-dialog.png)

<span data-ttu-id="cc6cb-127">Visual Studio dokončí následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="cc6cb-127">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="cc6cb-128">Vytvoří profil publikování obsahující nastavení publikování.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-128">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="cc6cb-129">Vytvoří nebo použije existující *webové aplikace Azure* pomocí zadané podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-129">Creates or uses an existing *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="cc6cb-130">Publikuje aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-130">Publishes the app.</span></span>
* <span data-ttu-id="cc6cb-131">Spustí prohlížeč, s publikovanou webovou aplikaci načíst.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-131">Launches a browser, with the published web app loaded.</span></span>

<span data-ttu-id="cc6cb-132">Všimněte si, že formát adresy URL pro aplikaci je *{aplikace} .azurewebsites .net*.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-132">Notice the format of the URL for the app is *{app name}.azurewebsites.net*.</span></span> <span data-ttu-id="cc6cb-133">Například aplikaci s názvem `SignalRChattR` má adresu URL, která vypadá jako `https://signalrchattr.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-133">For example, an app named `SignalRChattR` has a URL that looks like `https://signalrchattr.azurewebsites.net`.</span></span>

<span data-ttu-id="cc6cb-134">Pokud dojde k chybě HTTP 502.2, přečtěte si téma [verze preview nasazení ASP.NET Core do služby Azure App Service](xref:host-and-deploy/azure-apps/index) k jeho vyřešení.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-134">If an HTTP 502.2 error occurs, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index) to resolve it.</span></span>

## <a name="configure-signalr-web-app"></a><span data-ttu-id="cc6cb-135">Konfigurace funkce SignalR webové aplikace</span><span class="sxs-lookup"><span data-stu-id="cc6cb-135">Configure SignalR web app</span></span>

<span data-ttu-id="cc6cb-136">ASP.NET Core SignalR apps, které jsou zveřejněné jako webová aplikace Azure musí mít [spřažení směrování žádostí na aplikace](https://en.wikipedia.org/wiki/Application_Request_Routing) povolena.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-136">ASP.NET Core SignalR apps that are published as an Azure Web App must have [ARR Affinity](https://en.wikipedia.org/wiki/Application_Request_Routing) enabled.</span></span> <span data-ttu-id="cc6cb-137">[Protokoly Websocket](xref:fundamentals/websockets) by měla být zapnutá, povolit přenos objekty Websocket na funkci.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-137">[WebSockets](xref:fundamentals/websockets) should be enabled, to allow the WebSockets transport to function.</span></span>

<span data-ttu-id="cc6cb-138">Na webu Azure Portal, přejděte na **nastavení aplikace** pro vaši webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-138">In the Azure portal, navigate to **App Settings** for your web app.</span></span> <span data-ttu-id="cc6cb-139">Nastavte **objekty Websocket** k **na**a ověřte **spřažení směrování žádostí na aplikace** je **na**.</span><span class="sxs-lookup"><span data-stu-id="cc6cb-139">Set **WebSockets** to **On**, and verify **ARR Affinity** is **On**.</span></span>

![Nastavení Azure webové aplikace na webu Azure Portal](publish-to-azure-web-app/_static/azure-web-app-settings.png)

 <span data-ttu-id="cc6cb-141">Protokoly Websocket a ostatní přenosy [jsou omezené podle plánu služby App Service](/azure/azure-subscription-service-limits#app-service-limits).</span><span class="sxs-lookup"><span data-stu-id="cc6cb-141">WebSockets and other transports [are limited based on the App Service Plan](/azure/azure-subscription-service-limits#app-service-limits).</span></span>

## <a name="related-resources"></a><span data-ttu-id="cc6cb-142">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="cc6cb-142">Related resources</span></span>

* [<span data-ttu-id="cc6cb-143">Publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="cc6cb-143">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="cc6cb-144">Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc6cb-144">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>](xref:tutorials/publish-to-azure-webapp-using-vs)
* [<span data-ttu-id="cc6cb-145">Hostitelství a nasazení aplikací v ASP.NET Core ve verzi Preview v Azure</span><span class="sxs-lookup"><span data-stu-id="cc6cb-145">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
