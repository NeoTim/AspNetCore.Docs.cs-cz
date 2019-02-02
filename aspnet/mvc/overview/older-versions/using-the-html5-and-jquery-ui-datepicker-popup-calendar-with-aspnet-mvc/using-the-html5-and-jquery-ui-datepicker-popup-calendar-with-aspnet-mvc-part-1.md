---
uid: mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1
title: Použití jazyka HTML5 a kalendáře jQuery UI Datepicker s architekturou ASP.NET MVC – část 1 | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se seznámíte se základy práce pomocí editoru šablon, šablony zobrazení a kalendářem jQuery uživatelského rozhraní prvkem datepicker v MV ASP.NET...
ms.author: riande
ms.date: 08/29/2011
ms.assetid: c23d27f7-b0cf-44f2-8445-fb69e045c674
msc.legacyurl: /mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1
msc.type: authoredcontent
ms.openlocfilehash: 3e700d2db4f86fe6734e2f08b01c9f8a8a69b6c3
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667788"
---
<a name="using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc---part-1"></a>Použití jazyka HTML5 a kalendáře jQuery UI Datepicker s architekturou ASP.NET MVC – část 1
====================
Podle [Rick Anderson]((https://twitter.com/RickAndMSFT))

> V tomto kurzu se seznámíte se základy práce pomocí editoru šablon, šablony zobrazení a kalendářem jQuery UI datepicker v aplikaci MVC rozhraní ASP.NET Web.


V tomto kurzu se seznámíte se základy práce pomocí editoru šablon, zobrazení šablon a jQuery [kalendář automaticky otevírané okno prvkem datepicker v uživatelském rozhraní](http://plugins.jquery.com/project/datepicker) v aplikaci MVC rozhraní ASP.NET Web. Pro účely tohoto kurzu můžete použít Microsoft Visual Web Developer 2010 Express Service Pack 1 (&quot;Visual Web Developer&quot;), což je bezplatná verze sady Microsoft Visual Studio, nebo pokud už máte, který můžete použít Visual Studio 2010 SP1.

Než začnete, ujistěte se, že jste nainstalovali požadavky uvedené níže. Kliknutím na následující odkaz můžete nainstalovat všechny z nich: [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Alternativně můžete nainstalovat jednotlivě požadovaný software pomocí následujících odkazů:

- [Visual Studio Web Developer Express SP1 požadavky](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
- [ASP.NET MVC 3 Tools Update](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
- [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(podpora modulu runtime a nástroje)

Pokud používáte Visual Studio 2010 namísto Visual Web Developer, nainstalujte příslušné požadované součásti po kliknutí na následující odkaz: [Visual Studio 2010 požadavky](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).

V tomto kurzu se předpokládá dokončení [Začínáme s MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) kurzu nebo že jste obeznámeni s vývojem pro ASP.NET MVC. Tento kurz pracuje s dokončený projekt z [Začínáme s MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) kurzu.

Tento kurz ukazuje kód v jazyce C#. Ale [počáteční projekt](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800) a dokončené projektu jsou také k dispozici v jazyce Visual Basic.

Projekt sady Visual Studio s C# a zdrojový kód jazyka Visual Basic je k dispozici v tomto tématu: [Stáhněte si](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800).

### <a name="what-youll-build"></a>Co budete vytvářet

Přidejte šablony (konkrétně, úpravy a zobrazení šablony) pro jednoduchou aplikaci film výpis, který byl vytvořen v [Začínáme s MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) kurzu. Taky se přidá [jQuery UI datepicker](http://jqueryui.com/demos/datepicker/) překryvný kalendář pro zjednodušení procesu zadávání data. Na následujícím snímku obrazovky je vidět změny aplikace s jQuery UI datepicker překryvný kalendář zobrazen.

![dokončení jQuery](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image1.png)

### <a name="skills-youll-learn"></a>Dovednosti, které se dozvíte

Zde je, co se dozvíte:

- Použití atributů z [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) obor názvů pro řídit formát data, jakmile se zobrazí a pokud je v režimu úprav.
- Postup vytvoření šablon (Upravit a zobrazit šablony) k řízení formátování data.
- Postup přidání [jQuery UI datepicker](http://jqueryui.com/demos/datepicker/) jako způsob, jak zadat datová pole.

### <a name="getting-started"></a>Začínáme

Pokud ještě nemáte výpisu film aplikaci počáteční projekt, stáhněte si ji: 

* [Stáhněte si](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098).
* V Průzkumníku Windows, klikněte pravým tlačítkem myši *MvcMovie.zip* a vyberte možnost **vlastnosti**. 
* V **MvcMovie.zip vlastnosti** dialogu **Odblokovat**. (Odblokování brání upozornění zabezpečení, ke které dojde při pokusu o použití *ZIP* soubor, který jste stáhli z webu.)

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image2.png)

Klikněte pravým tlačítkem myši *MvcMovie.zip* a vyberte možnost **Extrahovat vše** dekomprimovat soubor. V aplikaci Visual Web Developer nebo Visual Studio 2010, otevřete *MvcMovieCS\_TU.sln* souboru.

V **Průzkumníka řešení**, dvakrát klikněte *Views\Shared\\_Layout.cshtml* ho otevřete. Změnit `H1` záhlaví z **filmová aplikace MVC** k **film jQuery**. Stisknutím kláves CTRL + F5 spusťte aplikaci a klikněte na tlačítko **Domů** kartu, která vás přesměruje na `Index` metody kontroleru video. Vyzkoušet si aplikaci, vyberte **upravit** odkaz a **podrobnosti** odkaz pro jeden z videa. Všimněte si, že v indexu, upravit, a hezky formátovaný zobrazení podrobností, datum vydání a cena:

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image3.png)

Formátování pro datum a cena je výsledkem použití [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) atribut vlastnosti `Movie` třídy.

Otevřít *Movie.cs* souboru a nastavte komentář `DisplayFormat` atribut na `ReleaseDate` a `Price` vlastnosti. Výsledná `Movie` třídy vypadá takto:

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample1.cs)]

Stisknutím kláves CTRL + F5 znovu spusťte aplikaci a vyberte **Domů** kartu k zobrazení seznamu video. Tentokrát datum vydání zobrazuje datum a čas, a cena pole již neukazuje symbol měny. Vaše změna `Movie` třídy zrušila nice formátování, které jste viděli dříve, ale opravíte to za chvíli.

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image4.png)

### <a name="using-the-dataannotations-datatype-attribute-to-specify-the-data-type"></a>Pomocí atributu DataAnnotations DataType určit typ dat

Nahradit komentované `DisplayFormat` atribut pro `ReleaseDate` vlastnost s [datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) atribut, pomocí `Date` výčtu. Nahradit `DisplayFormat` atribut pro `Price` vlastnost s [datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) atribut znovu, tento pomocí `Currency` výčtu. Je to, jak vypadá Dokončený kód:

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample2.cs)]

Spusťte aplikaci. Nyní datum vydání a vlastnosti ceny jsou správný formát (které používá odpovídající formáty data a měna). [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) atribut poskytuje typ metadat pro integrované technologie ASP.NET MVC šablony tak, aby pole vykreslení ve správném formátu. Použití `DataType` atribut je vhodnější než použít `DisplayFormat` atribut, který byl původně v kódu, protože `DataType` atribut díky modelu přehlednější a zvýšení flexibility pro účely jako internacionalizace.

V další části uvidíte, jak vytvořit vlastní šablony pro zobrazení datových polí.

> [!div class="step-by-step"]
> [Next](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2.md)
