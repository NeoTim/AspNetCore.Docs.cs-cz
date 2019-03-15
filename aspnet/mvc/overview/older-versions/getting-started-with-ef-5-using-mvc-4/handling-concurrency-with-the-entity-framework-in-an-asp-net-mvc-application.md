---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
title: Ošetření souběžnosti se sadou Entity Framework v aplikaci ASP.NET MVC (7 10) | Dokumentace Microsoftu
author: tdykstra
description: Contoso University ukázkovou webovou aplikaci ukazuje postup vytvoření aplikace ASP.NET MVC 4 pomocí Entity Framework 5 Code First a sady Visual Studio...
ms.author: riande
ms.date: 07/30/2013
ms.assetid: b83f47c4-8521-4d0a-8644-e8f77e39733e
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 5016c08b94ff6eff65cad8a8a01f27c50381bc8a
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978506"
---
<a name="handling-concurrency-with-the-entity-framework-in-an-aspnet-mvc-application-7-of-10"></a>Ošetření souběžnosti se sadou Entity Framework v aplikaci ASP.NET MVC (7 10)
====================
podle [Petr Dykstra](https://github.com/tdykstra)

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvářet aplikace ASP.NET MVC 4 pomocí Entity Framework 5 Code First a Visual Studio 2012. Informace o této sérii kurzů, naleznete v tématu [z prvního kurzu této série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Série kurzů můžete začít od začátku nebo [stáhnout počáteční projekt pro tuto kapitolu](building-the-ef5-mvc4-chapter-downloads.md) a začněte tady.
> 
> > [!NOTE] 
> > 
> > Pokud narazíte na problém nevyřešíte sami, [stáhnout dokončený kapitoly](building-the-ef5-mvc4-chapter-downloads.md) a zkuste problém reprodukovat. Porovnáním kód Dokončený kód v obecně najdete řešení problému. Některé běžné chyby a jejich řešení najdete v tématu [chyby a náhradní řešení.](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#errors)


V předchozích dvou kurzů, kterou jste pracovali související data. Tento kurz ukazuje, jak zpracovat souběžnosti. Vytvoříte webové stránky, které využívají službu `Department` entity a stránek, které upravit a odstranit `Department` entity budou zpracovávat chyby souběžnosti. Na následujících obrázcích je Index a odstranění stránky, včetně některé zprávy, které se zobrazí, pokud dojde ke konfliktu souběžnosti.

![Department_Index_page_before_edits](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dochází, když jeden uživatel zobrazuje entity data abyste ji mohli editovat a aktualizuje stejné entity data jiným uživatelem než změnu první uživatele je zapsána do databáze. Pokud nepovolíte detekce takové konflikty je, kdo aktualizuje databázi poslední přepíše změny dalších uživatelů. V mnoha aplikacích je přijatelné toto riziko: Pokud existuje několik uživatelů nebo několik aktualizací, nebo pokud není skutečně důležité, pokud některé změny budou přepsány, výhody vyváží nižší náklady na programování pro souběžnost. V takovém případě není nutné nakonfigurovat aplikaci pro zpracování konfliktů souběžnosti.

### <a name="pessimistic-concurrency-locking"></a>Pesimistická souběžnost (uzamčení)

Pokud vaše aplikace potřebuje se tak ztrátě dat ve scénářích souběžnosti, je to udělat jedním ze způsobů použití uzamčení databáze. Tento postup se nazývá *Pesimistická souběžnost*. Například předtím, než se pustíte do čtení řádku z databáze, můžete požádat o zámek pro jen pro čtení nebo pro přístup k aktualizaci. Pokud řádek pro aktualizaci přístup, žádné uživatelé můžou zamknout řádku, buď pro jen pro čtení nebo aktualizaci přístup, protože by dostanou kopii dat, která se právě mění. Pokud řádek pro přístup jen pro čtení, ostatní také zařízení Uzamknout pro přístup jen pro čtení, ale ne pro aktualizace.

Zámky pro správu má nevýhody. Může být složité do programu. Vyžaduje významné databáze správy zdrojů, a to může způsobit problémy s výkonem jako počet uživatelů aplikace zvyšuje (to znamená, ho nemá uspokojivé škálování). Z těchto důvodů ne všechny systémy správy databáze nepodporují Pesimistická souběžnost. Entity Framework obsahuje předdefinovanou podporu pro ni a v tomto kurzu nezobrazí způsobu jeho implementace.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Je alternativou k Pesimistická souběžnost *optimistického řízení souběžnosti*. Povolení konfliktů souběžnosti, která se provede a reaguje správně, pokud tomu znamená, že optimistického řízení souběžnosti. Například Jan spustí oddělení upravit stránku, změny **rozpočtu** velikost pro anglickou oddělení od $350,000.00 0.00 $.

![Changing_English_dept_budget_to_100000](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Předtím, než Jan klikne **Uložit**, spustí Jana na stejnou stránku a změny **datum zahájení** pole z 9/1/2007 na verzi 8 nebo 8/2013.

![Changing_English_dept_start_date_to_1999](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Jan klikne **Uložit** první a jeho změna návratu prohlížeč na indexovou stránku, pak Jana klikne vidí **Uložit**. Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti. Mezi možnosti patří následující:

- Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi. V ukázkovém scénáři žádné by dojít ke ztrátě dat., protože různé vlastnosti byly aktualizovány dva uživatelé. Při příštím někdo přejde z anglické oddělení, uvidí John's a Jana změny – počáteční datum 8/8/2013 a rozpočtu nulové dolarů.

    Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě, ale nemůže zamezení ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost entity. Ať už rozhraní Entity Framework funguje tímto způsobem závisí na implementace aktualizace kódu. Není často praktické ve webové aplikaci, protože to může vyžadovat spravovat velké množství stavu aby bylo možné udržovat přehled o všech původní hodnoty vlastností pro entitu a nové hodnoty. Správa velkého objemu stavu může ovlivnit výkon aplikace, protože ji vyžaduje prostředky serveru nebo musí být součástí webové stránky (například v skrytá pole).
- Můžete umožnit změnu Jana John's změna přepsána. Při příštím někdo přejde z anglické oddělení, uvidí 8/8/2013 a obnovený $350,000.00 hodnotu. Tento postup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář. (Hodnoty klienta přednost co je v úložišti.) Jak jsme uvedli v úvodu do této části, pokud tak učiníte vytvářet kód pro zpracování souběžnosti, k tomu dochází automaticky.
- Jana změny můžete zabránit aktualizují v databázi. By obvykle zobrazí chybovou zprávu, zobrazit její aktuální stav dat a povolit jí znovu použít své změny, pokud chce je. Tento postup se nazývá *Store Wins* scénář. (Hodnoty úložiště dat přednost hodnoty odeslány klientem.) V tomto kurzu budete implementovat scénář Store Wins. Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění na co se děje.

### <a name="detecting-concurrency-conflicts"></a>Zjišťování konfliktů souběžnosti

Konflikty lze vyřešit zpracování [OptimisticConcurrencyException](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) výjimky, které vyvolá rozhraní Entity Framework. Pokud chcete zjistit, kdy se má vyvolat tyto výjimky, musí být schopen rozpoznat konflikty Entity Framework. Proto je nutné nakonfigurovat databázi a datový model odpovídajícím způsobem. Některé možnosti aktivace zjišťování konfliktů, patří:

- V tabulce databáze zahrnují sledování sloupec, který slouží k určení, kdy změnil řádek. Potom můžete nakonfigurovat rozhraní Entity Framework patří tento sloupec ve `Where` klauzule SQL `Update` nebo `Delete` příkazy.

    Datový typ sloupce pro sledování je obvykle [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx). [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) hodnotu pořadové číslo, které se zvýší při každé aktualizaci řádku. V `Update` nebo `Delete` příkazu `Where` klauzule obsahuje původní hodnota sloupce pro sledování (původní verze řádku). Pokud se změnila řádek aktualizován jiným uživatelem, hodnota v `rowversion` sloupce se liší od původní hodnoty, proto `Update` nebo `Delete` příkaz nebyl nalezen řádek aktualizovat z důvodu `Where` klauzuli. Když rozhraní Entity Framework zjistí, že nebyly aktualizovány žádné řádky pomocí `Update` nebo `Delete` příkazu (to znamená, když počet ovlivněných řádků je nula), který interpretuje jako ke konfliktu souběžnosti.
- Konfigurace rozhraní Entity Framework pro zahrnutí původní hodnota každý sloupec v tabulce `Where` klauzuli `Update` a `Delete` příkazy.

    Jako první možnost, pokud něco v řádku od řádku se nejdřív přečíst, změnila `Where` klauzule nevrátí řádek k aktualizaci, která nastavení interpretuje Entity Framework jako ke konfliktu souběžnosti. Pro databázové tabulky, které mají mnoho sloupců, tento přístup může vést k velmi velké `Where` klauzule a můžete požadovat, že udržujete velké množství stavu. Jak bylo uvedeno dříve, mohou ovlivnit zachování velké množství stav výkonu aplikace protože ji vyžaduje prostředky serveru nebo musí být součástí webové stránky. Proto tento přístup nedoporučuje, a není metoda použitá v tomto kurzu.

    Pokud chcete k implementaci tohoto přístupu se souběžností, budete muset označit všechny vlastnosti primárního klíče v entitě, kterou chcete sledovat souběžnosti pro tak, že přidáte [atribut ConcurrencyCheck](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute.aspx) atributu na ně. Že změna umožňuje rozhraní Entity Framework zahrňte všechny sloupce v SQL `WHERE` klauzuli `UPDATE` příkazy.

Ve zbývající části tohoto kurzu přidáte [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) vlastnost pro sledování `Department` entity, vytvořte kontroler a zobrazení a otestovat a ověřit, že vše funguje správně.

## <a name="add-an-optimistic-concurrency-property-to-the-department-entity"></a>Přidat vlastnost optimistického řízení souběžnosti na entitu oddělení

V *Models\Department.cs*, přidání vlastnosti sledování do s názvem `RowVersion`:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=18-19)]

[Časové razítko](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx) atribut určuje, že v tomto sloupci se zahrne `Where` klauzuli `Update` a `Delete` příkazy, odeslané do databáze. Atribut se nazývá [časové razítko](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx) protože předchozích verzí SQL serveru použít SQL [časové razítko](https://msdn.microsoft.com/library/ms182776(v=SQL.90).aspx) datového typu než SQL [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) nahradili jsme ho. Typ formátu .NET pro `rowversion` bajtové pole. Pokud chcete použít rozhraní fluent API, můžete použít [IsConcurrencyToken](https://msdn.microsoft.com/library/gg679501(v=VS.103).aspx) metodu pro určení této vlastnosti sledování, jak je znázorněno v následujícím příkladu:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Přidáním vlastnosti změnit model databáze, takže je třeba provést další migraci. V balíčku správce konzoly (konzolu PMC), zadejte následující příkazy:

[!code-console[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cmd)]

## <a name="create-a-department-controller"></a>Vytvoření Kontroleru oddělení

Vytvoření `Department` kontroler a zobrazení stejně jako jste to udělali ostatních řadičů, pomocí následujících nastavení:

![Add_Controller_dialog_box_for_Department_controller](https://asp.net/media/2578041/Windows-Live-Writer_Handling-C.NET-MVC-Application-7-of-10h1_AFDC_Add_Controller_dialog_box_for_Department_controller_d1d9c788-f970-4d6a-9f5a-1eddc84330b7.png)

V *Controllers\DepartmentController.cs*, přidejte `using` – příkaz:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

Změna "LastName" k "FullName" kdekoli v tomto souboru (čtyři výskytů) tak, aby oddělení správce rozevírací seznam bude obsahovat úplný název instruktorem, nikoli pouze poslední název.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=1)]

Nahraďte stávající kód `HttpPost` `Edit` metodu s následujícím kódem:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Zobrazení se budou ukládat původní `RowVersion` hodnotu ve skrytém poli. Když vytvoří vazač modelu `department` instance, objekt bude mít původní `RowVersion` hodnotu vlastnosti a nové hodnoty pro ostatní vlastnosti, jako zadané uživatelem na stránky pro úpravu. Když rozhraní Entity Framework vytvoří SQL `UPDATE` příkazu, že bude obsahovat příkaz `WHERE` klauzuli, která hledá řádek, který má původní `RowVersion` hodnotu.

Pokud jsou ovlivněny žádné řádky `UPDATE` příkazu (žádné řádky mít původní `RowVersion` hodnota), vyvolá rozhraní Entity Framework `DbUpdateConcurrencyException` výjimky a kód v `catch` bloku získá ovlivněný `Department` entity z výjimky objekt. Tato entita má hodnoty přečtené z databáze a nové hodnoty zadané uživatelem:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

V dalším kroku kód přidá vlastní chybovou zprávu pro každý sloupec, který se liší od uživatel zadal na stránky pro úpravu hodnot v databázi:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Chybové zprávy vysvětluje, co se stalo a co dělat:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

Nakonec kód nastaví `RowVersion` hodnotu `Department` načíst objekt na novou hodnotu z databáze. Tato nová `RowVersion` hodnota bude uložena ve skrytém poli, když upravit stránka se zobrazí znovu a další čas kliknutí **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože redisplay stránky pro úpravu bude zachycena.

V *Views\Department\Edit.cshtml*, přidání skrytého pole k uložení `RowVersion` hodnotu vlastnosti, hned za skryté pole pro `DepartmentID` vlastnost:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cshtml?highlight=17)]

V *Views\Department\Index.cshtml*, nahraďte existující kód následujícím kódem přesunout řádek odkazy na levé straně a změnit záhlaví stránky title a ve sloupci zobrazíte `FullName` místo `LastName` v **Správce** sloupce:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cshtml)]

