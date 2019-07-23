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
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>Publikování ASP.NET Core SignalR aplikace do Azure App Service

Podle [Brady Gaster](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) je [Microsoft cloud computingu](https://azure.microsoft.com/) služba platformy pro hostování webových aplikací, včetně ASP.NET Core.

> [!NOTE]
> Tento článek se týká publikování aplikace SignalR technologie ASP.NET Core v sadě Visual Studio. Další informace najdete v tématu [služby SignalR pro Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Publikování aplikace

Tento článek se týká publikování pomocí nástrojů v sadě Visual Studio. Visual Studio Code uživatelé můžou používat [rozhraní příkazového řádku Azure](/cli/azure) příkazy k publikování aplikací do Azure. Další informace najdete v tématu [publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet).

1. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.

1. Ujistěte se, že **služby App Service** a **vytvořit nový** jsou vybrány v **vyberte cíl publikování** dialogového okna.

1. Vyberte **vytvořit profil** z **publikovat** tlačítko rozevíracího seznamu.

   Zadejte informace uvedené v následující tabulce v **vytvořit službu App Service** dialogového okna a vyberte **vytvořit**.

   | Položka               | Popis |
   | ------------------ | ----------- |
   | **Název**           | Jedinečný název aplikace. |
   | **Předplatné**   | Předplatné Azure, které aplikace používá. |
   | **Skupina prostředků** | Skupina souvisejících prostředků, do kterých patří aplikace. |
   | **Plán hostování**   | Cenový plán pro webovou aplikaci. |

1. Vyberte **služby Azure SignalR** v **závislosti** > **přidat** rozevíracího seznamu:

   ![Oblast závislosti zobrazuje v rozevíracím seznamu Přidat výběr služby Azure SignalR](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. V **služby Azure SignalR** dialogového okna, vyberte **vytvořit novou instanci služby Azure SignalR**.

1. Zadejte **název**, **skupiny prostředků**, a **umístění**. Vraťte se **služby Azure SignalR** dialogového okna a vyberte **přidat**.

Visual Studio dokončí následující úkoly:

* Vytvoří profil publikování obsahující nastavení publikování.
* Vytvoří *webové aplikace Azure* pomocí zadané podrobnosti.
* Publikuje aplikace.
* Spustí se prohlížeč, který načte webové aplikace.

Formát adresy URL aplikace je `{APP SERVICE NAME}.azurewebsites.net`. Například aplikaci s názvem `SignalRChatApp` má adresu URL `https://signalrchatapp.azurewebsites.net`.

Pokud HTTP *502.2 – Chybná brána* dojde k chybě při nasazování aplikace, která cílí na verzi .NET Core ve verzi preview, najdete v článku [verze preview nasazení ASP.NET Core do služby Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) k jeho vyřešení.

## <a name="configure-the-app-in-azure-app-service"></a>Konfigurace aplikace ve službě Azure App Service

> [!NOTE]
> *Tato část platí jenom pro aplikace bez použití služby Azure SignalR.*
>
> Pokud aplikace využívá služby Azure SignalR, App Service nevyžaduje konfiguraci spřažení požádat o směrování žádostí na aplikace a webové sokety popsané v této části. Klienti se připojí ke službě Azure SignalR, nikoli přímo do aplikace jejich webové sokety.

U aplikací hostovaných bez služby Azure SignalR povolte:

* [Spřažení směrování žádostí na aplikace](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) pro směrování požadavků od uživatele zpět na stejnou instanci služby App Service. Ve výchozím nastavení **na**.
* [Webové sokety](xref:fundamentals/websockets) umožňující webové sokety přenosu do funkce. Ve výchozím nastavení **vypnout**.

1. Na webu Azure Portal, přejděte do webové aplikace v **App Services**.
1. Otevřít **konfigurace** > **obecné nastavení**.
1. Nastavte **webové sokety** k **na**.
1. Ověřte, že **spřažení směrování žádostí na aplikace** je nastavena na **na**.

## <a name="app-service-plan-limits"></a>Omezení plánu služby App Service

Webové sokety a ostatní přenosy jsou omezené podle plánu služby App Service vybrali. Další informace najdete v tématu *Azure Cloud Services omezuje* a *limity služby App Service* oddíly [předplatného Azure a limity, kvóty a omezení](/azure/azure-subscription-service-limits#app-service-limits) článek.

## <a name="additional-resources"></a>Další zdroje

* [Novinky služby Azure SignalR](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet)
* [Hostitelství a nasazení aplikací v ASP.NET Core ve verzi Preview v Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
