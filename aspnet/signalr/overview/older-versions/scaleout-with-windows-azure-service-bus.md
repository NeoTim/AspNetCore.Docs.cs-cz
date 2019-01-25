---
uid: signalr/overview/older-versions/scaleout-with-windows-azure-service-bus
title: Škálování aplikace SignalR službou Azure Service Bus (SignalR 1.x) | Dokumentace Microsoftu
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 501db899-e68c-49ff-81b2-1dc561bfe908
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-windows-azure-service-bus
msc.type: authoredcontent
ms.openlocfilehash: 77186f43b38a8423a1cbd4cf42723c5b9ccdd953
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837907"
---
<a name="signalr-scaleout-with-azure-service-bus-signalr-1x"></a>Škálování aplikace SignalR službou Azure Service Bus (SignalR 1.x)
====================
podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

V tomto kurzu se naučíte se nasadit aplikace SignalR webové Role Windows Azure, pomocí propojovací Service Bus rozhraní k distribuci zpráv do jednotlivých instancí rolí.

![](scaleout-with-windows-azure-service-bus/_static/image1.png)

Požadavky:

- Účet Windows Azure.
- [Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409).
- Visual Studio 2012.

Propojovací service bus rozhraní je také kompatibilní s [sběrnice služby pro Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx), verze 1.1. To však není kompatibilní s verzí 1.0 sběrnice služby pro Windows Server.

## <a name="pricing"></a>Ceny

Propojovací Service Bus rozhraní používá témata pro odesílání zpráv. Nejnovější informace cenách, naleznete v tématu [služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/). V době psaní tohoto návodu můžete odeslat 1 000 000 zpráv za měsíc pro menší než 1 USD. Propojovacího rozhraní pošle zprávu služby Service bus pro každé volání metody rozbočovače SignalR. Existují také některé řídicí zprávy pro připojení, odpojení, spojovacího nebo odejít ze skupiny a tak dále. Ve většině aplikací bude většinu přenos zpráv volání metod rozbočovače.

## <a name="overview"></a>Přehled

Předtím, než získáme podrobný kurz, zde je rychlý přehled toho, co budete dělat.

1. Chcete-li vytvořit nový obor názvů služby Service Bus pomocí portálu Windows Azure.
2. Přidejte tyto balíčky NuGet pro vaši aplikaci: 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.ServiceBus](http://www.nuget.org/packages/SignalR.WindowsAzureServiceBus)
3. Vytvoření aplikace SignalR.
4. Přidejte následující kód do souboru Global.asax konfigurace propojovacího rozhraní: 

    [!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample1.cs)]

Pro každou aplikaci vyberte jinou hodnotu pro "YourAppName". Nepoužívejte stejnou hodnotu napříč více aplikacemi.

## <a name="create-the-azure-services"></a>Vytvoření služby Azure

Vytvoření cloudové služby, jak je popsáno v [jak vytvořit a nasadit Cloudovou službu](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy). Postupujte podle kroků v části "jak: Vytvořit cloudovou službu pomocí metody rychlého vytvoření". Pro účely tohoto kurzu není potřeba nahrát certifikát.

![](scaleout-with-windows-azure-service-bus/_static/image2.png)

Vytvořte nový obor názvů služby Service Bus, jak je popsáno v [způsob použití služby Service Bus témata/předplatná](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions). Postupujte podle kroků v části "Vytvoření služby Namespace".

![](scaleout-with-windows-azure-service-bus/_static/image3.png)

> [!NOTE]
> Je nutné vybrat stejné oblasti pro cloudové služby a obor názvů služby Service Bus.


## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Spusťte Visual Studio. Z **souboru** nabídky, klikněte na tlačítko **nový projekt**.

V **nový projekt** dialogového okna rozbalte **Visual C#**. V části **nainstalované šablony**vyberte **cloudu** a pak vyberte **Windows Azure Cloud Service**. Ponechte výchozí rozhraní .NET Framework 4.5. Pojmenujte aplikaci ChatService a klikněte na tlačítko **OK**.

![](scaleout-with-windows-azure-service-bus/_static/image4.png)

V **nová Cloudová služba Windows Azure** dialogového okna, vyberte webovou roli ASP.NET MVC 4. Klikněte na tlačítko se šipkou vpravo (**&gt;**) Chcete-li přidat roli do řešení.

