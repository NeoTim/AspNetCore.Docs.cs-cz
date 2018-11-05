---
uid: web-pages/overview/ui-layouts-and-themes/18-customizing-site-wide-behavior
title: Přizpůsobení chování v celém webu pro webové stránky ASP.NET (Razor) weby | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola vysvětluje, jak provádět celého webu nebo celou složku, nikoli pouze na stránce nastavení.
ms.author: riande
ms.date: 02/17/2014
ms.assetid: e158bed7-226f-4275-b02e-7553bd58c669
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/18-customizing-site-wide-behavior
msc.type: authoredcontent
ms.openlocfilehash: ca7c241d6e4d1e4baa581faf2bda4ed275b4e785
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021466"
---
<a name="customizing-site-wide-behavior-for-aspnet-web-pages-razor-sites"></a>Přizpůsobení chování v celém webu pro weby technologie ASP.NET webové stránky (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak vytvořit nastavení na straně serveru pro stránky na webu rozhraní ASP.NET Web Pages (Razor).
> 
> Co se dozvíte:
> 
> - Jak spustit kód, který vám umožní sady hodnoty (globální hodnot nebo nastavení pomocné rutiny) pro všechny stránky v lokalitě.
> - Jak spustit kód, který umožňuje nastavit hodnoty pro všechny stránky ve složce.
> - Spuštění kódu před a po stránka načte.
> - Jak odeslat chyby centrální chybovou stránku.
> - Postup přidání ověřování pro všechny stránky ve složce.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Služba WebMatrix 3
> - Knihovnu ASP.NET Web Helpers (balíček NuGet)
>   
> 
> V tomto kurzu funguje taky s 3 webových stránek ASP.NET a Visual Studio 2013 (nebo Visual Studio Express 2013 for Web), s výjimkou případů, které nemohou používat knihovnu ASP.NET Web Helpers.


<a id="Adding_Website_Startup_Code"></a>
## <a name="adding-website-startup-code-for-aspnet-web-pages"></a>Přidání spouštěcí kód webu pro webové stránky ASP.NET

Pro velká část kódu, který píšete v ASP.NET Web Pages jednotlivé stránky může obsahovat veškerý kód, který je potřeba pro danou stránku. Například pokud stránka odešle e-mailovou zprávu, je možné vložit jednu stránku veškerý kód pro tuto operaci. To může zahrnovat kód pro inicializaci nastavení pro odesílání e-mailu (to znamená, že pro SMTP server) a pro posílání e-mailové zprávy.

Ale v některých případech můžete chtít spouštět nějaký kód před spuštěním libovolné stránky na webu. To je užitečné pro nastavení hodnot, které lze použít kdekoli v síti (označované jako *globální hodnoty*.) Například některé pomocné rutiny vyžadují, abyste zadejte hodnoty jako nastavení e-mailu nebo klíče účtu. Může být užitečná při stávajícím nastavení do globální hodnoty.

Můžete to provést tak, že vytvoříte stránku s názvem  *\_AppStart.cshtml* v kořenové složce webu. Pokud tuto stránku existuje, spustí při prvním požadavku na libovolnou stránku na webu. Proto je vhodné místo pro spuštění kódu nastavit globální hodnoty. (Protože  *\_AppStart.cshtml* má předponu podtržítka ASP.NET nebude odesílat stránky do prohlížeče, i v případě, že uživatelé si ji vyžádat přímo.)

Následující obrázek ukazuje, jak  *\_AppStart.cshtml* stránce funguje. Když přijde žádost pro stránku, a pokud je to první požadavek pro všechny stránky na webu technologie ASP.NET nejprve zkontroluje, jestli  *\_AppStart.cshtml* stránka existuje. Pokud ano, některý kód  *\_AppStart.cshtml* stránce běhy a pak spustí požadovanou stránku.

![[image]](18-customizing-site-wide-behavior/_static/image1.jpg)

## <a name="setting-global-values-for-your-website"></a>Nastavení globální hodnoty pro váš web

1. V kořenové složce webu služby WebMatrix, vytvořte soubor s názvem  *\_AppStart.cshtml*. Soubor musí být v kořenové složce webu.
2. Nahraďte existující obsah následujícím kódem: 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample1.cshtml)]

    Uloží hodnotu v tomto kódu `AppState` slovník, který je automaticky dostupný pro všechny stránky v lokalitě. Všimněte si,  *\_AppStart.cshtml* soubor nemá v něm žádné značky. Na stránce spustí kód a pak přesměrovat na stránce, která byla původně požadována.

    > [!NOTE]
    > Buďte opatrní při uvádění kód  *\_AppStart.cshtml* souboru. Pokud dojde k nějaké chybě v kódu v  *\_AppStart.cshtml* souboru webu se nespustí.
