---
uid: signalr/overview/performance/scaleout-with-windows-azure-service-bus
title: Škálování aplikace SignalR službou Azure Service Bus | Dokumentace Microsoftu
author: MikeWasson
description: Verze softwaru použít v této verzi tématu Visual Studio 2013 .NET 4.5 SignalR 2 dřívější verze tohoto tématu pro funkci SignalR 1.x verzi tohoto tématu...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: ce1305f9-30fd-49e3-bf38-d0a78dfb06c3
msc.legacyurl: /signalr/overview/performance/scaleout-with-windows-azure-service-bus
msc.type: authoredcontent
ms.openlocfilehash: 5cdb9b5eb6d3f5ebd5c96e4b0d89926c18bddadd
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287608"
---
<a name="signalr-scaleout-with-azure-service-bus"></a>Škálování aplikace SignalR službou Azure Service Bus
====================
podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

V tomto kurzu se naučíte se nasadit aplikace SignalR webové Role Windows Azure, pomocí propojovací Service Bus rozhraní k distribuci zpráv do jednotlivých instancí rolí. (Můžete také použít propojovací Service Bus rozhraní s [webové aplikace ve službě Azure App Service](https://docs.microsoft.com/azure/app-service-web/).)

![](scaleout-with-windows-azure-service-bus/_static/image1.png)

Požadavky:

- Účet Windows Azure.
- [Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409).
- Visual Studio 2012 nebo 2013.

Propojovací service bus rozhraní je také kompatibilní s [sběrnice služby pro Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx), verze 1.1. To však není kompatibilní s verzí 1.0 sběrnice služby pro Windows Server.

## <a name="pricing"></a>Ceny

Propojovací Service Bus rozhraní používá témata pro odesílání zpráv. Nejnovější informace cenách, naleznete v tématu [služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/). V době psaní tohoto návodu můžete odeslat 1 000 000 zpráv za měsíc pro menší než 1 USD. Propojovacího rozhraní pošle zprávu služby Service bus pro každé volání metody rozbočovače SignalR. Existují také některé řídicí zprávy pro připojení, odpojení, spojovacího nebo odejít ze skupiny a tak dále. Ve většině aplikací bude většinu přenos zpráv volání metod rozbočovače.

## <a name="overview"></a>Přehled

Předtím, než získáme podrobný kurz, zde je rychlý přehled toho, co budete dělat.

1. Chcete-li vytvořit nový obor názvů služby Service Bus pomocí portálu Windows Azure.
2. Přidejte tyto balíčky NuGet pro vaši aplikaci: 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.ServiceBus3](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.ServiceBus3) nebo [Microsoft.AspNet.SignalR.ServiceBus](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.ServiceBus)
3. Vytvoření aplikace SignalR.
4. Přidejte následující kód do souboru Startup.cs konfigurace propojovacího rozhraní: 

    [!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample1.cs)]

Tento kód nastaví propojovacího rozhraní s výchozími hodnotami u [TopicCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.servicebusscaleoutconfiguration.topiccount(v=vs.118).aspx) a [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx). Informace o změně těchto hodnot najdete v tématu [výkon aplikace SignalR: Škálování metriky](signalr-performance.md#scaleout_metrics).

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

V **nová Cloudová služba Windows Azure** dialogového okna, vyberte webovou roli ASP.NET. Klikněte na tlačítko se šipkou vpravo (**&gt;**) Chcete-li přidat roli do řešení.

Najeďte myší na novou roli proto na ikonu tužky viditelné. Kliknutím na tuto ikonu přejmenujte roli. Název role "SignalRChat" a klikněte na **OK**.

![](scaleout-with-windows-azure-service-bus/_static/image5.png)

V **nový projekt ASP.NET** dialogového okna, vyberte **MVC**a klikněte na tlačítko OK.

![](scaleout-with-windows-azure-service-bus/_static/image6.png)

Průvodce projektem vytvoří dva projekty:

- ChatService: Tento projekt je aplikace Windows Azure. Definuje role Azure a další možnosti konfigurace.
- SignalRChat: Tento projekt není projekt ASP.NET MVC 5.

## <a name="create-the-signalr-chat-application"></a>Vytvoření chatovací SignalR aplikace

Vytvoření chatovací aplikace, postupujte podle kroků v tomto kurzu [Začínáme s knihovnou SignalR a MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).

Instalace potřebných knihoven pomocí NuGet. Z **nástroje** příkaz **Správce balíčků NuGet**, vyberte **konzoly Správce balíčků**. V **Konzola správce balíčků** okno, zadejte následující příkazy:

[!code-powershell[Main](scaleout-with-windows-azure-service-bus/samples/sample2.ps1)]

Použití `-ProjectName` možnost nainstalovat balíčky do projektu ASP.NET MVC, nikoli projekt Windows Azure.

## <a name="configure-the-backplane"></a>Konfigurace propojovacího rozhraní

V souboru Startup.cs vaší aplikace přidejte následující kód:

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample3.cs)]

