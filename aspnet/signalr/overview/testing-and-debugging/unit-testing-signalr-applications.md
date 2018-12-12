---
uid: signalr/overview/testing-and-debugging/unit-testing-signalr-applications
title: Testování aplikace SignalR | Dokumentace Microsoftu
author: pfletcher
description: Tento článek popisuje, jak používat funkce testování částí 2.0 SignalR.
ms.author: riande
ms.date: 06/10/2014
ms.assetid: d1983524-e0d5-4ee6-9d87-1f552f7cb964
msc.legacyurl: /signalr/overview/testing-and-debugging/unit-testing-signalr-applications
msc.type: authoredcontent
ms.openlocfilehash: d8f3afdc2749173d1e260096ee6bd4bf1ae4c7cb
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287507"
---
<a name="unit-testing-signalr-applications"></a>Testování jednotek aplikace knihovnou SignalR
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento článek popisuje použití funkce SignalR 2 testování částí.
>
> ## <a name="software-versions-used-in-this-topic"></a>Verze softwaru použitým v tomto tématu
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Funkce SignalR verze 2
>
>
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).


<a id="unit"></a>
## <a name="unit-testing-signalr-applications"></a>Testování aplikace knihovnou SignalR

Funkce testování částí v SignalR 2 slouží k vytvoření testů jednotek pro aplikace SignalR. Funkce SignalR 2 zahrnuje [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) rozhraní, které lze použít k vytvoření mock objektu k simulaci vaší metod rozbočovače pro testování.

