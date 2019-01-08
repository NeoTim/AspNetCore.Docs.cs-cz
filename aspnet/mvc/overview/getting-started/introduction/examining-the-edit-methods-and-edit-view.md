---
uid: mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view
title: Zkoumání metod Edit a zobrazení pro úpravy | Dokumentace Microsoftu
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/06/2019
ms.assetid: 52a4d5fe-aa31-4471-b3cb-a064f82cb791
msc.legacyurl: /mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view
msc.type: authoredcontent
ms.openlocfilehash: 75fd3a7dd55107cbdb9095d5b54b616133b4f65e
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099393"
---
<a name="examining-the-edit-methods-and-edit-view"></a>Zkoumání metod Edit a zobrazení pro úpravy
====================
Podle [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

V této části budete zkontrolujte vygenerovaný `Edit` metody akce a zobrazení kontroleru video. Ale nejprve bude trvat krátký zneužití Chcete-li datum vydání vypadat lépe. Otevřít *Models\Movie.cs* a přidejte zvýrazněné řádky je uvedeno níže:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample1.cs?highlight=2,12-14)]

Lze také nastavit jazykovou verzi datum konkrétní takto:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample2.cs?highlight=3)]

Probereme [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) v dalším kurzu. [Zobrazit](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayattribute.aspx) atribut určuje, co má být zobrazen pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atribut určuje typ dat, v tomto případě je datum, takže se nezobrazí čas informací uložených v poli. [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) chyby v prohlížeči Chrome, která vykresluje nesprávně formáty kalendářního data, je nutný atribut.

Spusťte aplikaci a přejděte `Movies` kontroleru. Podržte ukazatel myši nad **upravit** na adresu URL, která odkazuje na odkaz.

![EditLink_sm](examining-the-edit-methods-and-edit-view/_static/image1.png)

**Upravit** byl vytvořen odkaz `Html.ActionLink` metodu *Views\Movies\Index.cshtml* zobrazení:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample3.cshtml)]

![Html.ActionLink](examining-the-edit-methods-and-edit-view/_static/image2.png)

`Html` Pomocné rutiny, která je vystavena pomocí vlastnosti ve je objekt [System.Web.Mvc.WebViewPage](https://msdn.microsoft.com/library/gg402107(VS.98).aspx) základní třídy. `ActionLink` Metody pomocné rutiny usnadňuje dynamicky generovat hypertextové odkazy HTML, které jsou propojeny do metody akce v řadičích. První argument `ActionLink` metoda je text odkazu pro vykreslení (například `<a>Edit Me</a>`). Druhým argumentem je název metody akce, který má být vyvolán (v tomto případě `Edit` akci). Konečný argument je [anonymní objekt](https://weblogs.asp.net/scottgu/archive/2007/05/15/new-orcas-language-feature-anonymous-types.aspx) , který generuje data trasy (v tomto případě ID 4).

Vygenerovaný odkaz je vidět na předchozím obrázku je `http://localhost:1234/Movies/Edit/4`. Výchozí trasa (v *aplikace\_Start\RouteConfig.cs*) trvá vzor adresy URL `{controller}/{action}/{id}`. Proto převede ASP.NET `http://localhost:1234/Movies/Edit/4` do požadavku na `Edit` metody akce `Movies` řadiče s parametrem `ID` rovna 4. Zkontrolujte následující kód z *aplikace\_Start\RouteConfig.cs* souboru. [MapRoute](../../older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs.md) metoda se používá pro směrování požadavků HTTP správnou metodu kontroleru a akce a zadat volitelný parametr ID. [MapRoute](../../older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs.md) metoda také používá [HtmlHelpers](https://msdn.microsoft.com/library/system.web.mvc.htmlhelper(v=vs.108).aspx) například `ActionLink` k vygenerování adres URL daného kontroleru, metoda akce a všechna data trasy.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample4.cs?highlight=7)]

Můžete také předat parametry metody akce pomocí řetězce dotazu. Například adresa URL `http://localhost:1234/Movies/Edit?ID=3` také předá parametr `ID` 3 `Edit` metody akce `Movies` kontroleru.

![EditQueryString](examining-the-edit-methods-and-edit-view/_static/image3.png)

Otevřít `Movies` kontroleru. Dva `Edit` níže jsou uvedené metody akce.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample5.cs?highlight=19-21)]

