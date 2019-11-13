---
title: Publikování SignalR aplikace ASP.NET Core do Azure App Service
author: bradygaster
description: Naučte se publikovat ASP.NET Core SignalR aplikaci do Azure App Service.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d03a007ca883b3d0391b848e3e92c90469ee640a
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963923"
---
# <a name="publish-an-aspnet-core-opno-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="83cee-103">Publikování SignalR aplikace ASP.NET Core do Azure App Service</span><span class="sxs-lookup"><span data-stu-id="83cee-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="83cee-104">Od [Brady gastera](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="83cee-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="83cee-105">[Azure App Service](/azure/app-service/app-service-web-overview) je platforma [cloud computingu od Microsoftu](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83cee-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="83cee-106">Tento článek popisuje publikování ASP.NET Core SignalR aplikace ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="83cee-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="83cee-107">Další informace najdete v tématu [SignalR Service pro Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="83cee-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="83cee-108">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="83cee-108">Publish the app</span></span>

<span data-ttu-id="83cee-109">Tento článek popisuje publikování pomocí nástrojů v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="83cee-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="83cee-110">Visual Studio Code uživatelé můžou k publikování aplikací do Azure použít příkazy rozhraní příkazového [řádku Azure](/cli/azure) .</span><span class="sxs-lookup"><span data-stu-id="83cee-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="83cee-111">Další informace najdete v tématu [publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="83cee-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="83cee-112">V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="83cee-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="83cee-113">Potvrďte, že je vybrána možnost **App Service** a **vytvořit novou** v dialogovém okně **vybrat cíl publikování** .</span><span class="sxs-lookup"><span data-stu-id="83cee-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="83cee-114">V rozevíracím seznamu tlačítko pro **publikování** vyberte **vytvořit profil** .</span><span class="sxs-lookup"><span data-stu-id="83cee-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="83cee-115">Zadejte informace popsané v následující tabulce v dialogovém okně **vytvořit App Service** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="83cee-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="83cee-116">Položkami</span><span class="sxs-lookup"><span data-stu-id="83cee-116">Item</span></span>               | <span data-ttu-id="83cee-117">Popis</span><span class="sxs-lookup"><span data-stu-id="83cee-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="83cee-118">**Jméno**</span><span class="sxs-lookup"><span data-stu-id="83cee-118">**Name**</span></span>           | <span data-ttu-id="83cee-119">Jedinečný název aplikace</span><span class="sxs-lookup"><span data-stu-id="83cee-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="83cee-120">**Formě**</span><span class="sxs-lookup"><span data-stu-id="83cee-120">**Subscription**</span></span>   | <span data-ttu-id="83cee-121">Předplatné Azure, které aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="83cee-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="83cee-122">**Skupina prostředků**</span><span class="sxs-lookup"><span data-stu-id="83cee-122">**Resource Group**</span></span> | <span data-ttu-id="83cee-123">Skupina souvisejících prostředků, ke kterým aplikace patří.</span><span class="sxs-lookup"><span data-stu-id="83cee-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="83cee-124">**Plán hostování**</span><span class="sxs-lookup"><span data-stu-id="83cee-124">**Hosting Plan**</span></span>   | <span data-ttu-id="83cee-125">Cenový tarif webové aplikace</span><span class="sxs-lookup"><span data-stu-id="83cee-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="83cee-126">Vyberte **službu Azure SignalR** v rozevíracím seznamu > **Přidat** seznam **závislostí** :</span><span class="sxs-lookup"><span data-stu-id="83cee-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Oblast závislosti zobrazující výběr Azure [! Evřít. Služba NO-LOC (Signal)] v rozevíracím seznamu přidat](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="83cee-128">V dialogovém okně **Azure SignalR Service** vyberte **vytvořit novou instanci služby Azure SignalR** .</span><span class="sxs-lookup"><span data-stu-id="83cee-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="83cee-129">Zadejte **název**, **skupinu prostředků**a **umístění**.</span><span class="sxs-lookup"><span data-stu-id="83cee-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="83cee-130">Vraťte se do dialogového okna **Azure SignalR Service** a vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="83cee-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="83cee-131">Visual Studio dokončí následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="83cee-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="83cee-132">Vytvoří profil publikování obsahující nastavení publikování.</span><span class="sxs-lookup"><span data-stu-id="83cee-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="83cee-133">Vytvoří *webovou aplikaci Azure* s poskytnutými podrobnostmi.</span><span class="sxs-lookup"><span data-stu-id="83cee-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="83cee-134">Publikuje aplikaci.</span><span class="sxs-lookup"><span data-stu-id="83cee-134">Publishes the app.</span></span>
* <span data-ttu-id="83cee-135">Spustí prohlížeč, který načte webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="83cee-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="83cee-136">Formát adresy URL aplikace je `{APP SERVICE NAME}.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="83cee-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="83cee-137">Například aplikace s názvem `SignalRChatApp` má adresu URL `https://signalrchatapp.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="83cee-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="83cee-138">Pokud při nasazování aplikace, která cílí na verzi Preview .NET Core, dojde k chybě HTTP *502,2 – chyba brány* , přečtěte si téma [nasazení ASP.NET Core verze Preview a Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) k jejich vyřešení.</span><span class="sxs-lookup"><span data-stu-id="83cee-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="83cee-139">Konfigurace aplikace v Azure App Service</span><span class="sxs-lookup"><span data-stu-id="83cee-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="83cee-140">*Tato část platí jenom pro aplikace, které nepoužívají službu Azure SignalR.*</span><span class="sxs-lookup"><span data-stu-id="83cee-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="83cee-141">Pokud aplikace používá službu Azure SignalR, App Service nevyžaduje konfiguraci spřažení (ARR) žádostí o aplikace a webové sokety popsané v této části.</span><span class="sxs-lookup"><span data-stu-id="83cee-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="83cee-142">Klienti připojují své webové sokety ke službě Azure SignalR, nikoli přímo do aplikace.</span><span class="sxs-lookup"><span data-stu-id="83cee-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="83cee-143">U aplikací hostovaných bez služby Azure SignalR povolte:</span><span class="sxs-lookup"><span data-stu-id="83cee-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="83cee-144">[Spřažení ARR](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) pro směrování požadavků od uživatele zpět do stejné instance App Service.</span><span class="sxs-lookup"><span data-stu-id="83cee-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="83cee-145">Výchozí nastavení je **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="83cee-145">The default setting is **On**.</span></span>
* <span data-ttu-id="83cee-146">[Webové sokety](xref:fundamentals/websockets) , které umožňují přenos webových soketů do funkce.</span><span class="sxs-lookup"><span data-stu-id="83cee-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="83cee-147">Výchozí nastavení je **vypnuté**.</span><span class="sxs-lookup"><span data-stu-id="83cee-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="83cee-148">V Azure Portal přejděte do webové aplikace v **App Services**.</span><span class="sxs-lookup"><span data-stu-id="83cee-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="83cee-149">Otevřete > konfigurace **Obecné nastavení**.</span><span class="sxs-lookup"><span data-stu-id="83cee-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="83cee-150">Nastavte **webové sokety** na **zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="83cee-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="83cee-151">Ověřte, zda je **spřažení ARR** nastaveno **na hodnotu Zapnuto**.</span><span class="sxs-lookup"><span data-stu-id="83cee-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="83cee-152">Omezení plánu App Service</span><span class="sxs-lookup"><span data-stu-id="83cee-152">App Service Plan limits</span></span>

<span data-ttu-id="83cee-153">Webové sokety a další přenosy jsou omezené na základě vybraného plánu App Service.</span><span class="sxs-lookup"><span data-stu-id="83cee-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="83cee-154">Další informace najdete v částech omezení *App Service* a omezení služby *Azure Cloud Services* v článku omezení pro [předplatné a službu Azure a omezení služby, kvóty a omezení](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="83cee-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83cee-155">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="83cee-155">Additional resources</span></span>

* <span data-ttu-id="83cee-156">[Co je služba Azure SignalR?](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="83cee-156">[What is Azure SignalR Service?](/azure/azure-signalr/signalr-overview)</span></span>
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="83cee-157">Publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="83cee-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="83cee-158">Hostování a nasazení aplikací ASP.NET Core Preview v Azure</span><span class="sxs-lookup"><span data-stu-id="83cee-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
