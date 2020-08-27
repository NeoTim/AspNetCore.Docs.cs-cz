---
title: Publikování ASP.NET Core webového rozhraní API do Azure API Management pomocí sady Visual Studio
author: codemillmatt
description: Naučte se publikovat webové rozhraní API ASP.NET Core do Azure API Management pomocí sady Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945664"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="f9f68-103">Publikování ASP.NET Core webového rozhraní API do Azure API Management pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9f68-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="f9f68-104">Podle [matného Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="f9f68-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="f9f68-105">Nastavení</span><span class="sxs-lookup"><span data-stu-id="f9f68-105">Set up</span></span>

- <span data-ttu-id="f9f68-106">Pokud ho nemáte, otevřete [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) .</span><span class="sxs-lookup"><span data-stu-id="f9f68-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="f9f68-107">Pokud jste to ještě neudělali, [vytvořte novou instanci Azure API Management](/azure/api-management/get-started-create-service-instance) .</span><span class="sxs-lookup"><span data-stu-id="f9f68-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="f9f68-108">[Vytvořte projekt aplikace webového rozhraní API](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="f9f68-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="f9f68-109">Konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="f9f68-109">Configure the app</span></span>

<span data-ttu-id="f9f68-110">Přidání definic Swagger do ASP.NET Core webového rozhraní API umožňuje službě Azure API Management číst definice rozhraní API aplikace.</span><span class="sxs-lookup"><span data-stu-id="f9f68-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="f9f68-111">Proveďte následující kroky.</span><span class="sxs-lookup"><span data-stu-id="f9f68-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="f9f68-112">Přidat Swagger</span><span class="sxs-lookup"><span data-stu-id="f9f68-112">Add Swagger</span></span>

1. <span data-ttu-id="f9f68-113">Přidejte balíček NuGet [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) do projektu ASP.NET Core webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="f9f68-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Snímek obrazovky, ve kterém se má nakonfigurovat dialogové okno NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="f9f68-115">Do metody `Startup.ConfigureServices` přidejte následující řádek:</span><span class="sxs-lookup"><span data-stu-id="f9f68-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="f9f68-116">Do metody `Startup.Configure` přidejte následující řádek:</span><span class="sxs-lookup"><span data-stu-id="f9f68-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="f9f68-117">Změna směrování rozhraní API</span><span class="sxs-lookup"><span data-stu-id="f9f68-117">Change the API routing</span></span>

<span data-ttu-id="f9f68-118">Dále změníte strukturu adres URL potřebnou pro přístup k `Get` akci `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="f9f68-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="f9f68-119">Dokončete následující kroky:</span><span class="sxs-lookup"><span data-stu-id="f9f68-119">Complete the following steps:</span></span>

1. <span data-ttu-id="f9f68-120">Otevřete soubor *WeatherForecastController.cs* .</span><span class="sxs-lookup"><span data-stu-id="f9f68-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="f9f68-121">Odstraní `[Route("[controller]")]` atribut na úrovni třídy.</span><span class="sxs-lookup"><span data-stu-id="f9f68-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="f9f68-122">Definice třídy bude vypadat následovně:</span><span class="sxs-lookup"><span data-stu-id="f9f68-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="f9f68-123">Přidejte `[Route("/")]` k `Get()` akci atribut.</span><span class="sxs-lookup"><span data-stu-id="f9f68-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="f9f68-124">Definice funkce bude vypadat následovně:</span><span class="sxs-lookup"><span data-stu-id="f9f68-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="f9f68-125">Publikování webového rozhraní API pro Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f9f68-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="f9f68-126">K publikování ASP.NET Core webového rozhraní API do Azure API Management proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="f9f68-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="f9f68-127">Publikujte aplikaci API a Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f9f68-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="f9f68-128">Přidejte do instance služby Azure API Management prázdné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f9f68-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="f9f68-129">Publikujte ASP.NET Core webové aplikace API do instance služby Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="f9f68-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="f9f68-130">Publikování aplikace API pro Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f9f68-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="f9f68-131">K publikování ASP.NET Core webového rozhraní API do Azure API Management proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="f9f68-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="f9f68-132">V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **publikovat**:</span><span class="sxs-lookup"><span data-stu-id="f9f68-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Kontextová nabídka otevřená se zvýrazněným odkazem pro publikování](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="f9f68-134">V dialogovém okně **publikovat** vyberte **Azure** a klikněte na tlačítko **Další** :</span><span class="sxs-lookup"><span data-stu-id="f9f68-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Dialog publikovat](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="f9f68-136">Vyberte **Azure App Service (Windows)** a klikněte na tlačítko **Další** :</span><span class="sxs-lookup"><span data-stu-id="f9f68-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Dialog publikovat: Vyberte App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="f9f68-138">Vyberte **vytvořit novou Azure App Service**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-138">Select **Create a new Azure App Service**.</span></span>

    ![Dialog publikovat: výběr instance služby Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="f9f68-140">Zobrazí se dialogové okno **vytvořit App Service** .</span><span class="sxs-lookup"><span data-stu-id="f9f68-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="f9f68-141">Vyplní se pole **název aplikace**, **Skupina prostředků**a položka **plánu App Service** .</span><span class="sxs-lookup"><span data-stu-id="f9f68-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="f9f68-142">Tyto názvy můžete zachovat nebo je změnit.</span><span class="sxs-lookup"><span data-stu-id="f9f68-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="f9f68-143">Vyberte tlačítko **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-143">Select the **Create** button.</span></span>

    ![Dialogové okno Vytvoření služby App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="f9f68-145">Po vytvoření se dialogové okno automaticky zavře a dialog **publikovat** se znovu stane aktivním.</span><span class="sxs-lookup"><span data-stu-id="f9f68-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="f9f68-146">Vytvořená instance je automaticky vybraná.</span><span class="sxs-lookup"><span data-stu-id="f9f68-146">The instance that was created is automatically selected.</span></span>

![Dialog publikovat: Vyberte instanci App Service.](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="f9f68-148">V tuto chvíli potřebujete přidat rozhraní API do služby Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="f9f68-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="f9f68-149">Nechte Visual Studio otevřené, zatímco dokončíte následující úkoly.</span><span class="sxs-lookup"><span data-stu-id="f9f68-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="f9f68-150">Přidání rozhraní API do Azure API Management</span><span class="sxs-lookup"><span data-stu-id="f9f68-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="f9f68-151">Otevřete instanci služby API Management vytvořenou dříve v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="f9f68-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="f9f68-152">Vyberte okno **rozhraní API** :</span><span class="sxs-lookup"><span data-stu-id="f9f68-152">Select the **APIs** blade:</span></span>

    ![Okno rozhraní API vybrané z instance služby API Management](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="f9f68-154">Z panelu **Přidat nové rozhraní API** vyberte dlaždici **prázdná rozhraní API** :</span><span class="sxs-lookup"><span data-stu-id="f9f68-154">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

    ![Obrazovka znázorňující zvýrazněné prázdné dlaždice rozhraní API](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="f9f68-156">Do dialogového okna **vytvořit prázdné rozhraní API** zadejte následující hodnoty:</span><span class="sxs-lookup"><span data-stu-id="f9f68-156">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="f9f68-157">**Zobrazovaný název**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="f9f68-157">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="f9f68-158">**Název**: *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="f9f68-158">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="f9f68-159">**Přípona adresy URL rozhraní API**: *v1*</span><span class="sxs-lookup"><span data-stu-id="f9f68-159">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="f9f68-160">Pole **Adresa URL webové služby** nechejte prázdné.</span><span class="sxs-lookup"><span data-stu-id="f9f68-160">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="f9f68-161">Vyberte tlačítko **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-161">Select the **Create** button.</span></span>

    ![Snímek obrazovky s dokončeným vytvořením prázdného dialogového okna rozhraní API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="f9f68-163">Vytvoří se prázdné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f9f68-163">The blank API is created.</span></span>

![Snímek obrazovky s prázdným rozhraním API na portálu API Management](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="f9f68-165">Publikování webového rozhraní API ASP.NET Core do Azure API Management</span><span class="sxs-lookup"><span data-stu-id="f9f68-165">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="f9f68-166">Přepněte zpět do sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f9f68-166">Switch back to Visual Studio.</span></span> <span data-ttu-id="f9f68-167">Dialog **publikovat** by měl být stále otevřený tam, kde jste předtím skončili.</span><span class="sxs-lookup"><span data-stu-id="f9f68-167">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="f9f68-168">Vyberte nově publikovaná Azure App Service, aby byla zvýrazněna.</span><span class="sxs-lookup"><span data-stu-id="f9f68-168">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="f9f68-169">Vyberte tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-169">Select the **Next** button.</span></span>

    ![Snímek obrazovky dialogového okna publikovat se zvolenou službou App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="f9f68-171">Dialog teď zobrazuje službu Azure API Management vytvořenou dříve.</span><span class="sxs-lookup"><span data-stu-id="f9f68-171">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="f9f68-172">Rozbalte ho a složku *rozhraní API* , abyste viděli prázdné rozhraní API, které jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="f9f68-172">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="f9f68-173">Vyberte název prázdného rozhraní API a klikněte na tlačítko **Dokončit** .</span><span class="sxs-lookup"><span data-stu-id="f9f68-173">Select the blank API's name and select the **Finish** button.</span></span>

    ![Snímek obrazovky nově vytvořeného prázdného rozhraní API služby Azure API Management, které je vybrané v dialogovém okně Publikovat](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="f9f68-175">Dialogové okno se zavře a zobrazí se Souhrnná obrazovka s informacemi o publikování.</span><span class="sxs-lookup"><span data-stu-id="f9f68-175">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="f9f68-176">Vyberte tlačítko **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-176">Select the **Publish** button.</span></span>

    ![Snímek obrazovky sady Visual Studio se zobrazeným profilem publikování](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="f9f68-178">Webové rozhraní API se bude publikovat jak v Azure App Service, tak i v Azure API Management.</span><span class="sxs-lookup"><span data-stu-id="f9f68-178">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="f9f68-179">Zobrazí se nové okno prohlížeče, ve kterém se zobrazí rozhraní API běžící v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f9f68-179">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="f9f68-180">Toto okno můžete zavřít.</span><span class="sxs-lookup"><span data-stu-id="f9f68-180">You can close that window.</span></span>

1. <span data-ttu-id="f9f68-181">Přepněte zpátky na instanci služby Azure API Management v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="f9f68-181">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="f9f68-182">Aktualizujte okno prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f9f68-182">Refresh the browser window.</span></span>
1. <span data-ttu-id="f9f68-183">Vyberte prázdné rozhraní API, které jste vytvořili v předchozích krocích.</span><span class="sxs-lookup"><span data-stu-id="f9f68-183">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="f9f68-184">Teď se naplní a můžete prozkoumat.</span><span class="sxs-lookup"><span data-stu-id="f9f68-184">It's now populated and you can explore around.</span></span>

    ![Snímek obrazovky s vyplněným rozhraním API v Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="f9f68-186">Konfigurace názvu publikovaného rozhraní API</span><span class="sxs-lookup"><span data-stu-id="f9f68-186">Configure the published API name</span></span>

<span data-ttu-id="f9f68-187">Všimněte si, že název rozhraní API je jiný než ten, který jste si pojmenovali.</span><span class="sxs-lookup"><span data-stu-id="f9f68-187">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="f9f68-188">Publikované rozhraní API má název *WeatherAPI*; ale pojmenovali jste ho *WeatherForecasts* při jeho vytváření.</span><span class="sxs-lookup"><span data-stu-id="f9f68-188">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="f9f68-189">Opravte název provedením následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="f9f68-189">Complete the following steps to fix the name:</span></span>

![Snímek obrazovky vyplněných rozhraní API s různými názvy zvýrazněný](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="f9f68-191">Z metody odstraňte následující řádek `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f9f68-191">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="f9f68-192">Do metody `Startup.ConfigureServices` přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="f9f68-192">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="f9f68-193">Otevřete nově vytvořený profil publikování.</span><span class="sxs-lookup"><span data-stu-id="f9f68-193">Open the newly created publish profile.</span></span> <span data-ttu-id="f9f68-194">Dá se najít z **Průzkumník řešení** ve složce *Properties/PublishProfiles* .</span><span class="sxs-lookup"><span data-stu-id="f9f68-194">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Snímek obrazovky znázorňující zvýrazněné umístění souboru profilu publikování](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="f9f68-196">Změňte `<OpenAPIDocumentName>` hodnotu elementu z `v1` na `WeatherForecasts` .</span><span class="sxs-lookup"><span data-stu-id="f9f68-196">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![snímek obrazovky se změnami potřebnými pro publikovaný profil](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="f9f68-198">Znovu publikujte ASP.NET Core webové rozhraní API a otevřete instanci Azure API Management v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="f9f68-198">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="f9f68-199">Aktualizujte stránku v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="f9f68-199">Refresh the page in your browser.</span></span> <span data-ttu-id="f9f68-200">Uvidíte, že název rozhraní API je teď správný.</span><span class="sxs-lookup"><span data-stu-id="f9f68-200">You'll see the name of the API is now correct.</span></span>

    ![Snímek obrazovky dokončeného rozhraní API na portálu](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="f9f68-202">Ověření funkčního webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="f9f68-202">Verify the web API is working</span></span>

<span data-ttu-id="f9f68-203">Nasazené ASP.NET Core webové rozhraní API můžete otestovat v Azure API Management z Azure Portal pomocí následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="f9f68-203">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="f9f68-204">Otevřete kartu **Test**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-204">Open the **Test** tab.</span></span>
1. <span data-ttu-id="f9f68-205">Vyberte **/** nebo operace **Get** .</span><span class="sxs-lookup"><span data-stu-id="f9f68-205">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="f9f68-206">Vyberte **Poslat**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-206">Select **Send**.</span></span>

    ![Snímek obrazovky portálu před testem](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="f9f68-208">Úspěšná odpověď bude vypadat následovně:</span><span class="sxs-lookup"><span data-stu-id="f9f68-208">A successful response will look like the following:</span></span>

![Snímek obrazovky úspěšné odpovědi od API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="f9f68-210">Vyčištění</span><span class="sxs-lookup"><span data-stu-id="f9f68-210">Clean up</span></span>

<span data-ttu-id="f9f68-211">Po dokončení testování aplikace přejdete na [Azure Portal](https://portal.azure.com/) a aplikaci odstraníte.</span><span class="sxs-lookup"><span data-stu-id="f9f68-211">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="f9f68-212">Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="f9f68-212">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure Portal: skupiny prostředků v nabídce bočního panelu](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="f9f68-214">Na stránce **skupiny prostředků** vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-214">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure Portal: stránka skupin prostředků](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="f9f68-216">Zadejte název skupiny prostředků a vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="f9f68-216">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="f9f68-217">Vaše aplikace a všechny ostatní prostředky vytvořené v tomto kurzu se teď z Azure odstraní.</span><span class="sxs-lookup"><span data-stu-id="f9f68-217">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f9f68-218">Další kroky</span><span class="sxs-lookup"><span data-stu-id="f9f68-218">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="f9f68-219">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f9f68-219">Additional resources</span></span>

- [<span data-ttu-id="f9f68-220">Azure API Management</span><span class="sxs-lookup"><span data-stu-id="f9f68-220">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="f9f68-221">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f9f68-221">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