Najeďte myší na novou roli proto na ikonu tužky viditelné. Kliknutím na tuto ikonu přejmenujte roli. Název role "SignalRChat" a klikněte na **OK**.

![](scaleout-with-windows-azure-service-bus/_static/image5.png)

V **nového projektu ASP.NET MVC 4** průvodce, vyberte **internetovou aplikaci**. Klikněte na **OK**. Průvodce projektem vytvoří dva projekty:

- ChatService: Tento projekt je aplikace Windows Azure. Definuje role Azure a další možnosti konfigurace.
- SignalRChat: Tento projekt není projekt ASP.NET MVC 4.

## <a name="create-the-signalr-chat-application"></a>Vytvoření chatovací SignalR aplikace

Vytvoření chatovací aplikace, postupujte podle kroků v tomto kurzu [Začínáme s knihovnou SignalR a MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md).

Instalace potřebných knihoven pomocí NuGet. Z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**. V **Konzola správce balíčků** okno, zadejte následující příkazy:

[!code-powershell[Main](scaleout-with-windows-azure-service-bus/samples/sample2.ps1)]

Použití `-ProjectName` možnost nainstalovat balíčky do projektu ASP.NET MVC, nikoli projekt Windows Azure.

## <a name="configure-the-backplane"></a>Konfigurace propojovacího rozhraní

V souboru Global.asax vaší aplikace přidejte následující kód:

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample3.cs)]

Teď budete muset získat připojovací řetězec service bus. Na webu Azure Portal vyberte obor názvů služby Service bus, který jste vytvořili a klikněte na ikonu přístupový klíč.

![](scaleout-with-windows-azure-service-bus/_static/image6.png)

Zkopírujte připojovací řetězec do schránky a vložte ho do *connectionString* proměnné.

![](scaleout-with-windows-azure-service-bus/_static/image7.png)

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample4.cs)]

## <a name="deploy-to-azure"></a>Nasazení do Azure

V Průzkumníku řešení rozbalte **role** složky v projektu ChatService.

![](scaleout-with-windows-azure-service-bus/_static/image8.png)

Klikněte pravým tlačítkem na roli SignalRChat a vyberte **vlastnosti**. Vyberte **konfigurace** kartu. V části **instance** vyberte 2. Můžete také nastavit velikost virtuálního počítače **nejmenším instancím**.

![](scaleout-with-windows-azure-service-bus/_static/image9.png)

Uložte změny.

V Průzkumníku řešení klikněte pravým tlačítkem na projekt ChatService. Vyberte **Publikovat**.

![](scaleout-with-windows-azure-service-bus/_static/image10.png)

Pokud je toto vaše první čas publikování na platformě Windows Azure, je nutné stáhnout svoje přihlašovací údaje. V **publikovat** průvodce, klikněte na tlačítko "Přihlásit a stáhnout přihlašovací údaje". To vás vyzve k přihlášení na webu Windows Azure portal a stáhněte si soubor nastavení publikování.

![](scaleout-with-windows-azure-service-bus/_static/image11.png)

Klikněte na tlačítko **Import** a vyberte soubor nastavení publikování, který jste stáhli.

Klikněte na **Další**. V **nastavení publikování** dialogového okna, v části **Cloudovou službu**, vyberte cloudovou službu, kterou jste vytvořili dříve.

![](scaleout-with-windows-azure-service-bus/_static/image12.png)

Klikněte na tlačítko **publikovat**. Může trvat pár minut a nasaďte aplikaci a spustit virtuální počítače.

Nyní při spuštění aplikace chat instance rolí komunikovat přes Azure Service Bus pomocí tématu služby Service Bus. Téma je fronty zpráv, který umožňuje několik předplatitelů.

Propojovacího rozhraní automaticky vytvoří téma a předplatná. Pokud chcete zobrazit předplatná a zprávu aktivity, otevřete na webu Azure portal, vyberte obor názvů služby Service Bus a klikněte na "Tématech".

![](scaleout-with-windows-azure-service-bus/_static/image13.png)

Ujistěte se, trvat několik minut, než se aktivita zpráva se zobrazí na řídicím panelu.

![](scaleout-with-windows-azure-service-bus/_static/image14.png)

Funkce SignalR spravuje životnost tématu. Tak dlouho, dokud je vaše aplikace nasazena, nepokoušejte ručně odstraňte témata nebo změnit nastavení v tomto tématu.
