---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Kurz: Popisovač souběžnosti ovládacím prvkem EF v aplikaci ASP.NET MVC 5'
description: Tento kurz ukazuje, jak použít optimistické řízení souběžnosti řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
author: tdykstra
ms.author: riande
ms.date: 01/21/2019
ms.topic: tutorial
ms.assetid: be0c098a-1fb2-457e-b815-ddca601afc65
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: b77b8d6f952472f4d3030f54665f970b8ace2caf
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444178"
---
# <a name="tutorial-handle-concurrency-with-ef-in-an-aspnet-mvc-5-app"></a>Kurz: Popisovač souběžnosti ovládacím prvkem EF v aplikaci ASP.NET MVC 5

V předchozích kurzech jste zjistili, jak aktualizovat data. Tento kurz ukazuje, jak použít optimistické řízení souběžnosti řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu. Změnit webové stránky, které využívají službu `Department` entity tak, aby se zpracování chyb souběžnosti. Upravit a odstranit stránky, včetně některé zprávy, které se zobrazí, pokud dojde ke konfliktu souběžnosti na následujících obrázcích.

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Další informace o konfliktů souběžnosti
> * Přidat optimistického řízení souběžnosti
> * Změna kontroleru oddělení
> * Test souběžného zpracování
> * Aktualizovat stránku Delete

## <a name="prerequisites"></a>Požadavky