## <a name="testing-optimistic-concurrency-handling"></a>Testování zpracování optimistického řízení souběžnosti

Spuštění tohoto webu a klikněte na tlačítko **oddělení**:

![Department_Index_page_before_edits](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

Klikněte pravým tlačítkem myši **upravit** hypertextový odkaz Jan Novak a vyberte **otevřít na nové kartě** klikněte **upravit** Jan Novak hypertextový odkaz. Dvě okna zobrazení stejné informace.

![Department_Edit_page_before_changes](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

Pole v prvním okně prohlížeče a klikněte na tlačítko **Uložit**.

![Department_Edit_page_1_after_change](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

Prohlížeč zobrazí indexovou stránku s změněné hodnoty.

![Departments_Index_page_after_first_budget_edit](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)

Jakékoli pole v druhém okně prohlížeče a klikněte na tlačítko **Uložit**.

![Department_Edit_page_2_after_change](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

Klikněte na tlačítko **Uložit** v druhém okně prohlížeče. Zobrazí chybová zpráva:

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

Klikněte na tlačítko **Uložit** znovu. Hodnota, kterou jste zadali v druhé prohlížeče uložená spolu s původní hodnoty data, která změníte v první prohlížeče. Uložené hodnoty se zobrazí, jakmile se zobrazí stránka indexu.

![Department_Index_page_with_change_from_second_browser](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image11.png)

## <a name="updating-the-delete-page"></a>Aktualizace stránky Delete

Odstranění stránky Entity Framework detekuje souběžnosti konflikty způsobené někdo jinak úpravy oddělení podobným způsobem. Když `HttpGet` `Delete` metoda zobrazí potvrzení zobrazení, zobrazení zahrnuje původní `RowVersion` hodnotu ve skrytém poli. Hodnota se pak k dispozici na `HttpPost` `Delete` metodu, která je volána, když uživatel potvrdí odstranění. Když vytvoří SQL Entity Framework `DELETE` příkazu, obsahuje `WHERE` klauzule s původní `RowVersion` hodnotu. Pokud vliv na výsledky příkazu v nulový počet řádků (tj. řádek byl změněn, jakmile se zobrazí stránka potvrzení odstranění), je vyvolána výjimka souběžnosti a `HttpGet Delete` metoda je volána příznakem chyba nastavena na `true` k opětovnému zobrazení potvrzovací stránku s chybovou zprávou. Je také možné, že vzhledem k tomu, že řádek byl odstraněn jiným uživatelem, tak v tom případě se zobrazí různé chybová zpráva vliv nulový počet řádků.

V *DepartmentController.cs*, nahraďte `HttpGet` `Delete` metodu s následujícím kódem:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]

Metoda přijímá volitelný parametr, který označuje, zda je právě na stránce zobrazí znovu po chybě souběžnosti. Pokud je tento příznak `true`, chybová zpráva je odeslána pomocí zobrazení `ViewBag` vlastnost.

Nahraďte kód v `HttpPost` `Delete` – metoda (s názvem `DeleteConfirmed`) následujícím kódem:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

V automaticky generovaný kód, který jste právě nahrazen tato metoda přijaty pouze ID záznamu:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

Změnili jste tento parametr `Department` zapisovanou instanci entity vytvořené vazače modelu. To umožňuje přístup k `RowVersion` vlastnost hodnota kromě klíče záznamu.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

Také jste změnili název metody akce z `DeleteConfirmed` k `Delete`. Automaticky generovaný kód s názvem `HttpPost` `Delete` metoda `DeleteConfirmed` poskytnout `HttpPost` metoda jedinečnou signaturu. (CLR vyžaduje přetížené metody s parametry jinou metodu.) Teď, když podpisy jsou jedinečné, můžete zůstat u konvence MVC a použijte stejný název pro `HttpPost` a `HttpGet` metody pro odstranění.

Pokud došlo k chybě souběžnosti je zachycena, kód znovu zobrazí na stránce potvrzení odstranění a zajišťuje, že příznak, který označují, že by se zobrazit zpráva chybě souběžnosti.

V *Views\Department\Delete.cshtml*, nahraďte automaticky generovaný kód následujícím kódem, který umožňuje formátování změní a přidá pole zprávy o chybě. Změny jsou zvýrazněné.

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml?highlight=9,37,40,45-46)]

Tento kód přidá chybovou zprávu mezi `h2` a `h3` záhlaví:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]

