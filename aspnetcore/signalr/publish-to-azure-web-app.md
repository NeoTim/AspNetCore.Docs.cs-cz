---
title: Publikování ASP.NET Core SignalR aplikace do webové aplikace Azure
author: bradygaster
description: Publikování ASP.NET Core SignalR aplikace do webové aplikace Azure
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/20/2018
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 66fa855590c49c4284e4b42cae57f3d4d81dd0fc
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837673"
---
# <a name="publish-an-aspnet-core-signalr-app-to-an-azure-web-app"></a>Publikování ASP.NET Core aplikace SignalR pro webovou aplikaci Azure

[Služba Azure Web Apps](/azure/app-service/app-service-web-overview) je [Microsoft cloud computingu](https://azure.microsoft.com/) služba platformy pro hostování webových aplikací, včetně ASP.NET Core.

> [!NOTE]
> Tento článek se týká publikování aplikace SignalR technologie ASP.NET Core v sadě Visual Studio. Navštivte [služby SignalR pro Azure](https://azure.microsoft.com/en-gb/services/signalr-service?) Další informace o použití aplikace SignalR v Azure.

## <a name="publish-the-app"></a>Publikování aplikace

Visual Studio obsahuje integrované nástroje pro publikování do webové aplikace Azure. Můžete použít Visual Studio Code uživatele [rozhraní příkazového řádku Azure](/cli/azure) příkazy k publikování aplikací do Azure. Tento článek se týká publikování pomocí nástrojů v sadě Visual Studio. Publikování aplikace pomocí Azure CLI, najdete v článku [publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet).

Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**. Ujistěte se, že **vytvořit nový** se změnami **vyberte cíl publikování** dialogového okna a vyberte **publikovat**.

![Cíl publikování výběru](publish-to-azure-web-app/_static/pick-publish-target-dialog.png)

Zadejte následující informace **vytvořit službu App Service** dialogového okna a vyberte **vytvořit**.

| Položka | Popis |
| ---- | ----------- |
| **Název aplikace** | Jedinečný název aplikace. |
| **Předplatné** | Předplatné Azure, které aplikace používá. |
| **Skupina prostředků** | Skupina souvisejících prostředků, do kterých patří aplikace.  |
| **Plán hostování** | Cenový plán pro webovou aplikaci. |

![Vytvořit službu app service](publish-to-azure-web-app/_static/create-app-service-dialog.png)

Visual Studio dokončí následující úkoly:

* Vytvoří profil publikování obsahující nastavení publikování.
* Vytvoří nebo použije existující *webové aplikace Azure* pomocí zadané podrobnosti.
* Publikuje aplikace.
* Spustí prohlížeč, s publikovanou webovou aplikaci načíst.

Všimněte si, že formát adresy URL pro aplikaci je *{aplikace} .azurewebsites .net*. Například aplikaci s názvem `SignalRChattR` má adresu URL, která vypadá jako `https://signalrchattr.azurewebsites.net`.

Pokud dojde k chybě HTTP 502.2, přečtěte si téma [verze preview nasazení ASP.NET Core do služby Azure App Service](xref:host-and-deploy/azure-apps/index) k jeho vyřešení.

## <a name="configure-signalr-web-app"></a>Konfigurace funkce SignalR webové aplikace

ASP.NET Core SignalR apps, které jsou zveřejněné jako webová aplikace Azure musí mít [spřažení směrování žádostí na aplikace](https://en.wikipedia.org/wiki/Application_Request_Routing) povolena. [Protokoly Websocket](xref:fundamentals/websockets) by měla být zapnutá, povolit přenos objekty Websocket na funkci.

Na webu Azure Portal, přejděte na **nastavení aplikace** pro vaši webovou aplikaci. Nastavte **objekty Websocket** k **na**a ověřte **spřažení směrování žádostí na aplikace** je **na**.

![Nastavení Azure webové aplikace na webu Azure Portal](publish-to-azure-web-app/_static/azure-web-app-settings.png)

 Protokoly Websocket a ostatní přenosy [jsou omezené podle plánu služby App Service](/azure/azure-subscription-service-limits#app-service-limits).

## <a name="related-resources"></a>Související prostředky

* [Publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet)
* [Publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs)
* [Hostitelství a nasazení aplikací v ASP.NET Core ve verzi Preview v Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
