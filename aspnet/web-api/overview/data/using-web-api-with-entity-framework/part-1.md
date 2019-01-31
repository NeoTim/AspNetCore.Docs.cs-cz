---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: Použití rozhraní Web API 2 s Entity Framework 6 | Dokumentace Microsoftu
author: MikeWasson
description: V tomto kurzu se dozvíte, že se se základy vytváření webovou aplikaci s webovým rozhraním API technologie ASP.NET back-endu. Tento kurz používá Entity Framework 6 pro uspořádání dat...
ms.author: riande
ms.date: 01/17/2019
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: 266c808e3525787181038d2de473194989039e02
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236520"
---
<a name="using-web-api-2-with-entity-framework-6"></a>Použití webového rozhraní API 2 se sadou Entity Framework 6
====================

[Stáhnout dokončený projekt](https://github.com/MikeWasson/BookService)

> V tomto kurzu se naučíte, že se se základy vytváření webovou aplikaci s webovým rozhraním API technologie ASP.NET back-endu. Tento kurz používá Entity Framework 6 pro datová vrstva a knihovnou Knockout.js pro aplikace JavaScript na straně klienta. Tento kurz také ukazuje, jak nasadit aplikaci do Azure App Service Web Apps.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
> - Web API 2.1
> - Visual Studio 2017 (stáhněte si Visual Studio 2017 [tady](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))
> - Entity Framework 6
> - .NET 4.7
> - [Knockout.js](http://knockoutjs.com/) 3.1

Tento kurz používá prostředí ASP.NET Web API 2 s Entity Framework 6 a vytvořte webovou aplikaci, která zpracovává back-end databáze. Zde je snímek obrazovky aplikace, kterou vytvoříte.

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

Aplikace používá návrhu jednostránkové aplikace (SPA). "Jednostránkovou aplikaci" je obecný termín pro webovou aplikaci, která načte jednu stránku HTML a následně aktualizuje dynamicky, místo načtení nové stránky na stránku. Po načtení počáteční stránky aplikace komunikuje se serverem přes odesílání požadavků AJAX. AJAX žádosti vrácená data JSON, který aplikace používá k aktualizaci uživatelského rozhraní.

AJAX není novinka, ale ještě dnes existují architektury JavaScriptu, které usnadňují tvorbu a udržování rozsáhlé sofistikované aplikace SPA. Tento kurz používá [knihovnou Knockout.js](http://knockoutjs.com/), ale můžete použít libovolnou architekturu JavaScript klienta.

Tady jsou hlavní stavební bloky pro tuto aplikaci:

- ASP.NET MVC vytvoří na stránce HTML.
- Rozhraní ASP.NET Web API zpracovává požadavky AJAX a vrací JSON data.
- Rozhraní Knockout.js data vazbu elementů HTML JSON data.
- Entity Framework a k databázi.

## <a name="see-this-app-running-on-azure"></a>Zobrazit tuto aplikaci běžící v Azure

Chcete zobrazit dokončené web spuštěný jako živou webovou aplikaci? Kompletní verze aplikace můžete nasadit ke svému účtu Azure pomocí následujícího tlačítka.

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

Potřebujete účet Azure k nasazení tohoto řešení do Azure. Pokud ještě nemáte účet, máte následující možnosti:

- [Zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.
- [Aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.

## <a name="create-the-project"></a>Vytvoření projektu

Otevřít Visual Studio. Z **souboru** nabídce vyberte možnost **nový**a pak vyberte **projektu**. (Nebo vyberte **nový projekt** na úvodní stránce.)

V **nový projekt** dialogového okna, vyberte **webové** v levém podokně a **webová aplikace ASP.NET (.NET Framework)** v prostředním podokně. Pojmenujte projekt **BookService** a vyberte **OK**.

[![](part-1/_static/image11.png)](part-1/_static/image11.png)

V **nový projekt ASP.NET** dialogového okna, vyberte **webového rozhraní API** šablony.

[![](part-1/_static/image12.png)](part-1/_static/image12.png)


Vyberte **OK** pro vytvoření projektu.

## <a name="configure-azure-settings-optional"></a>Konfigurace nastavení služby Azure (volitelné)

Po vytvoření projektu můžete nasadit do Azure App Service Web Apps v každém okamžiku. 

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt a vyberte **publikovat**.

2. V zobrazeném okně vyberte **Start**. **Vyberte cíl publikování** zobrazí se dialogové okno.

   [![](part-1/_static/image14.png)](part-1/_static/image14.png)

3. Vyberte **vytvořit profil**. **Vytvořit službu App Service** zobrazí se dialogové okno.

   [![](part-1/_static/image15.png)](part-1/_static/image15.png)

   Přijměte výchozí hodnoty, nebo zadat jiné hodnoty pro název aplikace, skupiny prostředků, hostování plán předplatného Azure a geografické oblasti. 

4. Vyberte **vytvoření databáze SQL**. **Nakonfigurujte systém SQL Server** zobrazí se dialogové okno. 

   [![](part-1/_static/image16.png)](part-1/_static/image16.png)

   Přijměte výchozí hodnoty nebo zadat jiné hodnoty. Zadejte **uživatelské jméno správce** a **heslo správce** pro novou databázi. Vyberte **OK** po dokončení. **Vytvořit službu App Service** se znovu zobrazí stránka.

5. Vyberte **vytvořit** při vytváření profilu. Zpráva se zobrazí v pravém dolním rohu, která udává, že nasazování probíhá. Po nějakou dobu **publikovat** se znovu zobrazí okno.

    [![](part-1/_static/image17.png)](part-1/_static/image17.png)
   
    Profil, který jste vytvořili k nasazení aplikace je nyní k dispozici. 


> [!div class="step-by-step"]
> [Next](part-2.md)
