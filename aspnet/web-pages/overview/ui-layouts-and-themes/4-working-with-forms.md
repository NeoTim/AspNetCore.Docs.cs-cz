---
uid: web-pages/overview/ui-layouts-and-themes/4-working-with-forms
title: Práce s formuláři HTML na webech ASP.NET Web Pages (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Formulář je část dokumentu HTML místo, kam dáte ovládací prvky vstupu uživatele, jako jsou textová pole, zaškrtávací políčka, přepínače a rozevírací seznamy. Použití formulářů co...
ms.author: riande
ms.date: 02/10/2014
ms.assetid: f3f4b8c8-e8f6-4474-ad94-69228a6c01ee
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/4-working-with-forms
msc.type: authoredcontent
ms.openlocfilehash: de700055168f9d17167c82afe836b546160c6e91
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021612"
---
<a name="working-with-html-forms-in-aspnet-web-pages-razor-sites"></a>Práce s formuláři HTML v lokalitách rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje, jak zpracovat formuláře HTML (pomocí tlačítka a textová pole) při práci na webu rozhraní ASP.NET Web Pages (Razor).
> 
> **Co se dozvíte:** 
> 
> - Jak vytvořit formuláře HTML.
> - Jak číst uživatelského vstupu z formuláře.
> - Jak ověřit vstup uživatele.
> - Postup při obnovení hodnot formuláře po odeslání stránky.
> 
> Toto jsou ASP.NET programování koncepty představenými v tomto článku:
> 
> - `Request` Objektu.
> - Ověření vstupu.
> - Kódování HTML.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


## <a name="creating-a-simple-html-form"></a>Vytvoření jednoduchého formuláře HTML

1. Vytvoření nového webu.
2. V kořenové složce vytvořit webovou stránku s názvem *Form.cshtml* a zadejte následující kód:

    [!code-html[Main](4-working-with-forms/samples/sample1.html)]
3. Spusťte v prohlížeči stránku. (V nástroji WebMatrix, v **soubory** pracovní prostor, klikněte pravým tlačítkem na soubor a pak vyberte **spustit v prohlížeči**.) Jednoduchý formulář s tři vstupní pole a **odeslat** je zobrazeno tlačítko.

    ![Snímek obrazovky, formuláře s tři textová pole.](4-working-with-forms/_static/image1.jpg)

    V tomto okamžiku, pokud kliknete **odeslat** tlačítko, nic se nestane. Aby formuláři užitečná, budete muset přidat nějaký kód, který se spustí na serveru.

## <a name="reading-user-input-from-the-form"></a>Čtení uživatelského vstupu z formuláře

Pro zpracování formuláře, přidáte kód, který čte hodnoty zadané pole a poté s nimi. Tento postup ukazuje, jak číst pole a uživatelský vstup, zobrazí na stránce. (V produkční aplikace, obvykle provedete zajímavější věci s uživatelským vstupem. Můžete to udělat v článku o práci s databázemi.)

1. V horní části *Form.cshtml* soubor, zadejte následující kód:

    [!code-cshtml[Main](4-working-with-forms/samples/sample2.cshtml)]

    Když uživatel poprvé požádá stránky, zobrazí se pouze prázdný formulář. Uživatel (což bude vám) vyplní ve formuláři a poté klikne na tlačítko **odeslat**. Odešle (příspěvků) uživatelský vstup k serveru. Ve výchozím nastavení, že se odesílá na stejnou stránku (konkrétně *Form.cshtml*).

    Při odesílání stránky tentokrát zadané hodnoty se zobrazí přímo nad formuláře:

    ![Snímek obrazovky zobrazující hodnoty, které jste zadali na stránku.](4-working-with-forms/_static/image2.jpg)

    Prohlédněte si kód pro stránku. Nejprve pomocí `IsPost` metodou ke zjištění, zda se odeslání stránky &#8212; to znamená, zda uživatel kliknutí **odeslat** tlačítko. Pokud je příspěvek, `IsPost` vrací hodnotu true. Toto je standardní způsob na webových stránkách ASP.NET k určení, zda pracujete s počáteční požadavek (požadavek GET) nebo zpětného odeslání (požadavek POST). (Další informace o GET a POST, najdete v části bočního panelu "HTTP GET a POST a IsPost vlastnost" v [Úvod do ASP.NET Web Pages programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890#SB_HttpGetPost).)

    Teď získáte hodnoty, které uživatel vyplněno z `Request.Form` objektu a umístit je do proměnné na později. `Request.Form` Objekt obsahuje všechny hodnoty, které byly odeslány na stránce každé identifikované klíčem. Klíč je ekvivalentní k `name` atribut z pole formuláře, který chcete číst. Například pro čtení `companyname` pole (textové pole), můžete použít `Request.Form["companyname"]`.

    Formuláře, hodnoty jsou uloženy v `Request.Form` objektu jako řetězce. Proto když je nutné pracovat s hodnotou jako číslo nebo datum nebo jiný typ, budete muset převést z řetězce na daný typ. V tomto příkladu `AsInt` metodu `Request.Form` se používá k převodu hodnoty pole zaměstnanci (který obsahuje počet zaměstnanců) na celé číslo.
2. Spustit v prohlížeči stránku, vyplňte pole formuláře a klikněte na tlačítko **odeslat**. Na stránce se zobrazí hodnoty, které jste zadali.

> [!TIP] 
> 
> <a id="SB_HTMLEncoding"></a>
> ### <a name="html-encoding-for-appearance-and-security"></a>Kódování pro vzhled a zabezpečení v jazyce HTML
> 
> HTML obsahuje speciální znaky, jako je použití `<`, `>`, a `&`. Pokud se tyto speciální znaky nezobrazí, kde se očekává, že poškodí vzhled a funkce webové stránky. Třeba v prohlížeči interpretuje `<` znak (Pokud je následované mezerou) jako začátek elementu HTML, jako je třeba `<b>` nebo `<input ...>`. Pokud v prohlížeči nerozpozná elementu, jednoduše zahodí řetězec, který začíná `<` dokud nedosáhne něco, která znovu rozpozná. Samozřejmě to může způsobit nějaké divné vykreslování na stránce.
> 
> Kódování HTML nahradí kód, který prohlížeče interpretovat jako správný symbol těchto vyhrazených znaků. Například `<` nahradí znak `&lt;` a `>` nahradí znak `&gt;`. Prohlížeč zobrazí tyto řetězce pro nahrazování jako znaky, které chcete zobrazit.
> 
> Je vhodné použít kódování vždy zobrazí řetězce v jazyce HTML (vstup), že jste získali od uživatele. Pokud to neuděláte, uživatel může pokusit o získání webové stránky ke spuštění škodlivých skriptů nebo provádět další činnosti, která ohrožuje zabezpečení vašeho webu nebo který je právě není to, co chcete. (To je zvlášť důležité, pokud přijímají uživatelský vstup, uložte ho někde a zobrazit později &#8212; , jako například komentář blogu, uživatel kontrolovat nebo něco jako, který.)
> 
> Aby se zabránilo tyto problémy, rozhraní ASP.NET Web Pages automaticky umístí kódování HTML jakýkoli text obsahu, který jste výstup z vašeho kódu. Například při zobrazení obsahu proměnné nebo výraz, který používá kód, jako `@MyVar`, rozhraní ASP.NET Web Pages Galerie automaticky kóduje výstup.


## <a name="validating-user-input"></a>Ověřování uživatelského vstupu

Uživatelé dělat chyby. Požádejte ho, chcete-li vyplnit pole a zapomenou, nebo požádejte ho, chcete-li zadat počet zaměstnanců a jejich místo toho zadejte název. Pokud chcete mít jistotu, že formulář bylo vyplněno správně předtím, než ho zpracovat, ověřit vstup uživatele.

Tento postup ukazuje, jak ověřit všechna pole tři formuláře, abyste měli jistotu, že uživatel neměli nechat prázdné. Můžete také zkontrolujte, že hodnota počtu zaměstnanců číslo. Pokud chyby existují, vám zobrazí chybová zpráva, která uživateli říká, jaké hodnoty úspěšně ověření.

1. V *Form.cshtml* souboru, první blok kódu nahraďte následujícím kódem: 

    [!code-cshtml[Main](4-working-with-forms/samples/sample3.cshtml)]

    Chcete-li ověření vstupu uživatele, použijte `Validation` pomocné rutiny. Zaregistrovat povinná pole voláním `Validation.RequireField`. Zaregistrovat jiné typy ověřování voláním `Validation.Add` a určením pole, které chcete ověřit a typ ověření, který má provést.

    Při spuštění stránky technologie ASP.NET provede všemi ověřovacími za vás. Výsledky můžete zkontrolovat, že volání `Validation.IsValid`, která vrací hodnotu true, pokud vše, co předaný a false, pokud žádné pole se nepovedlo ověřit. Obvykle volání `Validation.IsValid` před provedením jakékoli zpracování na vstupu uživatele.
2. Aktualizace `<body>` element tak, že přidáte tři volání `Html.ValidationMessage` metoda takto:

    [!code-cshtml[Main](4-working-with-forms/samples/sample4.cshtml?highlight=8,13,18)]

    K zobrazení chybových zpráv ověření, můžete volat Html.`ValidationMessage` a předejte mu název pole, které chcete zprávu pro.
3. Spuštění stránky. Ponechte pole prázdné a klikněte na tlačítko **odeslat**. Se zobrazí chybové zprávy.

    ![Snímek obrazovky zobrazující zobrazí chybové zprávy, pokud není uživatelský vstup projít ověřením.](4-working-with-forms/_static/image3.jpg)
4. Přidat do řetězce (například "ABC") **Employee Count** pole a klikněte na tlačítko **odeslat** znovu. Tentokrát se zobrazí chyba, která označuje, že řetězec není ve správném formátu, konkrétně, celé číslo.

    ![Snímek obrazovky zobrazující zobrazí chybové zprávy, pokud uživatelé zadat řetězec pro pole zaměstnanci.](4-working-with-forms/_static/image4.jpg)

ASP.NET Web Pages poskytuje další možnosti pro ověřování uživatelských vstupů, včetně možnost automaticky provádět ověření pomocí klientského skriptu, aby uživatelé získali okamžitou zpětnou vazbu v prohlížeči. Najdete v článku [další prostředky](#Additional_Resources) části později pro další informace.

## <a name="restoring-form-values-after-postbacks"></a>Obnovení po postbacků hodnot formuláře

Při testování na stránce v předchozí části jste si možná všimli, pokud došlo k chybě ověřování, všechno, co že jste zadali (nejen neplatná data) byla pryč a bylo nutné znovu zadat hodnoty pro všechna pole. To znázorňuje důležité vzít: při odeslání stránky, zpracovat je a pak vykreslení stránky, na stránce je znovu vytvořit úplně od začátku. Jak už jste viděli, to znamená, že všechny hodnoty, které byly na stránce při odeslání budou ztraceny.

Problém můžete vyřešit jednoduše, ale. Máte přístup k hodnotám, které byly předány (v `Request.Form` objektu, takže můžete přejít k vyplnění tyto hodnoty do polí formuláře při vykreslování stránky.

1. V *Form.cshtml* souboru, nahradí `value` atributy `<input>` prvky pomocí `value` atribut.: 

    [!code-cshtml[Main](4-working-with-forms/samples/sample5.cshtml?highlight=13,19,25)]

    `value` Atribut `<input>` prvky byla nastavena na hodnotu pole z dynamicky načíst `Request.Form` objektu. Při prvním požadavku na stránku hodnoty v `Request.Form` objektu jsou všechny prázdné. To je v pořádku, protože díky tomu je formulář prázdný.
2. Spustit v prohlížeči stránku, přejít k vyplnění polí formuláře nebo ji ponechat prázdnou a klikněte na tlačítko **odeslat**. Zobrazí se stránka, která zobrazuje zadané hodnoty.

    ![forms-5](4-working-with-forms/_static/image5.jpg)

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky

- [1,001 způsoby, jak získat vstup z webových uživatelů](https://msdn.microsoft.com/library/ms971057.aspx)
- [Pomocí formulářů a zpracování vstupu uživatele](https://msdn.microsoft.com/library/ms525182(VS.90).aspx)
- [Ověřování uživatelských vstupů na webech s webovými stránkami ASP.NET](https://go.microsoft.com/fwlink/?LinkId=253002)
- [Pomocí automatické dokončování ve formulářích HTML](https://msdn.microsoft.com/library/ms533032(VS.85).aspx)