* [Async a uložené procedury](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dochází, když jeden uživatel zobrazuje entity data abyste ji mohli editovat a aktualizuje stejné entity data jiným uživatelem než změnu první uživatele je zapsána do databáze. Pokud nepovolíte detekce takové konflikty je, kdo aktualizuje databázi poslední přepíše změny dalších uživatelů. V mnoha aplikacích je přijatelné toto riziko: Pokud existuje několik uživatelů nebo několik aktualizací, nebo pokud není skutečně důležité, pokud některé změny budou přepsány, výhody vyváží nižší náklady na programování pro souběžnost. V takovém případě není nutné nakonfigurovat aplikaci pro zpracování konfliktů souběžnosti.

### <a name="pessimistic-concurrency-locking"></a>Pesimistická souběžnost (uzamčení)

Pokud vaše aplikace potřebuje se tak ztrátě dat ve scénářích souběžnosti, je to udělat jedním ze způsobů použití uzamčení databáze. Tento postup se nazývá *Pesimistická souběžnost*. Například předtím, než se pustíte do čtení řádku z databáze, můžete požádat o zámek pro jen pro čtení nebo pro přístup k aktualizaci. Pokud řádek pro aktualizaci přístup, žádné uživatelé můžou zamknout řádku, buď pro jen pro čtení nebo aktualizaci přístup, protože by dostanou kopii dat, která se právě mění. Pokud řádek pro přístup jen pro čtení, ostatní také zařízení Uzamknout pro přístup jen pro čtení, ale ne pro aktualizace.

Zámky pro správu má nevýhody. Může být složité do programu. Vyžaduje významné databáze správy zdrojů, a to může způsobit problémy s výkonem jako počet uživatelů aplikace zvyšuje. Z těchto důvodů ne všechny systémy správy databáze nepodporují Pesimistická souběžnost. Entity Framework obsahuje předdefinovanou podporu pro ni a v tomto kurzu nezobrazí způsobu jeho implementace.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Je alternativou k Pesimistická souběžnost *optimistického řízení souběžnosti*. Povolení konfliktů souběžnosti, která se provede a reaguje správně, pokud tomu znamená, že optimistického řízení souběžnosti. Například Jan spustí oddělení upravit stránku, změny **rozpočtu** velikost pro anglickou oddělení od $350,000.00 0.00 $.

Předtím, než Jan klikne **Uložit**, spustí Jana na stejnou stránku a změny **datum zahájení** pole z 9/1/2007 na verzi 8 nebo 8/2013.

Jan klikne **Uložit** první a jeho změna návratu prohlížeč na indexovou stránku, pak Jana klikne vidí **Uložit**. Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti. Mezi možnosti patří následující:

- Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi. V ukázkovém scénáři žádné by dojít ke ztrátě dat., protože různé vlastnosti byly aktualizovány dva uživatelé. Při příštím někdo přejde z anglické oddělení, uvidí John's a Jana změny – počáteční datum 8/8/2013 a rozpočtu nulové dolarů.

    Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě, ale nemůže zamezení ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost entity. Ať už rozhraní Entity Framework funguje tímto způsobem závisí na implementace aktualizace kódu. Není často praktické ve webové aplikaci, protože to může vyžadovat spravovat velké množství stavu aby bylo možné udržovat přehled o všech původní hodnoty vlastností pro entitu a nové hodnoty. Správa velkého objemu stavu může ovlivnit výkon aplikace, protože ji vyžaduje prostředky serveru nebo musí být součástí webové stránky (například v skrytá pole) nebo do souboru cookie.
- Můžete umožnit změnu Jana John's změna přepsána. Při příštím někdo přejde z anglické oddělení, uvidí 8/8/2013 a obnovený $350,000.00 hodnotu. Tento postup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář. (Všechny hodnoty z klienta přednost co je v úložišti.) Jak jsme uvedli v úvodu do této části, pokud tak učiníte vytvářet kód pro zpracování souběžnosti, k tomu dochází automaticky.
- Jana změny můžete zabránit aktualizují v databázi. By obvykle zobrazí chybovou zprávu, zobrazit její aktuální stav dat a povolit jí znovu použít své změny, pokud chce je. Tento postup se nazývá *Store Wins* scénář. (Hodnoty úložiště dat přednost hodnoty odeslány klientem.) V tomto kurzu budete implementovat scénář Store Wins. Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění na co se děje.

### <a name="detecting-concurrency-conflicts"></a>Zjišťování konfliktů souběžnosti

Konflikty lze vyřešit zpracování [OptimisticConcurrencyException](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) výjimky, které vyvolá rozhraní Entity Framework. Pokud chcete zjistit, kdy se má vyvolat tyto výjimky, musí být schopen rozpoznat konflikty Entity Framework. Proto je nutné nakonfigurovat databázi a datový model odpovídajícím způsobem. Některé možnosti aktivace zjišťování konfliktů, patří:

- V tabulce databáze zahrnují sledování sloupec, který slouží k určení, kdy změnil řádek. Potom můžete nakonfigurovat rozhraní Entity Framework patří tento sloupec ve `Where` klauzule SQL `Update` nebo `Delete` příkazy.

    Datový typ sloupce pro sledování je obvykle [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx). [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) hodnotu pořadové číslo, které se zvýší při každé aktualizaci řádku. V `Update` nebo `Delete` příkazu `Where` klauzule obsahuje původní hodnota sloupce pro sledování (původní verze řádku). Pokud se změnila řádek aktualizován jiným uživatelem, hodnota v `rowversion` sloupce se liší od původní hodnoty, proto `Update` nebo `Delete` příkaz nebyl nalezen řádek aktualizovat z důvodu `Where` klauzuli. Když rozhraní Entity Framework zjistí, že nebyly aktualizovány žádné řádky pomocí `Update` nebo `Delete` příkazu (to znamená, když počet ovlivněných řádků je nula), který interpretuje jako ke konfliktu souběžnosti.
- Konfigurace rozhraní Entity Framework pro zahrnutí původní hodnota každý sloupec v tabulce `Where` klauzuli `Update` a `Delete` příkazy.

    Jako první možnost, pokud něco v řádku od řádku se nejdřív přečíst, změnila `Where` klauzule nevrátí řádek k aktualizaci, která nastavení interpretuje Entity Framework jako ke konfliktu souběžnosti. Pro databázové tabulky, které mají mnoho sloupců, tento přístup může vést k velmi velké `Where` klauzule a můžete požadovat, že udržujete velké množství stavu. Jak bylo uvedeno dříve, udržování velké množství stavu může ovlivnit výkon aplikace. Proto tento postup se obecně nedoporučuje, které není metoda použitá v tomto kurzu.

    Pokud chcete k implementaci tohoto přístupu se souběžností, budete muset označit všechny vlastnosti primárního klíče v entitě, kterou chcete sledovat souběžnosti pro tak, že přidáte [atribut ConcurrencyCheck](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute.aspx) atributu na ně. Že změna umožňuje rozhraní Entity Framework zahrňte všechny sloupce v SQL `WHERE` klauzuli `UPDATE` příkazy.

Ve zbývající části tohoto kurzu přidáte [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) vlastnost pro sledování `Department` entity, vytvořte kontroler a zobrazení a otestovat a ověřit, že vše funguje správně.

## <a name="add-optimistic-concurrency"></a>Přidat optimistického řízení souběžnosti

V *Models\Department.cs*, přidání vlastnosti sledování do s názvem `RowVersion`:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=20-22)]