3. V kořenové složce vytvořte novou stránku s názvem *AppName.cshtml*.
4. Nahraďte výchozí značek a kódu s následujícími možnostmi: 

    [!code-html[Main](18-customizing-site-wide-behavior/samples/sample2.html)]

    Extrahuje hodnotu ze, tento kód `AppState` objekt, který jste nastavili v  *\_AppStart.cshtml* stránky.
5. Spustit *AppName.cshtml* stránku v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.) Na stránce se zobrazí globální hodnoty. 

    ![[image]](18-customizing-site-wide-behavior/_static/image2.jpg)

<a id="Setting_Values_For_Helpers"></a>
## <a name="setting-values-for-helpers"></a>Nastavení hodnot pro pomocné rutiny

Dobře využijte pro  *\_AppStart.cshtml* je soubor k nastavení hodnot pro pomocné rutiny, které používáte ve vaší lokalitě a, které mají být inicializovány. Typické příklady jsou nastavení e-mailu pro `WebMail` pomocné rutiny a privátní a veřejné klíče pro `ReCaptcha` pomocné rutiny. V takových případech můžete nastavit hodnoty jednou  *\_AppStart.cshtml* a pak je již nastavena pro všechny stránky webu.

Tento postup ukazuje, jak nastavit `WebMail` nastavení globálně. (Další informace o používání `WebMail` pomocné rutiny, najdete v článku [přidání e-mailu na serveru ASP.NET Web Pages](../getting-started/11-adding-email-to-your-web-site.md).)

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste to ještě nepřidali.
2. Pokud ještě nemáte  *\_AppStart.cshtml* souboru, v kořenové složce webu vytvořte soubor s názvem  *\_AppStart.cshtml*.
3. Přidejte následující `WebMail` nastavení  *\_AppStart.cshtml* souboru: 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample3.cshtml?highlight=2-7)]

    Upravte následující související nastavení v kódu e-mailu:

   - Nastavte `your-SMTP-host` na název serveru SMTP, který máte přístup.
   - Nastavte `your-user-name-here` na uživatelské jméno pro účet serveru SMTP.
   - Nastavte `your-account-password` heslo pro účet serveru SMTP.
   - Nastavte `your-email-address-here` vlastní e-mailovou adresu. Toto je e-mailovou adresu, které se zpráva poslala. (Některé poskytovateli e-mailu není vám umožní zadat jiný `From` adresy a používat vaše uživatelské jméno jako `From` adresu.)

     Další informace o nastavení protokolu SMTP, naleznete v tématu [konfigurace nastavení e-mailu](https://go.microsoft.com/fwlink/?LinkID=202899#configuring_email_settings) v článku [odesílání e-mailu z webových stránek ASP.NET (Razor) stránky](https://go.microsoft.com/fwlink/?LinkID=202899) a [potíže s odesláním e-mailu](https://go.microsoft.com/fwlink/?LinkId=253001#email)v [webové stránky ASP.NET (Razor) Průvodce odstraňováním potíží](https://go.microsoft.com/fwlink/?LinkId=253001).
4. Uložit  *\_AppStart.cshtml* soubor a zavřete ho.
5. V kořenové složce webu, vytvořte novou stránku s názvem *TestEmail.cshtml*.
6. Nahraďte existující obsah následujícím kódem: 

     [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample4.cshtml)]
7. Spustit *TestEmail.cshtml* stránku v prohlížeči.
8. Vyplňte pole pošlete sami sobě e-mailovou zprávu a pak klikněte na **odeslat**.
9. Zkontrolujte si e-mail. abyste měli jistotu, že se že zobrazila zpráva.

Důležitou součástí v tomto příkladu je, že nastavení, která obvykle nezměníte –, jako je název serveru SMTP a e-mailu pověření – jsou nastaveny  *\_AppStart.cshtml* souboru. Díky tomu není nutné je znovu nastavit na každé stránce kam poslat e-mailu. (I když Pokud z nějakého důvodu potřebujete změnit tato nastavení, můžete nastavit jejich jednotlivě na stránce.) Na stránce nastavit pouze hodnoty, které obvykle změnit pokaždé, když, stejně jako příjemce a text e-mailové zprávy.

