---
uid: aspnet/overview/owin-and-katana/owin-startup-class-detection
title: Rozpoznání spouštěcí třídy OWIN | Dokumentace Microsoftu
author: Praburaj
description: Tento kurz ukazuje, jak konfigurovat načteny které třídy pro spuštění OWIN. Další informace o OWIN naleznete v tématu Přehled projektu Katana. V tomto kurzu se...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 08257f55-36f4-4e39-9c88-2a5602838c79
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-startup-class-detection
msc.type: authoredcontent
ms.openlocfilehash: 0b34cca8b48383dbb028106651758dff889ed614
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667294"
---
<a name="owin-startup-class-detection"></a>Rozpoznání spouštěcí třídy OWIN
====================

> Tento kurz ukazuje, jak konfigurovat načteny které třídy pro spuštění OWIN. Další informace o OWIN, naleznete v tématu [Přehled projektu Katana](an-overview-of-project-katana.md). V tomto kurzu byla zapsána od Ricka Andersona ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) ), Manažer Praburaj a Howard Dierking ( [ @howard \_dierking](https://twitter.com/howard_dierking) ).
>
> ## <a name="prerequisites"></a>Požadavky
>
> [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)


## <a name="owin-startup-class-detection"></a>Rozpoznání spouštěcí třídy OWIN

 Každá aplikace OWIN obsahuje třídu spuštění zadávat komponenty pro kanál aplikací. Existují různé způsoby připojení třídu pro spuštění v modulu runtime, v závislosti na model hostingu zvolíte (OwinHost, služby IIS a služby IIS Express). Třída při spuštění uvedeno v tomto kurzu je možné v každé hostitelské aplikace. Třída při spuštění napojení hostování modulu runtime pomocí jedné z těchto přístupů:

1. **Zásady vytváření názvů**: Vyhledá Katana pro třídu s názvem `Startup` v oboru názvů odpovídající název sestavení nebo v globálním oboru názvů.
2. **Atribut OwinStartup**: Toto je postup, jak bude Většina vývojářů zadejte třídu pro spuštění. Tento atribut nastaví na třídu pro spuštění `TestStartup` třídy v `StartupDemo` oboru názvů.

    [!code-csharp[Main](owin-startup-class-detection/samples/sample1.cs)]

   `OwinStartup` Atribut přepisuje zásady vytváření názvů. Můžete také zadat popisný název k tomuto atributu, ale použít popisný název vyžaduje také použití `appSetting` element v konfiguračním souboru.
3. **Element nastavení aplikace v konfiguračním souboru**: `appSetting` Přepíše element `OwinStartup` atribut a zásady vytváření názvů. Můžete mít více tříd spuštění (každý pomocí `OwinStartup` atribut) a konfigurace, které třída při spuštění bude načten v konfiguračním souboru pomocí značek podobný následujícímu:

    [!code-xml[Main](owin-startup-class-detection/samples/sample2.xml)]

   Můžete také použít následující klíč, který explicitně určuje třídu pro spuštění a sestavení:

    [!code-xml[Main](owin-startup-class-detection/samples/sample3.xml)]

   Následující kód XML v konfiguračním souboru určí název třídy popisný spuštění `ProductionConfiguration`.

    [!code-xml[Main](owin-startup-class-detection/samples/sample4.xml)]

   Výše uvedené značky musí být použit s následující `OwinStartup` atribut, který určuje popisný název a způsobí, že `ProductionStartup2` má třída spustit.

    [!code-csharp[Main](owin-startup-class-detection/samples/sample5.cs?highlight=1,16)]
4. Chcete-li zakázat zjišťování pro spuštění OWIN přidat `appSetting owin:AutomaticAppStartup` s hodnotou `"false"` v souboru web.config.

    [!code-xml[Main](owin-startup-class-detection/samples/sample6.xml)]

## <a name="create-an-aspnet-web-app-using-owin-startup"></a>Vytvoření webové aplikace ASP.NET pomocí OWIN Startup

1. Vytvoří prázdná webová aplikace Asp.Net a pojmenujte ho **StartupDemo**. – Instalace `Microsoft.Owin.Host.SystemWeb` pomocí Správce balíčků NuGet. Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet**a potom **Konzola správce balíčků**. Zadejte následující příkaz:

    [!code-powershell[Main](owin-startup-class-detection/samples/sample7.ps1)]
2. Přidání třídy pro spuštění OWIN. V sadě Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **přidat třídu**. - v **přidat novou položku** dialogového okna zadejte *OWIN* do vyhledávacího pole a změnit název, který má Startup.cs, a pak vyberte **přidat**.

     ![](owin-startup-class-detection/_static/image1.png)

   Další čas, které chcete přidat *třída Owin Startup*, bude uvedená v k dispozici **přidat** nabídky.

     ![](owin-startup-class-detection/_static/image2.png)

   Alternativně klikněte pravým tlačítkem na projekt a vyberte **přidat**a pak vyberte **nová položka**a pak vyberte **třída Owin Startup**.

     ![](owin-startup-class-detection/_static/image3.png)

- Nahraďte generovaný kód *Startup.cs* souboru následujícím kódem:

    [!code-csharp[Main](owin-startup-class-detection/samples/sample8.cs?highlight=5,7,15-28,31-34)]

  `app.Use` Výraz lambda se použije k registraci zadané middleware součást do kanálu OWIN. V tomto případě nastavíme protokolování příchozích požadavků před reagovat na příchozí požadavek. `next` Parametrem je delegát ( [Func](https://msdn.microsoft.com/library/bb534960(v=vs.100).aspx) &lt; [úloh](https://msdn.microsoft.com/library/dd321424(v=vs.100).aspx) &gt; ) k další komponenta v kanálu. `app.Run` Lambda výraz zachytí vytvořit kanál na příchozí požadavky a poskytuje mechanismus odpovědi.
     > [!NOTE]
     > Ve výše uvedeném kódu, budeme mít komentář `OwinStartup` atribut a My se spoléhat na konvenci spuštěných třídu s názvem `Startup` .-stiskněte ***F5*** ke spuštění aplikace. Stiskněte několikrát tlačítko Aktualizovat.

    ![](owin-startup-class-detection/_static/image4.png) Poznámka: Číslo, jak je znázorněno obrázcích v tomto kurzu nebudou odpovídat na číslo uvedené. Milisekundy řetězec se používá k zobrazit nová odpověď, když obnovíte stránku.
  Zobrazí se informace o trasování v **výstup** okna.

    ![](owin-startup-class-detection/_static/image5.png)

## <a name="add-more-startup-classes"></a>Přidat další spuštění třídy

V této části přidáme další spuštění třídu. Do vaší aplikace můžete přidat více třídy pro spuštění OWIN. Například můžete chtít vytvořit spouštěcí třídy pro vývoj, testování a produkci.

1. Vytvořte novou třídu spuštění OWIN s názvem `ProductionStartup`.
2. Generovaného kódu nahraďte následujícím kódem:

    [!code-csharp[Main](owin-startup-class-detection/samples/sample9.cs?highlight=14-18)]
3. Stisknutím klávesy F5 spusťte aplikaci ovládacího prvku. `OwinStartup` Atribut určuje třídu pro spuštění produkčního prostředí běží.

    ![](owin-startup-class-detection/_static/image6.png)
4. Vytvořte jinou třídu pro spuštění OWIN s názvem `TestStartup`.
5. Generovaného kódu nahraďte následujícím kódem:

    [!code-csharp[Main](owin-startup-class-detection/samples/sample10.cs?highlight=6,14-18)]

   `OwinStartup` Určuje atribut přetížení výše `TestingConfiguration` jako *popisný* název třída při spuštění.
6. Otevřít *web.config* a přidejte spouštěcí klíč aplikace OWIN, který určuje popisný název třídy spuštění:

    [!code-xml[Main](owin-startup-class-detection/samples/sample11.xml?highlight=3-5)]
7. Stisknutím klávesy F5 spusťte aplikaci ovládacího prvku. Element nastavení aplikace trvá předcházejících a testovací konfigurace spuštění.

    ![](owin-startup-class-detection/_static/image7.png)
8. Odeberte *popisný* název z `OwinStartup` atribut `TestStartup` třídy.

    [!code-csharp[Main](owin-startup-class-detection/samples/sample12.cs)]
9. Nahradit spouštěcí klíč aplikace OWIN v *web.config* souboru následujícím kódem:

    [!code-xml[Main](owin-startup-class-detection/samples/sample13.xml)]
10. Vrátit zpět `OwinStartup` atribut v každé třídě atribut výchozího kódu generovaný sady Visual Studio:

    [!code-csharp[Main](owin-startup-class-detection/samples/sample14.cs)]

    Všechny klíče aplikace OWIN při spuštění níže způsobí, že třída produkčního prostředí pro spuštění.

    [!code-xml[Main](owin-startup-class-detection/samples/sample15.xml)]

    Poslední spouštěcí klíč určuje metodu spouštěný konfigurace. Následující spouštěcí klíč aplikace OWIN umožňuje změnit název třídy konfigurace má `MyConfiguration` .

    [!code-xml[Main](owin-startup-class-detection/samples/sample16.xml)]

## <a name="using-owinhostexe"></a>Pomocí Owinhost.exe

1. Nahradíte soubor Web.config následujícím kódem:

    [!code-xml[Main](owin-startup-class-detection/samples/sample17.xml?highlight=3-6)]

   Klíč poslední služby wins, tak v tomto případě `TestStartup` je zadán.
2. Nainstalujte z konzole PMC Owinhost:

    [!code-console[Main](owin-startup-class-detection/samples/sample18.cmd)]
3. Přejděte do složky aplikace (složku obsahující *Web.config* soubor) a příkazový řádek a zadejte:

    [!code-console[Main](owin-startup-class-detection/samples/sample19.cmd)]

   Příkazové okno se zobrazí:

    [!code-console[Main](owin-startup-class-detection/samples/sample20.cmd)]
4. Spustit prohlížeč s adresou URL `http://localhost:5000/`.

    ![](owin-startup-class-detection/_static/image8.png)

   OwinHost zachované při spuštění konvence uvedené výše.
5. V příkazovém okně stisknutím klávesy Enter ukončete OwinHost.
6. V `ProductionStartup` třídy, přidejte následující atribut OwinStartup, který určuje popisný název *ProductionConfiguration*.

    [!code-csharp[Main](owin-startup-class-detection/samples/sample21.cs)]
7. Do příkazového řádku a zadejte:

    [!code-console[Main](owin-startup-class-detection/samples/sample22.cmd)]

   Třída při spuštění produkčního prostředí je načtena.
    ![](owin-startup-class-detection/_static/image9.png)

   Naše aplikace má více spuštění třídy a v tomto příkladu jsme změnily která třída při spuštění načíst do modulu runtime.
8. Následující možnosti modulu runtime při spuštění testů:

    [!code-console[Main](owin-startup-class-detection/samples/sample23.cmd)]