[Časové razítko](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx) atribut určuje, že v tomto sloupci se zahrne `Where` klauzuli `Update` a `Delete` příkazy, odeslané do databáze. Atribut se nazývá [časové razítko](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx) protože předchozích verzí SQL serveru použít SQL [časové razítko](https://msdn.microsoft.com/library/ms182776(v=SQL.90).aspx) datového typu než SQL [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) nahradili jsme ho. Typ formátu .net pro [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) bajtové pole.

Pokud chcete použít rozhraní fluent API, můžete použít [IsConcurrencyToken](https://msdn.microsoft.com/library/gg679501(v=VS.103).aspx) metodu pro určení této vlastnosti sledování, jak je znázorněno v následujícím příkladu:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Přidáním vlastnosti změnit model databáze, takže je třeba provést další migraci. V balíčku správce konzoly (konzolu PMC), zadejte následující příkazy:

[!code-console[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cmd)]

## <a name="modify-department-controller"></a>Změna kontroleru oddělení

V *Controllers\DepartmentController.cs*, přidejte `using` – příkaz:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

V *DepartmentController.cs* souborů, změňte všechny čtyři výskyty "LastName" na "FullName" tak, aby oddělení správce rozevírací seznamy bude obsahovat úplný název instruktorem, nikoli pouze poslední název.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=1)]

Nahraďte stávající kód `HttpPost` `Edit` metodu s následujícím kódem:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Pokud `FindAsync` metoda vrátí hodnotu null, z oddělení byla odstraněna jiným uživatelem. Kód používá hodnoty odeslaného formuláře vytvořit entitu oddělení tak, aby stránky pro úpravu můžete zobrazí znovu, zobrazí se chybová zpráva. Jako alternativu nebude muset znovu vytvořit entity oddělení, pokud zobrazení pouze chybové zprávy bez opětovné zobrazení pole oddělení.

Zobrazení ukládá původní `RowVersion` hodnotu ve skrytém poli a metoda obdrží v `rowVersion` parametru. Před voláním `SaveChanges`, budete muset vytvořit z původní `RowVersion` hodnoty vlastnosti `OriginalValues` kolekce entity. Když rozhraní Entity Framework vytvoří SQL `UPDATE` příkazu, že bude obsahovat příkaz `WHERE` klauzuli, která hledá řádek, který má původní `RowVersion` hodnotu.

Pokud jsou ovlivněny žádné řádky `UPDATE` příkazu (žádné řádky mít původní `RowVersion` hodnota), vyvolá rozhraní Entity Framework `DbUpdateConcurrencyException` výjimky a kód v `catch` bloku získá ovlivněný `Department` entity z výjimky objekt.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Tento objekt obsahuje nové hodnoty zadané uživatelem v jeho `Entity` vlastnosti kde můžete získat hodnoty z databáze pomocí volání `GetDatabaseValues` metody.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

`GetDatabaseValues` Metoda vrátí hodnotu null, pokud někdo odstranil řádku z databáze; v opačném případě musíte přetypovat vráceného objektu na `Department` třídy s cílem získat přístup `Department` vlastnosti. (Protože je již zaškrtnuté políčko k odstranění, `databaseEntry` by mít hodnotu null jenom v případě, že byl odstraněn z oddělení po `FindAsync` spustí a před `SaveChanges` spustí.)

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

V dalším kroku kód přidá vlastní chybovou zprávu pro každý sloupec, který se liší od uživatel zadal na stránky pro úpravu hodnot v databázi:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

Chybové zprávy vysvětluje, co se stalo a co dělat:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Nakonec kód nastaví `RowVersion` hodnotu `Department` načíst objekt na novou hodnotu z databáze. Tato nová `RowVersion` hodnota bude uložena ve skrytém poli, když upravit stránka se zobrazí znovu a další čas kliknutí **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože redisplay stránky pro úpravu bude zachycena.

V *Views\Department\Edit.cshtml*, přidání skrytého pole k uložení `RowVersion` hodnotu vlastnosti, hned za skryté pole pro `DepartmentID` vlastnost:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=18)]

## <a name="test-concurrency-handling"></a>Test souběžného zpracování

Spuštění tohoto webu a klikněte na tlačítko **oddělení**.

Klikněte pravým tlačítkem myši **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě** klikněte **upravit** hypertextového odkazu pro anglickou oddělení. Dvě karty zobrazí stejné informace.

Změňte pole na první záložce prohlížeče a klikněte na tlačítko **Uložit**.

Prohlížeč zobrazí indexovou stránku s změněné hodnoty.

Změňte pole na druhé záložce prohlížeče a klikněte na tlačítko **Uložit**. Zobrazí chybová zpráva:

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