Všimněte si, že druhá `Edit` předchází metody akce `HttpPost` atribut. Tento atribut určuje, že přetížení `Edit` metodu lze vyvolat pouze pro požadavky POST. Můžete použít `HttpGet` atribut na první upravit metodu, ale to není nezbytné vzhledem k tomu, že je výchozí hodnota. (Budeme odkazovat na metody akce, které jsou přiřazeny implicitně `HttpGet` atribut jako `HttpGet` metody.) [Svázat](https://msdn.microsoft.com/library/system.web.mvc.bindattribute(v=vs.108).aspx) atribut je jiný důležité zabezpečení mechanismus, který uchovává hackery ze over-pass-the odesílání dat do modelu. Vlastnosti by měla obsahovat pouze vazby atributu, který chcete změnit. Informace o overposting a atribut vazby v mé [overposting Poznámka k zabezpečení](https://go.microsoft.com/fwlink/?LinkId=317598). V jednoduchého modelu použité v tomto kurzu jsme závazné všechna data v modelu. [ValidateAntiForgeryToken](https://msdn.microsoft.com/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.108).aspx) atribut se používá při prevenci proti padělání žádosti a je spárovaná s `@Html.AntiForgeryToken()` v souboru zobrazení úprav (*Views\Movies\Edit.cshtml*), části je uveden níže:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample6.cshtml?highlight=9)]

`@Html.AntiForgeryToken()` generuje token proti padělání skryté formuláře, který musí odpovídat `Edit` metodu `Movies` kontroleru. Další informace o webů žádosti padělání (označované také jako XSRF nebo CSRF) ve své kurzu [prevence XSRF/CSRF v MVC](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md).

`HttpGet` `Edit` Metoda přijímá parametr ID film, vyhledá videa pomocí Entity Frameworku `Find` metoda a vrátí vybraného videa do zobrazení pro úpravy. Pokud nelze najít videa, [HttpNotFound](https://msdn.microsoft.com/library/gg453938(VS.98).aspx) je vrácena. Když systém generování uživatelského rozhraní zobrazení pro úpravy, prozkoumat `Movie` třídy a vytvořený kód pro vykreslení `<label>` a `<input>` prvky pro každou vlastnost třídy. Následující příklad ukazuje zobrazení pro úpravy, které vygeneroval systém generování uživatelského rozhraní sady visual studio:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample7.cshtml)]

Všimněte si, jak se má zobrazit šablonu `@model MvcMovie.Models.Movie` příkazu v horní části souboru, toto nastavení určuje, že zobrazení očekává, že model pro zobrazení šablony, která má být typu `Movie`.

Automaticky generovaný kód používá několik *pomocné metody* zjednodušit značka jazyka HTML. [ `Html.LabelFor` ](https://msdn.microsoft.com/library/gg401864(VS.98).aspx) Název pole, zobrazí pomocná (&quot;Title&quot;, &quot;ReleaseDate&quot;, &quot;žánr&quot;, nebo &quot;cena &quot;). [ `Html.EditorFor` ](https://msdn.microsoft.com/library/system.web.mvc.html.editorextensions.editorfor(VS.98).aspx) Vykreslí pomocné rutiny HTML `<input>` elementu. [ `Html.ValidationMessageFor` ](https://msdn.microsoft.com/library/system.web.mvc.html.validationextensions.validationmessagefor(VS.98).aspx) Pomocné rutiny zobrazí všechny zprávy ověření přidružené k této vlastnosti.

Spusťte aplikaci a přejděte */Movies* adresy URL. Klikněte na tlačítko **upravit** odkaz. V prohlížeči zobrazte zdroj stránky. Kód HTML pro element formuláře je uveden níže.

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample8.cshtml?highlight=1-2)]

`<input>` Prvky jsou v kódu HTML `<form>` elementu jehož `action` atribut je nastaven na Odeslat požadavek POST */filmy/úpravy* adresy URL. Publikuje se data formuláře na serveru při **Uložit** po kliknutí na tlačítko. Druhý řádek ukazuje skrytého [XSRF](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) token generovaných `@Html.AntiForgeryToken()` volání.

## <a name="processing-the-post-request"></a>Zpracování požadavku POST

Následující seznam ukazuje `HttpPost` verzi `Edit` metody akce.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample9.cs)]

