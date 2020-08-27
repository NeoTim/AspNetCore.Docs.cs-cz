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
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Publikování ASP.NET Core webového rozhraní API do Azure API Management pomocí sady Visual Studio

Podle [matného Soucoup](https://twitter.com/codemillmatt)

## <a name="set-up"></a>Nastavení

- Pokud ho nemáte, otevřete [bezplatný účet Azure](https://azure.microsoft.com/free/dotnet/) .
- Pokud jste to ještě neudělali, [vytvořte novou instanci Azure API Management](/azure/api-management/get-started-create-service-instance) .
- [Vytvořte projekt aplikace webového rozhraní API](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>Konfigurace aplikace

Přidání definic Swagger do ASP.NET Core webového rozhraní API umožňuje službě Azure API Management číst definice rozhraní API aplikace. Proveďte následující kroky.

### <a name="add-swagger"></a>Přidat Swagger

1. Přidejte balíček NuGet [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) do projektu ASP.NET Core webového rozhraní API:

    ![Snímek obrazovky, ve kterém se má nakonfigurovat dialogové okno NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. Do metody `Startup.ConfigureServices` přidejte následující řádek:
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. Do metody `Startup.Configure` přidejte následující řádek:

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>Změna směrování rozhraní API

Dále změníte strukturu adres URL potřebnou pro přístup k `Get` akci `WeatherForecastController` . Dokončete následující kroky:

1. Otevřete soubor *WeatherForecastController.cs* .
1. Odstraní `[Route("[controller]")]` atribut na úrovni třídy. Definice třídy bude vypadat následovně:

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. Přidejte `[Route("/")]` k `Get()` akci atribut. Definice funkce bude vypadat následovně:

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Publikování webového rozhraní API pro Azure App Service

K publikování ASP.NET Core webového rozhraní API do Azure API Management proveďte následující kroky:

1. Publikujte aplikaci API a Azure App Service.
1. Přidejte do instance služby Azure API Management prázdné rozhraní API.
1. Publikujte ASP.NET Core webové aplikace API do instance služby Azure API Management.

### <a name="publish-the-api-app-to-azure-app-service"></a>Publikování aplikace API pro Azure App Service

K publikování ASP.NET Core webového rozhraní API do Azure API Management proveďte následující kroky:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte **publikovat**:

    ![Kontextová nabídka otevřená se zvýrazněným odkazem pro publikování](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. V dialogovém okně **publikovat** vyberte **Azure** a klikněte na tlačítko **Další** :

    ![Dialog publikovat](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. Vyberte **Azure App Service (Windows)** a klikněte na tlačítko **Další** :

    ![Dialog publikovat: Vyberte App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. Vyberte **vytvořit novou Azure App Service**.

    ![Dialog publikovat: výběr instance služby Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    Zobrazí se dialogové okno **vytvořit App Service** . Vyplní se pole **název aplikace**, **Skupina prostředků**a položka **plánu App Service** . Tyto názvy můžete zachovat nebo je změnit.
1. Vyberte tlačítko **Vytvořit**.

    ![Dialogové okno Vytvoření služby App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

Po vytvoření se dialogové okno automaticky zavře a dialog **publikovat** se znovu stane aktivním. Vytvořená instance je automaticky vybraná.

![Dialog publikovat: Vyberte instanci App Service.](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

V tuto chvíli potřebujete přidat rozhraní API do služby Azure API Management. Nechte Visual Studio otevřené, zatímco dokončíte následující úkoly.

### <a name="add-an-api-to-azure-api-management"></a>Přidání rozhraní API do Azure API Management

1. Otevřete instanci služby API Management vytvořenou dříve v Azure Portal. Vyberte okno **rozhraní API** :

    ![Okno rozhraní API vybrané z instance služby API Management](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. Z panelu **Přidat nové rozhraní API** vyberte dlaždici **prázdná rozhraní API** :

    ![Obrazovka znázorňující zvýrazněné prázdné dlaždice rozhraní API](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. Do dialogového okna **vytvořit prázdné rozhraní API** zadejte následující hodnoty:    

    - **Zobrazovaný název**: *WeatherForecasts*
    - **Název**: *weatherforecasts*
    - **Přípona adresy URL rozhraní API**: *v1*
    - Pole **Adresa URL webové služby** nechejte prázdné.

1. Vyberte tlačítko **Vytvořit**.

    ![Snímek obrazovky s dokončeným vytvořením prázdného dialogového okna rozhraní API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

Vytvoří se prázdné rozhraní API.

![Snímek obrazovky s prázdným rozhraním API na portálu API Management](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>Publikování webového rozhraní API ASP.NET Core do Azure API Management

1. Přepněte zpět do sady Visual Studio. Dialog **publikovat** by měl být stále otevřený tam, kde jste předtím skončili.
1. Vyberte nově publikovaná Azure App Service, aby byla zvýrazněna.
1. Vyberte tlačítko **Další**.

    ![Snímek obrazovky dialogového okna publikovat se zvolenou službou App Service](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. Dialog teď zobrazuje službu Azure API Management vytvořenou dříve. Rozbalte ho a složku *rozhraní API* , abyste viděli prázdné rozhraní API, které jste vytvořili.
1. Vyberte název prázdného rozhraní API a klikněte na tlačítko **Dokončit** .

    ![Snímek obrazovky nově vytvořeného prázdného rozhraní API služby Azure API Management, které je vybrané v dialogovém okně Publikovat](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. Dialogové okno se zavře a zobrazí se Souhrnná obrazovka s informacemi o publikování. Vyberte tlačítko **Publikovat**.

    ![Snímek obrazovky sady Visual Studio se zobrazeným profilem publikování](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    Webové rozhraní API se bude publikovat jak v Azure App Service, tak i v Azure API Management. Zobrazí se nové okno prohlížeče, ve kterém se zobrazí rozhraní API běžící v Azure App Service. Toto okno můžete zavřít.

1. Přepněte zpátky na instanci služby Azure API Management v Azure Portal.
1. Aktualizujte okno prohlížeče.
1. Vyberte prázdné rozhraní API, které jste vytvořili v předchozích krocích. Teď se naplní a můžete prozkoumat.

    ![Snímek obrazovky s vyplněným rozhraním API v Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>Konfigurace názvu publikovaného rozhraní API

Všimněte si, že název rozhraní API je jiný než ten, který jste si pojmenovali. Publikované rozhraní API má název *WeatherAPI*; ale pojmenovali jste ho *WeatherForecasts* při jeho vytváření. Opravte název provedením následujících kroků:

![Snímek obrazovky vyplněných rozhraní API s různými názvy zvýrazněný](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. Z metody odstraňte následující řádek `Startup.ConfigureServices` :
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. Do metody `Startup.ConfigureServices` přidejte následující kód:
    
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

1. Otevřete nově vytvořený profil publikování. Dá se najít z **Průzkumník řešení** ve složce *Properties/PublishProfiles* .

    ![Snímek obrazovky znázorňující zvýrazněné umístění souboru profilu publikování](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. Změňte `<OpenAPIDocumentName>` hodnotu elementu z `v1` na `WeatherForecasts` .

    ![snímek obrazovky se změnami potřebnými pro publikovaný profil](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. Znovu publikujte ASP.NET Core webové rozhraní API a otevřete instanci Azure API Management v Azure Portal.
1. Aktualizujte stránku v prohlížeči. Uvidíte, že název rozhraní API je teď správný.

    ![Snímek obrazovky dokončeného rozhraní API na portálu](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>Ověření funkčního webového rozhraní API

Nasazené ASP.NET Core webové rozhraní API můžete otestovat v Azure API Management z Azure Portal pomocí následujících kroků:

1. Otevřete kartu **Test**.
1. Vyberte **/** nebo operace **Get** .
1. Vyberte **Poslat**.

    ![Snímek obrazovky portálu před testem](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

Úspěšná odpověď bude vypadat následovně:

![Snímek obrazovky úspěšné odpovědi od API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>Vyčištění

Po dokončení testování aplikace přejdete na [Azure Portal](https://portal.azure.com/) a aplikaci odstraníte.

1. Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste vytvořili.

    ![Azure Portal: skupiny prostředků v nabídce bočního panelu](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. Na stránce **skupiny prostředků** vyberte **Odstranit**.

    ![Azure Portal: stránka skupin prostředků](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. Zadejte název skupiny prostředků a vyberte **Odstranit**. Vaše aplikace a všechny ostatní prostředky vytvořené v tomto kurzu se teď z Azure odstraní.

## <a name="next-steps"></a>Další kroky

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Další zdroje

- [Azure API Management](/azure/api-management/api-management-key-concepts)
- [Azure App Service](/azure/app-service/app-service-web-overview)