Klikněte na tlačítko **Uložit** znovu. Hodnota, kterou jste zadali na druhé záložce prohlížeče je uložen spolu s původní hodnoty dat, který jste změnili v první prohlížeče. Uložené hodnoty se zobrazí, jakmile se zobrazí stránka indexu.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Odstranění stránky Entity Framework detekuje souběžnosti konflikty způsobené někdo jinak úpravy oddělení podobným způsobem. Když `HttpGet` `Delete` metoda zobrazí potvrzení zobrazení, zobrazení zahrnuje původní `RowVersion` hodnotu ve skrytém poli. Hodnota se pak k dispozici na `HttpPost` `Delete` metodu, která je volána, když uživatel potvrdí odstranění. Když vytvoří SQL Entity Framework `DELETE` příkazu, obsahuje `WHERE` klauzule s původní `RowVersion` hodnotu. Pokud vliv na výsledky příkazu v nulový počet řádků (tj. řádek byl změněn, jakmile se zobrazí stránka potvrzení odstranění), je vyvolána výjimka souběžnosti a `HttpGet Delete` metoda je volána příznakem chyba nastavena na `true` k opětovnému zobrazení potvrzovací stránku s chybovou zprávou. Je také možné, že vzhledem k tomu, že řádek byl odstraněn jiným uživatelem, tak v tom případě se zobrazí různé chybová zpráva vliv nulový počet řádků.

V *DepartmentController.cs*, nahraďte `HttpGet` `Delete` metodu s následujícím kódem:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

Metoda přijímá volitelný parametr, který označuje, zda je právě na stránce zobrazí znovu po chybě souběžnosti. Pokud je tento příznak `true`, chybová zpráva je odeslána pomocí zobrazení `ViewBag` vlastnost.

Nahraďte kód v `HttpPost` `Delete` – metoda (s názvem `DeleteConfirmed`) následujícím kódem:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

V automaticky generovaný kód, který jste právě nahrazen tato metoda přijaty pouze ID záznamu:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

Změnili jste tento parametr `Department` zapisovanou instanci entity vytvořené vazače modelu. To umožňuje přístup k `RowVersion` vlastnost hodnota kromě klíče záznamu.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs)]

Také jste změnili název metody akce z `DeleteConfirmed` k `Delete`. Automaticky generovaný kód s názvem `HttpPost` `Delete` metoda `DeleteConfirmed` poskytnout `HttpPost` metoda jedinečnou signaturu. (CLR vyžaduje přetížené metody s parametry jinou metodu.) Teď, když podpisy jsou jedinečné, můžete zůstat u konvence MVC a použijte stejný název pro `HttpPost` a `HttpGet` metody pro odstranění.

Pokud došlo k chybě souběžnosti je zachycena, kód znovu zobrazí na stránce potvrzení odstranění a zajišťuje, že příznak, který označují, že by se zobrazit zpráva chybě souběžnosti.

V *Views\Department\Delete.cshtml*, nahraďte následující kód, který přidá polem chybové zprávy a skrytá pole vlastností DepartmentID a RowVersion automaticky generovaný kód. Změny jsou zvýrazněné.

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml?highlight=9-10,21,52-54)]

Tento kód přidá chybovou zprávu mezi `h2` a `h3` záhlaví:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

Nahradí `LastName` s `FullName` v `Administrator` pole:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

Nakonec přidá skryté pole `DepartmentID` a `RowVersion` vlastnosti po `Html.BeginForm` – příkaz:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cshtml)]

Spustíte oddělení indexovou stránku. Klikněte pravým tlačítkem myši **odstranit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě** na první kartě klikněte na tlačítko **upravit** hypertextového odkazu pro anglickou oddělení.

V prvním okně, změňte jednu z hodnot a klikněte na tlačítko **Uložit**.

Indexovou stránku potvrdí změny.

Na druhé kartě klikněte **odstranit**.

Zobrazí chybová zpráva souběžnosti a oddělení hodnoty se aktualizují s tím, co je aktuálně v databázi.

![Department_Delete_confirmation_page_with_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

Vyberete-li **odstranit** znovu, budete přesměrováni na indexovou stránku, který ukazuje, že byl odstraněn z oddělení.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework najdete v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

Informace o dalších způsobech pro různé scénáře souběžného zpracování naleznete v tématu [optimistického řízení souběžnosti vzorů](https://msdn.microsoft.com/data/jj592904) a [práce s hodnotami vlastností](https://msdn.microsoft.com/data/jj592677) na webové stránce MSDN. Další kurz ukazuje postupy při implementaci tabulky na hierarchii dědičnosti pro `Instructor` a `Student` entity.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Dozvěděli jste se o konfliktů souběžnosti
> * Přidání optimistického řízení souběžnosti
> * Upravené oddělení kontroleru
> * Zpracování otestované souběžnosti
> * Aktualizovat stránku Delete

Přejděte k dalším článku se dozvíte, jak implementovat dědičnosti v datovém modelu.
> [!div class="nextstepaction"]
> [Implementace dědičnosti v datovém modelu](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)