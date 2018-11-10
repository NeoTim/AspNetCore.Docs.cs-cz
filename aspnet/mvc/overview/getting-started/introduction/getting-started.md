---
uid: mvc/overview/getting-started/introduction/getting-started
title: Začínáme s ASP.NET MVC 5 | Dokumentace Microsoftu
author: Rick-Anderson
ms.author: riande
ms.date: 10/04/2018
ms.assetid: f3d8adbe-55e7-4fd4-84a8-7155bc45c676
msc.legacyurl: /mvc/overview/getting-started/introduction/getting-started
msc.type: authoredcontent
ms.openlocfilehash: 462583a42f20126ef8f8b5927268c20ec1ceab89
ms.sourcegitcommit: edb9d2d78c9a4d68b397e74ae2aff088b325a143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51505794"
---
<a name="getting-started-with-aspnet-mvc-5"></a>Začínáme s ASP.NET MVC 5
====================
Podle [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [consider RP](../../../../includes/razor.md)]

V tomto kurzu se naučíte se základy vytváření webové aplikace ASP.NET MVC 5 pomocí [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017). Poslední zdrojový kód pro tento kurz je umístěn na [Githubu](https://github.com/aspnet/Docs/tree/master/aspnet/mvc/overview/getting-started/introduction/sample/MvcMovie/MvcMovie).

V tomto kurzu zapsal [Scott Guthrie](https://weblogs.asp.net/scottgu/) (twitter[ @scottgu ](https://twitter.com/scottgu) ), [Scott Hanselman](http://www.hanselman.com/blog/) (twitter: [ @shanselman ](https://twitter.com/shanselman) ) , a [Rick Anderson](https://twitter.com/RickAndMSFT) ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) )

Potřebujete účet Azure tak, aby tuto aplikaci nasadit do Azure:

- Je možné [zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.
- Je možné [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.

## <a name="get-started"></a>Začínáme

Začněte tím, že [instalací sady Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017). Spusťte Visual Studio.

Visual Studio je integrované vývojové prostředí, nebo integrovaného vývojového prostředí. Stejným způsobem, jako používáte k tvorbě dokumenty Microsoft Wordu, budete používat integrované vývojové prostředí pro vytváření aplikací. V sadě Visual Studio je seznam v dolní části zobrazující různé možnosti, které jsou k dispozici pro vás. Je také nabídka, která nabízí další způsob, jak provádět úkoly v integrovaném vývojovém prostředí. Například místo výběru **nový projekt** na **úvodní stránku**, můžete použít řádek nabídek a vyberte **souboru** > **nový projekt**.

![](getting-started/_static/image1.png)

## <a name="create-your-first-app"></a>Vytvoření první aplikace

Na **úvodní stránku**vyberte **nový projekt**. V **nový projekt** dialogové okno, vyberte **Visual C#** kategorie na levé straně, pak **webové**a pak vyberte **webová aplikace ASP.NET (.NET Framework)**  šablony projektu. Pojmenujte svůj projekt "MvcMovie" a klikněte na tlačítko **OK**.

![](getting-started/_static/image2.png)

V **nová webová aplikace ASP.NET** dialogovém okně zvolte **MVC** a klikněte na tlačítko **OK**.

![](getting-started/_static/image3.png)

Visual Studio použít výchozí šablonu projektu ASP.NET MVC, které jste právě vytvořili, takže Teď máte funkční aplikaci bez teď zrovna nic nedělá! Jde jednoduchý "Hello World!" projekt a je vhodné místo pro spuštění vaší aplikace.

![](getting-started/_static/image4.png)

Stisknutím klávesy **F5** pro spuštění ladění. Když stisknete klávesu **F5**, Visual Studio spustí [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí webovou aplikaci. Visual Studio pak spustí prohlížeč a otevře domovskou stránku aplikace. Všimněte si, že na panelu Adresa prohlížeče `localhost:port#` a nemít něco podobného `example.com`. Důvodem je, že `localhost` vždy odkazuje na vaše vlastní místní počítače v tomto případě je spuštěna aplikace právě jste vytvořili. Při spuštění webový projekt sady Visual Studio náhodný port se používá pro webový server. Na následujícím obrázku je číslo portu 1234. Při spuštění aplikace se zobrazí jiné číslo portu.

![](getting-started/_static/image5.png)

Předem připravené tuto výchozí šablonu poskytuje `Home`, `Contact`, a `About` stránky. Na následujícím obrázku se nezobrazí **Domů**, **o**, a **kontakt** odkazy. V závislosti na velikosti okna prohlížeče můžete potřebovat kliknutím na ikonu navigační naleznete pod následujícími odkazy.

![](getting-started/_static/image6.png)

Aplikace také poskytuje podporu pro registraci a přihlaste se. Dalším krokem je změnit, jak tato aplikace funguje a přečtěte si ještě něco o architektuře ASP.NET MVC. Ukončete aplikaci ASP.NET MVC a Změníme kód.

Seznam aktuální kurzy najdete v tématu [MVC Doporučené články](../mvc-learning-sequence.md).

## <a name="see-this-app-running-on-azure"></a>Zobrazit tuto aplikaci běžící v Azure

Chcete zobrazit dokončené web spuštěný jako živou webovou aplikaci? Kompletní verze aplikace můžete nasadit ke svému účtu Azure jednoduše kliknutím na následující tlačítko.

[![](https://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?repository=https://github.com/aspnet/Docs/tree/master/aspnet/mvc/overview/getting-started/introduction/sample/MvcMovie&amp;WT.mc_id=deploy_azure_aspnet)

Potřebujete účet Azure k nasazení tohoto řešení do Azure. Pokud ještě nemáte účet, použijte jednu z následujících možností k jejímu vytvoření:

- [Zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.
- [Aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers) – předplatné sady Visual Studio vám dává kredity každý měsíc, můžete použít k placení za služby Azure.

> [!div class="step-by-step"]
> [Next](adding-a-controller.md)