V této části přidáte testů jednotek pro aplikace vytvořené v [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md) pomocí [XUnit.net](https://github.com/xunit/xunit) a [Moq](https://github.com/Moq/moq4).

XUnit.net se dá používat k ovládání testu; Moq se použije k vytvoření [napodobení](http://en.wikipedia.org/wiki/Mock_object) objektu pro testování. Další napodobování architektury je možné v případě potřeby; [NSubstitute](http://nsubstitute.github.io/) je také vhodná. Tento kurz ukazuje, jak nastavit mock objektu dvěma způsoby: Nejprve pomocí `dynamic` objektu (představíme v rozhraní .NET Framework 4) a druhý, pomocí rozhraní.

### <a name="contents"></a>Obsah

Tento kurz obsahuje následující části.

- [Testování částí pomocí dynamické](#dynamic)
- [Podle typu testování částí](#type)

<a id="dynamic"></a>
### <a name="unit-testing-with-dynamic"></a>Testování částí pomocí dynamické

V této části přidáte testování částí pro aplikace vytvořené v [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md) používání dynamických objektů.

1. Nainstalujte [XUnit Runner rozšíření](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) pro Visual Studio 2013.
2. Dokončit [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md), nebo stáhnout hotovou aplikaci [galerie kódů MSDN](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).
3. Pokud používáte verzi aplikace, jak začít, otevřete **Konzola správce balíčků** a klikněte na tlačítko **obnovení** přidat do projektu balíček SignalR.

    ![Obnovení balíčků](unit-testing-signalr-applications/_static/image1.png)
4. Přidáte projekt do řešení pro testování částí. Klikněte pravým tlačítkem na řešení v **Průzkumníka řešení** a vyberte **přidat**, **nový projekt...** . V části **jazyka C#** uzlu, vyberte **Windows** uzlu. Vyberte **knihovna tříd**. Název nového projektu **TestLibrary** a klikněte na tlačítko **OK**.

    ![Vytvořte knihovnu testu](unit-testing-signalr-applications/_static/image2.png)
5. Přidáte odkaz v testovém projektu knihovny do projektu SignalRChat. Klikněte pravým tlačítkem myši **TestLibrary** projektu a vyberte **přidat**, **odkaz...** . Vyberte **projekty** pod uzlem **řešení** uzlu a kontrola **SignalRChat**. Klikněte na **OK**.

    ![Přidat odkaz na projekt](unit-testing-signalr-applications/_static/image3.png)
6. Přidat balíčky SignalR, Moq a XUnit **TestLibrary** projektu. V **Konzola správce balíčků**, nastavte **výchozí projekt** rozevírací nabídku, která **TestLibrary**. V okně konzoly spusťte následující příkazy:

   - `Install-Package Microsoft.AspNet.SignalR`
   - `Install-Package Moq`
   - `Install-Package XUnit`

     ![Instalace balíčků](unit-testing-signalr-applications/_static/image4.png)
7. Vytvoření testovacího souboru. Klikněte pravým tlačítkem myši **TestLibrary** projektu a klikněte na tlačítko **přidat...** , **Třídy**. Pojmenujte novou třídu **Tests.cs**.
8. Nahraďte obsah Tests.cs následujícím kódem.

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample1.cs)]

    Ve výše uvedeném kódu, je vytvořen pomocí testovacího klienta `Mock` objektu z [Moq](https://github.com/Moq/moq4) knihovny typu [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (v SignalR 2.1 přiřadit `dynamic` pro typ parametr.) `IHubCallerConnectionContext` Rozhraní je objekt proxy serveru, pomocí kterého můžete volat metody na straně klienta. `broadcastMessage` Funkce potom definovaná pro mock klienta tak, že je možné vyvolat v `ChatHub` třídy. Modul testu pak zavolá `Send` metodu `ChatHub` třídu, která volá imitaci `broadcastMessage` funkce.
9. Sestavte řešení stisknutím kombinace kláves **F6**.
10. Spusťte Jednotkový test. V sadě Visual Studio, vyberte **testovací**, **Windows**, **Průzkumník testů**. V okně Průzkumník testů, klikněte pravým tlačítkem na **HubsAreMockableViaDynamic** a vyberte **spustit vybrané testy**.

    ![Průzkumník testů](unit-testing-signalr-applications/_static/image5.png)
11. Ověřte, že test proběhl úspěšně kontrolou dolním podokně, v okně Průzkumníka testů. V okně se zobrazí, že test proběhl úspěšně.

    ![Test proběhl úspěšně](unit-testing-signalr-applications/_static/image6.png)

<a id="type"></a>
### <a name="unit-testing-by-type"></a>Podle typu testování částí

V této části přidáte testu pro aplikaci vytvořenou v [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md) pomocí rozhraní, který obsahuje metodu, která má být testována.

1. Proveďte kroky 1 až 7 [testování částí pomocí dynamické](#dynamic) kurzu výše.
2. Nahraďte obsah Tests.cs následujícím kódem.

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample2.cs)]

    Ve výše uvedeném kódu, se vytvoří rozhraní definuje podpis metody `broadcastMessage` metodu, pro kterou se vytvoří testovací stroj mock klienta. Mock klienta se pak vytvoří pomocí `Mock` objekt typu [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (v SignalR 2.1 přiřadit `dynamic` pro parametr typu.) `IHubCallerConnectionContext` Rozhraní je objekt proxy serveru, pomocí kterého můžete volat metody na straně klienta.

    Test pak vytvoří instanci `ChatHub`a potom vytvoří na cvičnou verzi `broadcastMessage` metodu, která pak je vyvolána voláním `Send` metodu v rozbočovači.
3. Sestavte řešení stisknutím kombinace kláves **F6**.
4. Spusťte Jednotkový test. V sadě Visual Studio, vyberte **testovací**, **Windows**, **Průzkumník testů**. V okně Průzkumník testů, klikněte pravým tlačítkem na **HubsAreMockableViaDynamic** a vyberte **spustit vybrané testy**.

    ![Průzkumník testů](unit-testing-signalr-applications/_static/image7.png)
5. Ověřte, že test proběhl úspěšně kontrolou dolním podokně, v okně Průzkumníka testů. V okně se zobrazí, že test proběhl úspěšně.

    ![Test proběhl úspěšně](unit-testing-signalr-applications/_static/image8.png)
