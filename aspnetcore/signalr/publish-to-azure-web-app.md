---
title: Publikování ASP.NET Core SignalR aplikace do Azure App Service
author: bradygaster
description: Zjistěte, jak publikovat aplikaci funkce SignalR technologie ASP.NET Core do služby Azure App Service.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406103"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="a5807-103">Publikování ASP.NET Core SignalR aplikace do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a5807-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="a5807-104">Podle [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="a5807-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="a5807-105">[Azure App Service](/azure/app-service/app-service-web-overview) je [Microsoft cloud computingu](https://azure.microsoft.com/) služba platformy pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5807-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="a5807-106">Tento článek se týká publikování aplikace SignalR technologie ASP.NET Core v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a5807-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="a5807-107">Další informace najdete v tématu [služby SignalR pro Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="a5807-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="a5807-108">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="a5807-108">Publish the app</span></span>

<span data-ttu-id="a5807-109">Tento článek se týká publikování pomocí nástrojů v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a5807-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="a5807-110">Visual Studio Code uživatelé můžou používat [rozhraní příkazového řádku Azure](/cli/azure) příkazy k publikování aplikací do Azure.</span><span class="sxs-lookup"><span data-stu-id="a5807-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="a5807-111">Další informace najdete v tématu [publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="a5807-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="a5807-112">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="a5807-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="a5807-113">Ujistěte se, že **služby App Service** a **vytvořit nový** jsou vybrány v **vyberte cíl publikování** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="a5807-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="a5807-114">Vyberte **vytvořit profil** z **publikovat** tlačítko rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="a5807-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="a5807-115">Zadejte informace uvedené v následující tabulce v **vytvořit službu App Service** dialogového okna a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="a5807-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="a5807-116">Položka</span><span class="sxs-lookup"><span data-stu-id="a5807-116">Item</span></span>               | <span data-ttu-id="a5807-117">Popis</span><span class="sxs-lookup"><span data-stu-id="a5807-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="a5807-118">**Název**</span><span class="sxs-lookup"><span data-stu-id="a5807-118">**Name**</span></span>           | <span data-ttu-id="a5807-119">Jedinečný název aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5807-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="a5807-120">**Předplatné**</span><span class="sxs-lookup"><span data-stu-id="a5807-120">**Subscription**</span></span>   | <span data-ttu-id="a5807-121">Předplatné Azure, které aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="a5807-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="a5807-122">**Skupina prostředků**</span><span class="sxs-lookup"><span data-stu-id="a5807-122">**Resource Group**</span></span> | <span data-ttu-id="a5807-123">Skupina souvisejících prostředků, do kterých patří aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5807-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="a5807-124">**Plán hostování**</span><span class="sxs-lookup"><span data-stu-id="a5807-124">**Hosting Plan**</span></span>   | <span data-ttu-id="a5807-125">Cenový plán pro webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a5807-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="a5807-126">Vyberte **služby Azure SignalR** v **závislosti** > **přidat** rozevíracího seznamu:</span><span class="sxs-lookup"><span data-stu-id="a5807-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Oblast závislosti zobrazuje v rozevíracím seznamu Přidat výběr služby Azure SignalR](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="a5807-128">V **služby Azure SignalR** dialogového okna, vyberte **vytvořit novou instanci služby Azure SignalR**.</span><span class="sxs-lookup"><span data-stu-id="a5807-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="a5807-129">Zadejte **název**, **skupiny prostředků**, a **umístění**.</span><span class="sxs-lookup"><span data-stu-id="a5807-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="a5807-130">Vraťte se **služby Azure SignalR** dialogového okna a vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="a5807-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="a5807-131">Visual Studio dokončí následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="a5807-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="a5807-132">Vytvoří profil publikování obsahující nastavení publikování.</span><span class="sxs-lookup"><span data-stu-id="a5807-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="a5807-133">Vytvoří *webové aplikace Azure* pomocí zadané podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="a5807-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="a5807-134">Publikuje aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5807-134">Publishes the app.</span></span>
* <span data-ttu-id="a5807-135">Spustí se prohlížeč, který načte webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5807-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="a5807-136">Formát adresy URL aplikace je `{APP SERVICE NAME}.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="a5807-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="a5807-137">Například aplikaci s názvem `SignalRChatApp` má adresu URL `https://signalrchatapp.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="a5807-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="a5807-138">Pokud HTTP *502.2 – Chybná brána* dojde k chybě při nasazování aplikace, která cílí na verzi .NET Core ve verzi preview, najdete v článku [verze preview nasazení ASP.NET Core do služby Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) k jeho vyřešení.</span><span class="sxs-lookup"><span data-stu-id="a5807-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="a5807-139">Konfigurace aplikace ve službě Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a5807-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="a5807-140">*Tato část platí jenom pro aplikace bez použití služby Azure SignalR.*</span><span class="sxs-lookup"><span data-stu-id="a5807-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="a5807-141">Pokud aplikace využívá služby Azure SignalR, App Service nevyžaduje konfiguraci spřažení požádat o směrování žádostí na aplikace a webové sokety popsané v této části.</span><span class="sxs-lookup"><span data-stu-id="a5807-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="a5807-142">Klienti se připojí ke službě Azure SignalR, nikoli přímo do aplikace jejich webové sokety.</span><span class="sxs-lookup"><span data-stu-id="a5807-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="a5807-143">U aplikací hostovaných bez služby Azure SignalR povolte:</span><span class="sxs-lookup"><span data-stu-id="a5807-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="a5807-144">[Spřažení směrování žádostí na aplikace](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) pro směrování požadavků od uživatele zpět na stejnou instanci služby App Service.</span><span class="sxs-lookup"><span data-stu-id="a5807-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="a5807-145">Ve výchozím nastavení **na**.</span><span class="sxs-lookup"><span data-stu-id="a5807-145">The default setting is **On**.</span></span>
* <span data-ttu-id="a5807-146">[Webové sokety](xref:fundamentals/websockets) umožňující webové sokety přenosu do funkce.</span><span class="sxs-lookup"><span data-stu-id="a5807-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="a5807-147">Ve výchozím nastavení **vypnout**.</span><span class="sxs-lookup"><span data-stu-id="a5807-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="a5807-148">Na webu Azure Portal, přejděte do webové aplikace v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="a5807-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="a5807-149">Otevřít **konfigurace** > **obecné nastavení**.</span><span class="sxs-lookup"><span data-stu-id="a5807-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="a5807-150">Nastavte **webové sokety** k **na**.</span><span class="sxs-lookup"><span data-stu-id="a5807-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="a5807-151">Ověřte, že **spřažení směrování žádostí na aplikace** je nastavena na **na**.</span><span class="sxs-lookup"><span data-stu-id="a5807-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="a5807-152">Omezení plánu služby App Service</span><span class="sxs-lookup"><span data-stu-id="a5807-152">App Service Plan limits</span></span>

<span data-ttu-id="a5807-153">Webové sokety a ostatní přenosy jsou omezené podle plánu služby App Service vybrali.</span><span class="sxs-lookup"><span data-stu-id="a5807-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="a5807-154">Další informace najdete v tématu *Azure Cloud Services omezuje* a *limity služby App Service* oddíly [předplatného Azure a limity, kvóty a omezení](/azure/azure-subscription-service-limits#app-service-limits) článek.</span><span class="sxs-lookup"><span data-stu-id="a5807-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5807-155">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a5807-155">Additional resources</span></span>

* [<span data-ttu-id="a5807-156">Novinky služby Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="a5807-156">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="a5807-157">Publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a5807-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="a5807-158">Hostitelství a nasazení aplikací v ASP.NET Core ve verzi Preview v Azure</span><span class="sxs-lookup"><span data-stu-id="a5807-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
