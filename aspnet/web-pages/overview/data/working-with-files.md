---
uid: web-pages/overview/data/working-with-files
title: Práce se soubory na webu technologie ASP.NET Web Pages (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola vysvětluje, jak číst, zapisovat, připojit, odstranit a nahrávání souborů.
ms.author: riande
ms.date: 02/20/2014
ms.assetid: eee916e4-ba4c-439a-a24e-68df7d45a569
msc.legacyurl: /web-pages/overview/data/working-with-files
msc.type: authoredcontent
ms.openlocfilehash: 8b9176cb88f6e460fe5494167d4a5880456530aa
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021557"
---
<a name="working-with-files-in-an-aspnet-web-pages-razor-site"></a>Práce se soubory na webu rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak číst, zapisovat, připojit, odstranit a nahrát soubory na webu rozhraní ASP.NET Web Pages (Razor).
> 
> > [!NOTE]
> > Pokud chcete nahrání imagí a manipulaci s nimi (například překlopit nebo změnit jejich velikost), najdete v článku [práce s obrázky na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202897).
> 
> 
> **Co se dozvíte:** 
> 
> - Jak vytvořit textový soubor a zapsat data do ní.
> - Postup připojení dat k existujícímu souboru.
> - Jak ke čtení souboru a zobrazení z něj.
> - Jak odstranit soubory z webu.
> - Jak chcete, aby uživatelé nahrát jeden soubor nebo více souborů.
> 
> Toto jsou ASP.NET programování funkcí představených v následujícím článku:
> 
> - `File` Objektu, který poskytuje způsob, jak spravovat soubory.
> - `FileUpload` Pomocné rutiny.
> - `Path` Objektu, který poskytuje metody, které umožňují pracovat s názvy souborů a cestu.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Služba WebMatrix 2
>   
> 
> V tomto kurzu funguje taky pomocí služby WebMatrix 3.


<a id="Creating_a_Text_File"></a>
## <a name="creating-a-text-file-and-writing-data-to-it"></a>Textový soubor a zapíše Data do ní

Kromě použití databázi na vašem webu, můžete pracovat se soubory. Můžete například použít textových souborů jako jednoduchý způsob, jak ukládat data z lokality. (Se někdy označuje jako textový soubor, který se používá k ukládání dat *plochého souboru*.) Textové soubory mohou být v různých formátech, jako je třeba *.txt*, *.xml*, nebo *CSV* (hodnoty oddělené čárkami).

Pokud chcete uložit data do textového souboru, můžete použít `File.WriteAllText` metoda a vybrat soubor k vytvoření a data do ní zapisovat. V tomto postupu vytvoříte stránku, která obsahuje jednoduchý formulář s třemi `input` elementy (křestní jméno, příjmení a e-mailovou adresu) a **odeslat** tlačítko. Jakmile uživatel formulář odešle, budete ukládat uživatelský vstup do textového souboru.

1. Vytvořte novou složku s názvem *aplikace\_Data*, pokud již neexistuje.
2. V kořenovém adresáři vašeho webu, vytvořte nový soubor s názvem *UserData.cshtml*.
3. Nahraďte existující obsah následujícím kódem: 

    [!code-cshtml[Main](working-with-files/samples/sample1.cshtml)]

    Značka jazyka HTML vytvoří se tři textová pole formuláře. V kódu, můžete použít `IsPost` a určí, zda byla odeslána na stránce před zahájením zpracování.

    Je první úkol k získání vstupu uživatele a přiřaďte ho k proměnné. Kód zřetězí hodnoty samostatné proměnných do jednoho řetězce oddělených čárkami, které se pak ukládá do jiné proměnné. Všimněte si, že oddělovací čárka je řetězec obsažený v uvozovkách (","), protože doslova vkládáte čárkou do velké řetězce, kterou vytváříte. Na konci data, která zřetězit dohromady, přidáte `Environment.NewLine`. Tím se přidá konec řádku (znak nového řádku). Co vytváříte s tomuto zřetězení je řetězec, který vypadá takto:

    [!code-css[Main](working-with-files/samples/sample2.css)]

    (S konec řádku neviditelná po uplynutí.)

    Pak vytvořte proměnnou (`dataFile`), která obsahuje umístění a název souboru pro uložení dat v. Nastavení umístění vyžaduje některé speciální zacházení. Ve službě websites, je špatný postup najdete v kódu absolutní cesty, jako je *C:\Folder\File.txt* pro soubory na webovém serveru. Pokud se přesune na webu budou chybné absolutní cestu. Kromě toho hostované lokality (na rozdíl od svého počítače) obvykle ještě neznáte co je správná cesta při psaní kódu.

    Někdy ale (jako je teď pro zápis do souboru) nutné úplnou cestu. Řešení, je použít `MapPath` metodu `Server` objektu. Vrátí úplnou cestu na váš web. K získání cesty pro kořenové složky webu, můžete uživatele `~` – operátor (k represen vaší lokality virtuální kořenové) k `MapPath`. (Můžete také předat název podsložky, jako je *~/App\_Data /*, k získání cesty k této podsložce.) Pak lze zřetězit Další informace, na kterou metoda vrátí Chcete-li vytvořit úplnou cestu. V tomto příkladu přidáte název souboru. (Další informace o tom, jak pracovat s cestami k souborům a složkám v [Úvod do ASP.NET Web Pages programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=195205#ID_WorkingWithFileAndFolderPaths).)

    Soubor je uložen v *aplikace\_Data* složky. Tato složka je zvláštní v technologii ASP.NET, který se používá k ukládání dat souborů, jak je popsáno v [Úvod k práci s databází na webech ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=195209).

    `WriteAllText` Metodu `File` objekt zapíše data do souboru. Tato metoda přebírá dva parametry: název (s cestou) k zápisu do souboru a skutečná data k zápisu. Všimněte si, že název prvního parametru má `@` znak jako předponu. To říká technologie ASP.NET, zadáváte doslovný řetězec literálu a že znaky, například "/" neměly by být vykládány speciálním způsobem. (Další informace najdete v tématu [Úvod do ASP.NET webové programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=195205#ID_WorkingWithFileAndFolderPaths).)

    > [!NOTE]
    > Aby se váš kód pro uložení souborů v *aplikace\_Data* složce aplikace potřebuje pro čtení i zápis oprávnění pro tuto složku. Ve svém vývojovém počítači to není obvykle problém. Při publikování webu poskytovatele hostingu webový server, může však muset explicitně nastavena patřičná oprávnění. Pokud tento kód spustit na serveru pro poskytovatele hostingu a dojde k chybám, obraťte se na poskytovatele hostingu a zjistěte, jak nastavit tato oprávnění.

- Spuštění stránky v prohlížeči. 

    ![](working-with-files/_static/image1.jpg)
- Zadejte hodnoty do polí a potom klikněte na tlačítko **odeslat**.
- Zavřete prohlížeč.
- Vraťte se do projektu a aktualizujte zobrazení.
- Otevřít *data.txt* souboru. Data, která jste odeslali ve formuláři je v souboru. 

    ![[image]](working-with-files/_static/image2.jpg)
- Zavřít *data.txt* souboru.

<a id="Appending_Data"></a>
## <a name="appending-data-to-an-existing-file"></a>Připojení dat k existujícímu souboru

V předchozím příkladu jste použili `WriteAllText` vytvořit textový soubor, který má pouze jednu část dat v něm. Pokud volání metody znovu a předat stejný název souboru, je úplně přepsán existující soubor. Ale po vytvoření souboru často chcete přidat nová data na konec souboru. Můžete provést, že při použití `AppendAllText` metodu `File` objektu.

1. Na webu vytvořte kopii *UserData.cshtml* a pojmenujte kopie *UserDataMultiple.cshtml*.
2. Nahraďte blok kódu před otevírací `<!DOCTYPE html>` značka s následující blok kódu: 

    [!code-cshtml[Main](working-with-files/samples/sample3.cshtml)]

    Tento kód obsahuje jednu změnu z předchozího příkladu. Namísto použití `WriteAllText`, používá `the AppendAllText` metody. Metody jsou podobné, s tím rozdílem, že `AppendAllText` přidá data na konec souboru. Stejně jako u `WriteAllText`, `AppendAllText` vytvoří soubor, pokud ještě neexistuje.
3. Spuštění stránky v prohlížeči.
4. Zadejte hodnoty pro pole a potom klikněte na tlačítko **odeslat**.
5. Přidání dalších dat a formulář znovu odešlete.
6. Vraťte se do vašeho projektu, klikněte pravým tlačítkem na složku projektu a pak klikněte na tlačítko **aktualizovat**.
7. Otevřít *data.txt* souboru. Nyní obsahuje nová data, která jste právě zadali. 

    ![[image]](working-with-files/_static/image3.jpg)

<a id="Reading_and_Displaying_Data"></a>
## <a name="reading-and-displaying-data-from-a-file"></a>Čtení a zobrazení dat ze souboru

I v případě, že není nutné zapsat data do textového souboru, pravděpodobně někdy musíte přečíst data z jednoho. K tomuto účelu můžete znovu použít `File` objektu. Můžete použít `File` objektu určeného ke čtení každý řádek zvlášť (oddělené symbolem zalomení řádků) nebo pro čtení bez ohledu na to, jak rozdělíme jednotlivé položky.

Tento postup ukazuje, jak číst a zobrazení dat, který jste vytvořili v předchozím příkladu.

1. V kořenovém adresáři vašeho webu, vytvořte nový soubor s názvem *DisplayData.cshtml*.
2. Nahraďte existující obsah následujícím kódem: 

    [!code-cshtml[Main](working-with-files/samples/sample4.cshtml)]

    Kód se spustí při čtení souboru, který jste vytvořili v předchozím příkladu do proměnné s názvem `userData`, pomocí volání této metody:

    [!code-css[Main](working-with-files/samples/sample5.css)]

    Kód k tomu je uvnitř `if` příkazu. Pokud chcete ke čtení souboru, je vhodné použít `File.Exists` metoda nejprve určit, zda soubor není k dispozici. Kód také zkontroluje, zda soubor je prázdný.

    Těla stránky obsahuje dva `foreach` smyčky, jeden vnořit do druhé. Vnější `foreach` smyčky získá jeden řádek v čase z datového souboru. V tomto případě jsou řádky určené konce řádků v souboru &#8212; jednotlivých datových položek je na samostatném řádku. Vnější smyčka vytvoří novou položku (`<li>` element) uvnitř uspořádaného seznamu (`<ol>` element).

    Vnitřní smyčku rozdělí každý řádek dat do položky (pole) použitím čárky jako oddělovače. (Na základě předchozího příkladu, to znamená, že každý řádek obsahuje tři pole &#8212; křestní jméno, příjmení a e-mailovou adresu, každé oddělené čárkou.) Také vytvoří vnitřní cykly `<ul>` pro každé pole v řádku dat položky seznamu a zobrazí jeden seznam.

    Kód ukazuje, jak se používají dva datové typy, pole a `char` datového typu. Pole je povinné, protože `File.ReadAllLines` metoda vrátí data jako pole. `char` Datový typ je povinné, protože `Split` vrátí metoda `array` ve kterém každý prvek je typu `char`. (Informace o polích naleznete v tématu [Úvod do ASP.NET webové programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890#ID_CollectionsAndObjects).)
3. Spuštění stránky v prohlížeči. Zobrazí se data, která jste zadali pro předchozí příklady. 

    ![[image]](working-with-files/_static/image4.jpg)

> [!TIP] 
> 
> **Zobrazení dat ze souboru CSV aplikace Microsoft Excel**
> 
> Aplikace Microsoft Excel můžete použít k uložení dat obsažených v tabulce jako soubor CSV (*CSV* souboru). Když použijete, je soubor uložen ve formátu prostého textu, není ve formátu aplikace Excel. Každý řádek v tabulce jsou oddělené oddělovačem zalomení řádku textového souboru a jednotlivých datových položek je oddělit je čárkami. Kód zobrazený v předchozím příkladu můžete použít ke čtení Excelového souboru CSV jenom tak, že změníte název datového souboru ve vašem kódu.


<a id="Deleting_Files"></a>
## <a name="deleting-files"></a>Odstraňování souborů

Chcete-li odstranit soubory z vašeho webu, můžete použít `File.Delete` metody. Tento postup ukazuje, jak umožnit uživatelům odstranit bitovou kopii (*.jpg* souboru) ze *imagí* složku v případě, že znáte název souboru.

> [!NOTE] 
> 
> **Důležité** do produkční webové stránky, obvykle omezíte kdo má povoleno provádět změny na data. Informace o tom, jak nastavit členství a způsoby, jak autorizovat uživatele k provádění úloh na webu, naleznete v tématu [přidání zabezpečení a s členstvím na server webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202904).


1. Na webu vytvořte podsložku s názvem *image*.
2. Zkopírujte jeden nebo více *.jpg* soubory do *imagí* složky.
3. V kořenovém adresáři webu vytvořte nový soubor s názvem *FileDelete.cshtml*.
4. Nahraďte existující obsah následujícím kódem: 

    [!code-cshtml[Main](working-with-files/samples/sample6.cshtml)]

    Tato stránka obsahuje formulář, kde uživatelé zadávají název souboru obrázku. Potom nezadávejte *.jpg* příponu názvu souboru; tak, že omezíte název souboru tímto způsobem, budete nápovědy zabraňuje uživatelům v odstranění libovolné soubory ve vaší lokalitě.

    Kód čte název souboru, že uživatel zadá a pak vytvoří úplnou cestu. K vytvoření cesty, tento kód použije aktuální cesta k webu (vrácené `Server.MapPath` metoda), *imagí* název složky, název, který má zadaný uživatel a ".jpg" jako řetězcový literál.

    Odstranit soubor kód volá `File.Delete` metoda, předají se jí úplnou cestu, která se právě vytvořena. Kód na konci značky zobrazí potvrzovací zpráva, že soubor byl odstraněn.
5. Spuštění stránky v prohlížeči. 

    ![[image]](working-with-files/_static/image5.jpg)
6. Zadejte název souboru, který se odstranit a potom klikněte na tlačítko **odeslat**. Pokud soubor byl odstraněn, název souboru se zobrazí v dolní části stránky.

<a id="Letting_Users_Upload_a_File"></a>
## <a name="letting-users-upload-a-file"></a>S informacemi uživatelé nahrání souboru

`FileUpload` Pomocné rutiny, umožňuje uživatelům odeslat soubory na váš web. Následující postup ukazuje, jak umožnit uživatelům nahrát jeden soubor.

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste ho nepřidali dříve.
2. V *aplikace\_Data* složku, vytvořte novou složku a pojmenujte ho *UploadedFiles*.
3. V kořenovém adresáři vytvořte nový soubor s názvem *FileUpload.cshtml*.
4. Nahraďte existující obsah na stránce s následujícími možnostmi: 

    [!code-cshtml[Main](working-with-files/samples/sample7.cshtml)]

    Používá tělo části stránky `FileUpload` pomocná rutina pro vytvoření nahrávání pole a tlačítka, které už pravděpodobně znáte:

    ![[image]](working-with-files/_static/image6.jpg)

    Vlastnosti, které jste nastavili pro `FileUpload` pomocné rutiny určit, který má být jediné pole pro soubor k nahrání a, který má tlačítko pro odeslání ke čtení **nahrát**. (Další pole přidáte později v tomto článku.)

    Když uživatel klepne **nahrát**, kód v horní části stránky načte soubor a uloží jej. `Request` Také obsahuje objekt, který obvykle použijete k získání hodnoty z pole formuláře `Files` pole, které obsahuje soubor (nebo soubory), které byly odeslány. Můžete získat jednotlivé soubory z konkrétní pozici v poli &#8212; například chcete-li získat první odeslaný soubor, získejte `Request.Files[0]`, chcete-li získat druhý soubor, dostanete `Request.Files[1]`, a tak dále. (Nezapomeňte, že v programování, počítací obvykle začíná od nuly).

    Při načtení nahraného souboru je umístíte do proměnné (tady `uploadedFile`) tak, že jste s ním pracovat. K určení názvu uloženého souboru, vám stačí získat jeho `FileName` vlastnost. Nicméně pokud uživatel nahraje soubor, `FileName` obsahuje původní jméno uživatele, která zahrnuje celou cestu. To může vypadat takto:

    *C:\Users\Public\Sample.txt*

    Nechcete všechny tyto informace cestu, protože to je cesta na počítači uživatele, ne pro váš server. Je vhodné skutečného názvu souboru (*ukázka.txt*). Můžete odstranit pouze soubor pomocí cesty s použitím `Path.GetFileName` metoda takto:

    [!code-csharp[Main](working-with-files/samples/sample8.cs)]

    `Path` Objektu je nástroj, který má několik metod následujícím způsobem, který slouží k odstranění cesty, kombinace cesty a tak dále.

    Jakmile jste získali názvu uloženého souboru, můžete vytvořit novou cestu pro místo ukládání nahraného souboru na vašem webu. V takovém případě můžete kombinovat `Server.MapPath`, názvy složek (*aplikace\_Data/UploadedFiles*) a název souboru nově odřapíkovaného vytvořit novou cestu. Potom můžete zavolat nahraný soubor `SaveAs` metoda ve skutečnosti soubor uložte.
5. Spuštění stránky v prohlížeči. 

    ![[image]](working-with-files/_static/image7.jpg)
6. Klikněte na tlačítko **Procházet** a potom vyberte soubor k odeslání. 

    ![[image]](working-with-files/_static/image8.jpg)

    Textové pole vedle **Procházet** tlačítko bude obsahovat umístění a cesta k souboru.

    ![[image]](working-with-files/_static/image9.jpg)
7. Klikněte na tlačítko **nahrát**.
8. Na webu, klikněte pravým tlačítkem na složku projektu a pak klikněte na tlačítko **aktualizovat**.
9. Otevřít *UploadedFiles* složky. Nahraný soubor je ve složce. 

    ![[image]](working-with-files/_static/image10.jpg)

<a id="Letting_Users_Upload_Multiple_Files"></a>
## <a name="letting-users-upload-multiple-files"></a>Umožňuje uživatelům odeslat více souborů

V předchozím příkladu můžete umožnit uživatelům nahrát jeden soubor. Ale můžete použít `FileUpload` pomocné rutiny pro nahrání více souborů najednou. To je užitečné pro scénáře, jako jsou nahrávání fotografií, kde je únavné, nahrát jeden soubor současně. (Informace o nahrávání fotografií v [práce s obrázky na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202897).) Tento příklad ukazuje, jak chcete, aby uživatelům odeslat dvě současně, ale stejný postup můžete použít k nahrání a víc než.

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste tak již neučinili.
2. Vytvoří novou stránku s názvem *FileUploadMultiple.cshtml*.
3. Nahraďte existující obsah na stránce s následujícími možnostmi:  

    [!code-cshtml[Main](working-with-files/samples/sample9.cshtml)]

    V tomto příkladu `FileUpload` pomocné rutiny v těle stránky je ve výchozím nastavení nakonfigurované a umožnit nahrát dva soubory. Protože `allowMoreFilesToBeAdded` je nastavena na `true`, pomocné rutiny vykreslí odkaz, který umožní uživateli přidat více polí nahrávání:

    ![[image]](working-with-files/_static/image11.jpg)

    Zpracovat soubory, které uživatel odešle kód používá stejný základní postup, který jste použili v předchozím příkladu &#8212; načtení souboru z `Request.Files` a pak ho uložte. (Včetně různé věci budete muset udělat, abyste získali správný název souboru a cestu.) Inovace této doby je, že uživatel může být nahrání více souborů a mnoho neznáte. Chcete-li zjistit, můžete získat `Request.Files.Count`.

    S tímto číslem v ručně, můžete můžete projít `Request.Files`, pak načíst každý soubor a uložte ho. Pokud chcete smyčku do známého počtu opakování v kolekci, můžete použít `for` smyčky, například takto:

    [!code-csharp[Main](working-with-files/samples/sample10.cs)]

    Proměnná `i` je pouze dočasné čítač, který přejde od nuly do libovolné horní limit nastavíte. V tomto případě horní limit je počet souborů. Ale protože čítač začíná na nule, jako je typické pro počítání scénáře v technologii ASP.NET, horní mez ve skutečnosti je jeden menší než počet souborů. (Pokud tři soubory jsou odeslány, počet je 0 až 2.)

    `uploadedCount` Proměnné celkový počet zpracovaných položek všech souborů, které se úspěšně nahrála a uložit. Tento kód účty pro možnost očekávaný soubor nemusí podařit k odeslání.
4. Spuštění stránky v prohlížeči. Prohlížeč zobrazí na stránce a jeho dvou nahrávání pole.
5. Vyberte dva soubory k nahrání.
6. Klikněte na tlačítko **přidat jiný soubor**. Na stránce se zobrazí nové pole nahrávání. 

    ![[image]](working-with-files/_static/image12.jpg)
7. Klikněte na tlačítko **nahrát**.
8. Na webu, klikněte pravým tlačítkem na složku projektu a pak klikněte na tlačítko **aktualizovat**.
9. Otevřít *UploadedFiles* složky úspěšně odeslané soubory.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


[Práce s obrázky na webu rozhraní ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=202897)

[Export do souboru CSV](https://msdn.microsoft.com/library/ms155919.aspx)