Nahradí `LastName` s `FullName` v `Administrator` pole:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

Nakonec přidá skryté pole `DepartmentID` a `RowVersion` vlastnosti po `Html.BeginForm` – příkaz:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

Spustíte oddělení indexovou stránku. Klikněte pravým tlačítkem myši **odstranit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít v novém okně** v prvním okně klikněte na tlačítko **upravit** hypertextový odkaz, angličtina oddělení.

V prvním okně, změňte jednu z hodnot a klikněte na tlačítko **Uložit** :

![Department_Edit_page_after_change_before_delete](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image12.png)

Indexovou stránku potvrdí změny.

![Departments_Index_page_after_budget_edit_before_delete](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image13.png)

V druhém okně klikněte na tlačítko **odstranit**.

![Department_Delete_confirmation_page_before_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image14.png)

Zobrazí chybová zpráva souběžnosti a oddělení hodnoty se aktualizují s tím, co je aktuálně v databázi.

![Department_Delete_confirmation_page_with_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

Vyberete-li **odstranit** znovu, budete přesměrováni na indexovou stránku, který ukazuje, že byl odstraněn z oddělení.

## <a name="summary"></a>Souhrn

Dokončení tohoto postupu Úvod ke zpracování konfliktů souběžnosti. Informace o dalších způsobech pro různé scénáře souběžného zpracování naleznete v tématu [optimistického řízení souběžnosti vzorů](https://blogs.msdn.com/b/adonet/archive/2011/02/03/using-dbcontext-in-ef-feature-ctp5-part-9-optimistic-concurrency-patterns.aspx) a [práce s hodnotami vlastností](https://blogs.msdn.com/b/adonet/archive/2011/01/30/using-dbcontext-in-ef-feature-ctp5-part-5-working-with-property-values.aspx) na blogu týmu Entity Framework. Další kurz ukazuje postupy při implementaci tabulky na hierarchii dědičnosti pro `Instructor` a `Student` entity.

Odkazy na další zdroje Entity Framework najdete v [mapa obsahu přístupu k Data technologie ASP.NET](../../../../whitepapers/aspnet-data-access-content-map.md).

> [!div class="step-by-step"]
> [Předchozí](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)
> [další](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)