<a id="Running_Code_Before_and_After"></a>
## <a name="running-code-before-and-after-files-in-a-folder"></a>Spuštění kódu před a po soubory ve složce

Stejně jako můžete použít  *\_AppStart.cshtml* psaní kódu před spuštěním stránky na webu, můžete napsat kód, který spouští před (a po) jakékoli stránky v určité složce spusťte. To je užitečné pro takové věci, jako je například nastavení na stejné stránce rozložení pro všechny stránky ve složce nebo kontroly, který je přihlášený uživatel před spuštěním stránku ve složce.

Pro stránky zejména složky, můžete vytvořit kód do souboru s názvem  *\_PageStart.cshtml*. Následující obrázek ukazuje, jak  *\_PageStart.cshtml* stránce funguje. Když přijde žádost pro stránku, ASP.NET zkontroluje  *\_AppStart.cshtml* stránce a, který spouští. ASP.NET zkontroluje, jestli je  *\_PageStart.cshtml* stránce, a pokud ano, spustí. Pak spustí požadovanou stránku.

Uvnitř  *\_PageStart.cshtml* stránky, můžete určit, kam během zpracování, které požadujete požadovanou stránku spuštění zahrnutím `RunPage` metody. To umožňuje spuštění kódu před spuštěním požadovanou stránku a pak znovu po něm. Pokud nechcete zahrnout `RunPage`, veškerý kód v  *\_PageStart.cshtml* spuštění a požadovaná stránka automaticky spustí.

![[image]](18-customizing-site-wide-behavior/_static/image3.jpg)

Technologie ASP.NET umožňuje vytvořit hierarchii  *\_PageStart.cshtml* soubory. Můžete vložit  *\_PageStart.cshtml* soubor v kořenové složce webu a v libovolné podsložce. Při vyžádání stránky  *\_PageStart.cshtml* souboru na úrovni úplně nahoře (nejblíže kořenovém adresáři webu) spuštění, za nímž následuje  *\_PageStart.cshtml* soubor v dalším podsložku, a tak dále dolů struktura podsložky, dokud požadavek dosáhne složku obsahující požadované stránky. Po všech příslušných  *\_PageStart.cshtml* spustili soubory, spustí požadovanou stránku.

Například můžete mít následující kombinace  *\_PageStart.cshtml* soubory a *stránku Default.cshtml* souboru:

[!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample5.cshtml)]

[!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample6.cshtml)]

[!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample7.cshtml)]

Při spuštění */myfolder/default.cshtml*, zobrazí se následující:

[!code-console[Main](18-customizing-site-wide-behavior/samples/sample8.cmd)]

## <a name="running-initialization-code-for-all-pages-in-a-folder"></a>Spuštění inicializace kódu pro všechny stránky ve složce

Dobře využijte pro  *\_PageStart.cshtml* soubory, je inicializovat stránce rozložení pro všechny soubory v jedné složce.

1. V kořenové složce vytvořte novou složku s názvem *InitPages*.
2. V *InitPages* složku vašeho webu, vytvořte soubor s názvem  *\_PageStart.cshtml* a výchozích značek a kódu nahraďte následujícím kódem: 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample9.cshtml)]
3. V kořenovém adresáři webové stránky, vytvořte složku s názvem *Shared*.
4. V *Shared* složce vytvořte soubor s názvem  *\_Layout1.cshtml* a výchozích značek a kódu nahraďte následujícím kódem: 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample10.cshtml)]
5. V *InitPages* složce vytvořte soubor s názvem *Content1.cshtml* a nahraďte existující obsah následujícím kódem: 

    [!code-html[Main](18-customizing-site-wide-behavior/samples/sample11.html)]
6. V *InitPages* složku, vytvořte jiný soubor s názvem *Content2.cshtml* a nahraďte výchozí kód následujícím kódem: 

    [!code-html[Main](18-customizing-site-wide-behavior/samples/sample12.html)]
7. Spustit *Content1.cshtml* v prohlížeči. 

    ![[image]](18-customizing-site-wide-behavior/_static/image4.jpg)

    Když *Content1.cshtml* stránce spuštění,  *\_PageStart.cshtml* souboru sady `Layout` a také nastaví `PageData["MyBackground"]` na barvu. V *Content1.cshtml*, rozložení a barva.
