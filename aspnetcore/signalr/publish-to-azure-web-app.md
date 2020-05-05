---
title: Publikování aplikace ASP.NET Core SignalR pro Azure App Service
author: bradygaster
description: Naučte se publikovat aplikaci ASP.NET Core SignalR pro Azure App Service.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: a5d19c1519c69351605e8da1d8fa70bff784efd4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777186"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>Publikování aplikace ASP.NET Core Signal pro Azure App Service

Od [Brady gastera](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) je platforma [cloud computingu od Microsoftu](https://azure.microsoft.com/) pro hostování webových aplikací, včetně ASP.NET Core.

> [!NOTE]
> Tento článek popisuje publikování aplikace ASP.NET Coreového signálu ze sady Visual Studio. Další informace najdete v tématu [Služba signaler pro Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Publikování aplikace

Tento článek popisuje publikování pomocí nástrojů v aplikaci Visual Studio. Visual Studio Code uživatelé můžou k publikování aplikací do Azure použít příkazy rozhraní příkazového [řádku Azure](/cli/azure) . Další informace najdete v tématu [publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet).

1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **publikovat**.

1. Potvrďte, že je vybrána možnost **App Service** a **vytvořit novou** v dialogovém okně **vybrat cíl publikování** .

1. V rozevíracím seznamu tlačítko pro **publikování** vyberte **vytvořit profil** .

   Zadejte informace popsané v následující tabulce v dialogovém okně **vytvořit App Service** a vyberte **vytvořit**.

   | Položka               | Popis |
   | ------------------ | ----------- |
   | **Název**           | Jedinečný název aplikace |
   | **Předplatné**   | Předplatné Azure, které aplikace používá. |
   | **Skupina prostředků** | Skupina souvisejících prostředků, ke kterým aplikace patří. |
   | **Plán hostování**   | Cenový tarif webové aplikace |

1. V rozevíracím seznamu**Přidat** **závislosti** > vyberte **službu Azure SignalR ** :

   ![Oblast závislosti zobrazující výběr služby Azure SignalR v rozevíracím seznamu přidat](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. V dialogovém **okně SignalR Azure Service** vyberte **vytvořit novou instanci služby Azure SignalR **.

1. Zadejte **název**, **skupinu prostředků**a **umístění**. Vraťte se do dialogového okna ** SignalR Azure Service** a vyberte **Přidat**.

Visual Studio dokončí následující úkoly:

* Vytvoří profil publikování obsahující nastavení publikování.
* Vytvoří *webovou aplikaci Azure* s poskytnutými podrobnostmi.
* Publikuje aplikaci.
* Spustí prohlížeč, který načte webovou aplikaci.

Formát adresy URL aplikace je `{APP SERVICE NAME}.azurewebsites.net`. Například aplikace s názvem `SignalRChatApp` má adresu URL. `https://signalrchatapp.azurewebsites.net`

Pokud při nasazování aplikace, která cílí na verzi Preview .NET Core, dojde k chybě HTTP *502,2 – chyba brány* , přečtěte si téma [nasazení ASP.NET Core verze Preview a Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) k jejich vyřešení.

## <a name="configure-the-app-in-azure-app-service"></a>Konfigurace aplikace v Azure App Service

> [!NOTE]
> *Tato část platí jenom pro aplikace, které nepoužívají SignalR službu Azure.*
>
> Pokud aplikace používá službu Azure SignalR , App Service nevyžaduje konfiguraci spřažení (ARR) žádostí o aplikace a webových soketů popsaných v této části. Klienti připojují své webové sokety ke SignalR službě Azure, nikoli přímo do aplikace.

U aplikací hostovaných bez služby SignalR Azure povolte:

* [Spřažení ARR](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) pro směrování požadavků od uživatele zpět do stejné instance App Service. Výchozí nastavení je **zapnuto**.
* [Webové sokety](xref:fundamentals/websockets) , které umožňují přenos webových soketů do funkce. Výchozí nastavení je **vypnuté**.

1. V Azure Portal přejděte do webové aplikace v **App Services**.
1. Otevřete **Configuration** > **Obecné nastavení**konfigurace.
1. Nastavte **webové sokety** na **zapnuto**.
1. Ověřte, zda je **spřažení ARR** nastaveno **na hodnotu Zapnuto**.

## <a name="app-service-plan-limits"></a>Omezení plánu App Service

Webové sokety a další přenosy jsou omezené na základě vybraného plánu App Service. Další informace najdete v částech omezení *App Service* a omezení služby *Azure Cloud Services* v článku omezení pro [předplatné a službu Azure a omezení služby, kvóty a omezení](/azure/azure-subscription-service-limits#app-service-limits) .

## <a name="additional-resources"></a>Další zdroje

* [Co je služba SignalR Azure?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Publikování aplikace ASP.NET Core do Azure pomocí nástrojů příkazového řádku](/azure/app-service/app-service-web-get-started-dotnet)
* [Hostování a nasazení aplikací ASP.NET Core Preview v Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
