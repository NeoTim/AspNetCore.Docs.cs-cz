---
uid: web-forms/overview/presenting-and-managing-data/model-binding/integrating-jquery-ui
title: Integrace s vazby modelu a webových formulářů JQuery UI Datepicker | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 3cbab37b-fb0f-4751-9ec4-74e068c3f380
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/integrating-jquery-ui
msc.type: authoredcontent
ms.openlocfilehash: ff1b17295c58d40d55bdcd4346b83121b579bb4c
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020556"
---
<a name="integrating-jquery-ui-datepicker-with-model-binding-and-web-forms"></a>Integrace prvku Datepicker uživatelského rozhraní JQuery s vazby modelu a webové formuláře
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource). Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.
> 
> Tento kurz ukazuje, jak přidat uživatelské rozhraní JQuery [Datepicker widgetu](http://jqueryui.com/datepicker/) webový formulář a vazba k aktualizaci databáze s vybranou hodnotu použití modelu.
> 
> V tomto kurzu vychází z vytvořeného v projektu [první](retrieving-data.md) a [druhý](updating-deleting-and-creating-data.md) části této série.
> 
> Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB. Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013. Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.


## <a name="what-youll-build"></a>Co budete vytvářet

V tomto kurzu budete:

1. Přidání vlastnosti do modelu k zaznamenání student získal datum registrace
2. Povolit uživateli vybrat datum registrace pomocí widgetu JQuery UI Datepicker
3. Vynutit ověřovacích pravidel pro datum registrace

Ve widgetu JQuery UI Datepicker umožňuje uživatelům snadno vybrat datum z kalendáře, která se zobrazí, když uživatel pracuje s polem. Pomocí tohoto widgetu můžete být uživatelům pohodlnější než ručním zadáním datum. Integrace stránku, která používá vazbu modelu pro operace s daty v ovládacím prvku Datepicker vyžaduje jenom malé množství další práce.

## <a name="add-a-new-property-to-the-model"></a>Přidat nové vlastnosti do modelu

Nejprve přidejte **data a času** vlastností pro své studenty pro model a migrovat tuto změnu do databáze. Otevřít **UniversityModels.cs**a přidat zvýrazněný kód do modelu studentů.

[!code-csharp[Main](integrating-jquery-ui/samples/sample1.cs?highlight=16-18)]

**RangeAttribute** je zahrnuta k vynucení ověřovacích pravidel pro vlastnost. Pro účely tohoto kurzu budeme předpokládat, že Contoso univerzitě byla založena na 1. lednu 2013, a proto dřívější registrace data nejsou platná.

V okně Správa balíčků přidejte migraci spuštěním příkazu **přidat migrace AddEnrollmentDate**. Všimněte si, že migrace kódu přidá nový sloupec Datum a čas do tabulky studentů. Tak, aby odpovídaly hodnota, kterou jste zadali v RangeAttribute, přidejte výchozí hodnotu pro nový sloupec, jak je znázorněno v následující zvýrazněný kód.

[!code-csharp[Main](integrating-jquery-ui/samples/sample2.cs?highlight=11)]

Uložte změny do souboru migrace.

Nemusíte znovu počáteční hodnoty data. Proto otevřete **Configuration.cs** ve složce migrace a odstranit nebo okomentovat kód v **počáteční hodnoty** metody. Soubor uložte a zavřete.

Nyní, spusťte příkaz **aktualizace databáze**. Všimněte si, že sloupec nyní existuje v databázi a všechny existující záznamy mají výchozí hodnotu pro EnrollmentDate.

## <a name="add-dynamic-controls-for-enrollment-date"></a>Přidat dynamické ovládací prvky pro datum registrace

Nyní přidáte ovládací prvky pro zobrazení a úpravy datum registrace. V tomto okamžiku je hodnota upravovat pomocí textového pole. V pozdější části kurzu změníte do textového pole na ovládacím prvku JQuery Datepicker.

Za prvé, je důležité si uvědomit, že není potřeba provádět změny na **AddStudent.aspx** souboru. Ovládací prvek DynamicEntity se automaticky generují nové vlastnosti.

Otevřít **Students.aspx**a přidejte následující zvýrazněný kód.

[!code-aspx[Main](integrating-jquery-ui/samples/sample3.aspx?highlight=13)]

Spusťte aplikaci a Všimněte si, že hodnota datum registrace můžete nastavit tak, že zadáte datum. Při přidání nového studenta:

![nastavit datum](integrating-jquery-ui/_static/image1.png)

Nebo úpravu existující hodnoty:

![Upravit datum](integrating-jquery-ui/_static/image2.png)

Zadáte datum funguje, ale nemusí být uživatelské prostředí, které chcete poskytnout. V další části vám umožní výběr data prostřednictvím kalendáře.

## <a name="install-nuget-package-to-work-with-jquery-ui"></a>Nainstalujte balíček NuGet pro práci s uživatelským rozhraním JQuery

**Uživatelského rozhraní džusu** balíček NuGet umožňuje snadnou integraci widgety uživatelského rozhraní JQuery do webové aplikace. Pokud chcete použít tento balíček, ji nainstalujte prostřednictvím balíčku NuGet.

![Přidat džusu uživatelského rozhraní](integrating-jquery-ui/_static/image3.png)

Verze džusu uživatelského rozhraní, který nainstalujete dojít ke konfliktu s verzí JQuery ve vaší aplikaci. Než budete pokračovat s tímto kurzem, spusťte aplikaci. Pokud dojde k chybě jazyka JavaScript, budete muset sjednotit JQuery verze. Můžete přidat do složky skriptů (verze 1.8.2 v době psaní tohoto kurzu) očekávaná verze JQuery nebo v Site.master zadejte cestu k souboru JQuery.

[!code-aspx[Main](integrating-jquery-ui/samples/sample4.aspx)]

## <a name="customize-datetime-template-to-include-datepicker-widget"></a>Přizpůsobení šablony data a času zahrnout Datepicker widgetu

Přidejte ovládací prvek Datepicker widgetu do šablony dynamických dat pro hodnotu data a času úpravy. Do šablony přidáte widgetu, automaticky je vykreslen ve formuláři pro přidání nového studenta a v mřížkovém zobrazení pro úpravy studenty. Otevřít **data a času\_Edit.ascx**a přidejte následující zvýrazněný kód.

[!code-aspx[Main](integrating-jquery-ui/samples/sample5.aspx?highlight=3)]

V souboru kódu na pozadí nastavíte minimální a maximální datum pro ovládací prvek DatePicker. Nastavením těchto hodnot se uživatelům zabránit v přechodu na neplatná data. Načte minimální a maximální hodnoty z **RangeAttribute** u vlastnosti data a času, pokud je zadáno. Otevřít **data a času\_Edit.ascx.cs**a přidejte následující zvýrazněný kód na stránku\_načíst metodu.

[!code-csharp[Main](integrating-jquery-ui/samples/sample6.cs?highlight=9-14)]

Spusťte webovou aplikaci a přejděte na stránku AddStudent. Zadejte hodnoty pro pole a Všimněte si, že po kliknutí na textové pole pro datum registrace se zobrazí v kalendáři.

![Výběr data](integrating-jquery-ui/_static/image4.png)

Vyberte datum a klikněte na tlačítko **vložit**. RangeAttribute vynucuje ověření na serveru. Nastavením vlastnosti minDate na ovládací prvek Datepicker můžete také použít ověření na straně klienta. Kalendář, nebude uživatel přejít na datum před hodnotu minDate.

Při úpravě záznamu v zobrazení mřížky se zobrazí také v kalendáři.

![Ovládací prvek DatePicker v prvku GridView](integrating-jquery-ui/_static/image5.png)

## <a name="conclusion"></a>Závěr

V tomto kurzu jste zjistili, jak začlenit JQuery widgetu do webového formuláře, který používá vazbu modelu.

V dalším [kurzu](using-query-string-values-to-retrieve-data.md), při výběru dat použijete hodnotu řetězce dotazu.

> [!div class="step-by-step"]
> [Předchozí](sorting-paging-and-filtering-data.md)
> [další](using-query-string-values-to-retrieve-data.md)