Teď budete muset získat připojovací řetězec service bus. Na webu Azure Portal vyberte obor názvů služby Service bus, který jste vytvořili a klikněte na ikonu přístupový klíč.

![](scaleout-with-windows-azure-service-bus/_static/image7.png)

Zkopírujte připojovací řetězec do schránky a vložte ho do *connectionString* proměnné.

![](scaleout-with-windows-azure-service-bus/_static/image8.png)

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample4.cs)]

## <a name="deploy-to-azure"></a>Nasazení do Azure

V Průzkumníku řešení rozbalte **role** složky v projektu ChatService.

![](scaleout-with-windows-azure-service-bus/_static/image9.png)

Klikněte pravým tlačítkem na roli SignalRChat a vyberte **vlastnosti**. Vyberte **konfigurace** kartu. V části **instance** vyberte 2. Můžete také nastavit velikost virtuálního počítače **nejmenším instancím**.

![](scaleout-with-windows-azure-service-bus/_static/image10.png)

Uložte změny.

V Průzkumníku řešení klikněte pravým tlačítkem na projekt ChatService. Vyberte **Publikovat**.

![](scaleout-with-windows-azure-service-bus/_static/image11.png)

Pokud je toto vaše první čas publikování na platformě Windows Azure, je nutné stáhnout svoje přihlašovací údaje. V **publikovat** průvodce, klikněte na tlačítko "Přihlásit a stáhnout přihlašovací údaje". To vás vyzve k přihlášení na webu Windows Azure portal a stáhněte si soubor nastavení publikování.

![](scaleout-with-windows-azure-service-bus/_static/image12.png)

Klikněte na tlačítko **Import** a vyberte soubor nastavení publikování, který jste stáhli.

Klikněte na tlačítko **Další**. V **nastavení publikování** dialogového okna, v části **Cloudovou službu**, vyberte cloudovou službu, kterou jste vytvořili dříve.

![](scaleout-with-windows-azure-service-bus/_static/image13.png)

Klikněte na tlačítko **publikovat**. Může trvat pár minut a nasaďte aplikaci a spustit virtuální počítače.

Nyní při spuštění aplikace chat instance rolí komunikovat přes Azure Service Bus pomocí tématu služby Service Bus. Téma je fronty zpráv, který umožňuje několik předplatitelů.

Propojovacího rozhraní automaticky vytvoří téma a předplatná. Pokud chcete zobrazit předplatná a zprávu aktivity, otevřete na webu Azure portal, vyberte obor názvů služby Service Bus a klikněte na "Tématech".

![](scaleout-with-windows-azure-service-bus/_static/image14.png)

Ujistěte se, trvat několik minut, než se aktivita zpráva se zobrazí na řídicím panelu.

![](scaleout-with-windows-azure-service-bus/_static/image15.png)

Funkce SignalR spravuje životnost tématu. Tak dlouho, dokud je vaše aplikace nasazena, nepokoušejte ručně odstraňte témata nebo změnit nastavení v tomto tématu.

## <a name="troubleshooting"></a>Poradce při potížích

**System.InvalidOperationException "Pouze podporované IsolationLevel je"IsolationLevel.Serializable"."**

Této chybě může dojít, pokud je úroveň transakce pro operace nastavená na něco jiného než `Serializable`. Ověřte, že jsou prováděny žádné operace s další úrovní transakcí.