[ValidateAntiForgeryToken](https://msdn.microsoft.com/library/system.web.mvc.validateantiforgerytokenattribute(v=vs.108).aspx) ověří atribut [XSRF](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) token generovaných `@Html.AntiForgeryToken()` volání v zobrazení.

[Vazač modelu ASP.NET MVC](https://msdn.microsoft.com/library/dd410405.aspx) vezme hodnoty odeslaného formuláře a vytvoří `Movie` objektu, který je předán jako `movie` parametru. `ModelState.IsValid` Ověřuje, že odeslaná data do formuláře je možné upravit (úpravy nebo aktualizace) `Movie` objektu. Pokud jsou data platná, data o filmech uložená `Movies` kolekce `db`(`MovieDBContext` instance). Nová data o filmech uložená v databázi voláním `SaveChanges` metoda `MovieDBContext`. Po uložení dat, kód přesměruje uživatele `Index` metody akce `MoviesController` třídu, která zobrazuje kolekce filmů, včetně pouze změny.

Jakmile ověřování na straně klienta zjistí, že hodnota pole není platná, zobrazí se chybová zpráva. Pokud jazyk JavaScript je zakázán, je zakázáno ověřování na straně klienta. Však server rozpozná odeslaných hodnot nejsou platné, a zobrazí znovu hodnot formuláře s chybovými zprávami.

Ověření je prozkoumat podrobněji dále v tomto kurzu.

`Html.ValidationMessageFor` Pomocné rutiny v *Edit.cshtml* zobrazení šablony postará o zobrazení odpovídající chybové zprávy.

![abcNotValid](examining-the-edit-methods-and-edit-view/_static/image4.png)

Všechny `HttpGet` metody podobné tvar. Dostanou video (nebo seznam objektů, v případě `Index`) a předána do zobrazení modelu. `Create` Metoda předává objekt prázdný film zobrazení pro vytváření. Všechny metody, které vytvořit, upravit, odstranit nebo jinak upravit data, proveďte `HttpPost` přetížení metody. Úpravy dat v metodě GET protokolu HTTP je bezpečnostním rizikem, jak je popsáno v příspěvku blogu [ASP.NET MVC Tip #46 – nepoužívejte odstranit odkazy, protože uživatel vytvořit bezpečnostní díry](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx). Úpravy dat v metodě GET HTTP osvědčené postupy vaší architektury a také porušuje [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer) vzor, který určuje, že požadavky GET by neměly měnit stav vaší aplikace. Jinými slovy provádění operace GET by měl být bezpečný provoz, který nemá žádné vedlejší účinky a nemění trvalá data.

## <a name="jquery-validation-for-non-english-locales"></a>k ověřování jQuery pro neanglická národní prostředí

Pokud používáte jazykovou verzi US English počítače, můžete tuto část přeskočit a přejít k dalšímu kurzu. Můžete stáhnout Globalize verzi tohoto kurzu [tady](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=16475). Vynikající kurz dvě části na mezinárodní prostředí, najdete v tématu [Nadeem na ASP.NET MVC 5 internacionalizace](http://afana.me/post/aspnet-mvc-internationalization.aspx).


> [!NOTE]
> pro podporu ověřování jQuery pro neanglická národní prostředí, které používají čárku (&quot;,&quot;) pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, musíte zahrnout *globalize.js* a konkrétní  *cultures/Globalize.cultures.js* souboru (z [ https://github.com/jquery/globalize ](https://github.com/jquery/globalize) ) a JavaScript, který chcete použít `Globalize.parseFloat`. Jiné než anglické jazykové ověřování jQuery můžete získat z NuGet. (Neinstalujte Globalize Pokud používáte anglické národní prostředí.)

1. Z **nástroje** klikněte na nabídku **Správce balíčků NuGet**a potom klikněte na tlačítko **spravovat balíčky NuGet pro řešení**.

    ![](examining-the-edit-methods-and-edit-view/_static/image5.png)
2. V levém podokně vyberte <strong>Procházet *.</strong>* (Viz následující obrázek).
3. Do vstupního pole, zadejte * Globalize **.

    ![](examining-the-edit-methods-and-edit-view/_static/image6.png) Zvolte `jQuery.Validation.Globalize`, zvolte `MvcMovie` a klikněte na tlačítko **nainstalovat**. *Scripts\jquery.globalize\globalize.js* soubor bude přidán do projektu. *Scripts\jquery.globalize\cultures\* složka bude obsahovat mnoho souborů JavaScriptu jazykovou verzi. Mějte na paměti, může trvat 5 minut k instalaci tohoto balíčku.

   Následující kód ukazuje změny souboru Views\Movies\Edit.cshtml:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample10.cshtml)]

Předcházet opakování tohoto kódu v každé zobrazení pro úpravy, můžete ho přesunout do rozložení souboru. K optimalizaci skript stáhnout, naleznete v tématu Moje kurzu [sdružování a Minifikace](../../performance/bundling-and-minification.md).

Další informace najdete v části [ASP.NET MVC 3 internacionalizace](http://afana.me/post/aspnet-mvc-internationalization.aspx) a [internacionalizace 3 ASP.NET MVC – část 2 (NerdDinner)](http://afana.me/post/aspnet-mvc-internationalization-part-2.aspx).

Jako dočasné opravu Pokud nelze získat ověření v národní prostředí, můžete vynutit počítače pro Americkou angličtinu nebo zakážete jazyka JavaScript v prohlížeči. K vynucení počítače pro Americkou angličtinu, můžete přidat element globalizace do kořenového adresáře projektů *web.config* souboru. Následující kód ukazuje elementu globalization jazyková verze nastavena na angličtinu Spojených států.

[!code-xml[Main](examining-the-edit-methods-and-edit-view/samples/sample11.xml)]

<a id="gettingstarted"></a><a id="jQueryAjaxJSON"></a> V dalším kurzu Implementujeme vyhledávací funkce.

> [!div class="step-by-step"]
> [Předchozí](accessing-your-models-data-from-a-controller.md)
> [další](adding-search.md)