8. Zobrazení *Content2.cshtml* v prohlížeči. 

    Rozložení je stejný, protože obě stránky použít stejnou stránku rozložení a barva inicializovány v  *\_PageStart.cshtml*.

## <a name="using-pagestartcshtml-to-handle-errors"></a>Pomocí \_PageStart.cshtml zpracování chyb

Další vhodné použít pro  *\_PageStart.cshtml* souboru je vytvoření způsob, jak zpracovat programovacích chyb (výjimek), které mohou nastat v některém *.cshtml* stránku ve složce. Tento příklad ukazuje jeden způsob, jak to provést.

1. V kořenové složce vytvořte složku s názvem *InitCatch*.
2. V *InitCatch* složku vašeho webu, vytvořte soubor s názvem  *\_PageStart.cshtml* a nahraďte existující kód a kód následujícím kódem: 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample13.cshtml)]

    V tomto kódu můžete se pokusit spustit požadované stránky explicitně voláním `RunPage` metodu `try` bloku. Pokud dojde k chybám programování v požadované stránce, kód uvnitř `catch` blok. V tomto případě kód provede přesměrování na stránku (*Error.cshtml*) a předá název souboru, ve kterém dochází k chybě jako část adresy URL. (Vytvoříte stránku za chvíli.)
3. V *InitCatch* složku vašeho webu, vytvořte soubor s názvem *Exception.cshtml* a nahraďte existující kód a kód následujícím kódem: 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample14.cshtml)]

    Pro účely tohoto příkladu co děláte na této stránce záměrně vytváří chybu při pokusu o otevření souboru databáze, která neexistuje.
4. V kořenové složce vytvořte soubor s názvem *Error.cshtml* a nahraďte existující kód a kód následujícím kódem: 

    [!code-html[Main](18-customizing-site-wide-behavior/samples/sample15.html)]

    Na této stránce, výraz `@Request["source"]` získá hodnotu z adresy URL a zobrazí ji.
5. Na panelu nástrojů klikněte na tlačítko **Uložit**.
6. Spustit *Exception.cshtml* v prohlížeči. 

    ![[image]](18-customizing-site-wide-behavior/_static/image5.jpg)

    Vzhledem k tomu, že dojde k chybě v *Exception.cshtml*,  *\_PageStart.cshtml* k přesměrování stránky *Error.cshtml* soubor, který zobrazí zprávu.

    Další informace o výjimkách, naleznete v tématu [Úvod do ASP.NET Web Pages programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=251587).

<a id="Using__PageStart.cshtml_to_Restrict_Folder_Access"></a>
## <a name="using-pagestartcshtml-to-restrict-folder-access"></a>Pomocí \_PageStart.cshtml omezit přístup ke složkám

Můžete také použít  *\_PageStart.cshtml* souboru k omezení přístupu ke všem souborům ve složce.

1. V nástroji WebMatrix, vytvoření nového webu pomocí **šablony z webu** možnost.
2. Z dostupných šablon, vyberte **Starter Site**.
3. V kořenové složce vytvořte složku s názvem *AuthenticatedContent*.
4. V *AuthenticatedContent* složce vytvořte soubor s názvem  *\_PageStart.cshtml* a nahraďte existující kód a kód následujícím kódem: 

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample16.cshtml)]

    Kód spustí tím, že všechny soubory ve složce ukládat do mezipaměti. (To je vyžadováno pro scénáře, jako jsou veřejné počítače, kde nechcete, aby jeden uživatel stránky v mezipaměti k dispozici pro dalšího uživatele.) V dalším kroku kód určuje, zda uživatel je přihlášený k webu před zobrazením kterákoli ze stránek ve složce. Pokud uživatel není přihlášený, kód se přesměruje na přihlašovací stránku. Na přihlašovací stránku můžete vrátit uživatele na stránce, která byla původně požadována Pokud zahrnete hodnotu řetězce dotazu s názvem `ReturnUrl`.
5. Vytvořit novou stránku *AuthenticatedContent* složku s názvem *Page.cshtml*.
6. Nahraďte výchozí značka následujícími způsoby:  

    [!code-cshtml[Main](18-customizing-site-wide-behavior/samples/sample17.cshtml)]
7. Spustit *Page.cshtml* v prohlížeči. Kód vás přesměruje na přihlašovací stránku. Musíte zaregistrovat před přihlášením. Po zaregistrované a přihlášení, můžete přejít na stránku a zobrazit jeho obsah.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky

[Úvod do programování pomocí syntaxe Razor rozhraní ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkID=251587)
