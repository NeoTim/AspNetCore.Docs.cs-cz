---
title: 'Kurz: Zpracování souběžnosti – ASP.NET MVC s EF Core'
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 7b18927d5d528ec2951087502e26b2b30214f389
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103017"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Kurz: Zpracování souběžnosti – ASP.NET MVC s EF Core

V předchozích kurzech jste zjistili, jak aktualizovat data. Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.

Vytvoříte webové stránky, které pracují s entitou oddělení a zpracování chyb, které souběžnosti. Upravit a odstranit stránky, včetně některé zprávy, které se zobrazí, pokud dojde ke konfliktu souběžnosti na následujících obrázcích.

![Stránky pro úpravu oddělení](concurrency/_static/edit-error.png)

![Odstranění stránky oddělení](concurrency/_static/delete-error.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Další informace o konfliktů souběžnosti
> * Přidání vlastnosti sledování do
> * Vytvoření kontroleru oddělení a zobrazení
> * Aktualizace zobrazení Index
> * Aktualizace metod Edit
> * Aktualizovat zobrazení pro úpravy
> * Testování konfliktů souběžnosti
> * Aktualizovat stránku Delete
> * Aktualizovat podrobnosti a vytvořit zobrazení

## <a name="prerequisites"></a>Požadavky

* [Aktualizace souvisejících dat ve webové aplikaci ASP.NET Core MVC s EF Core](update-related-data.md)

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dochází, když jeden uživatel zobrazuje entity data abyste ji mohli editovat a aktualizuje stejné entity data jiným uživatelem než změnu první uživatele je zapsána do databáze. Pokud nepovolíte detekce takové konflikty je, kdo aktualizuje databázi poslední přepíše změny dalších uživatelů. V mnoha aplikacích je přijatelné toto riziko: Pokud existuje několik uživatelů nebo několik aktualizací, nebo pokud není skutečně důležité, pokud některé změny budou přepsány, výhody vyváží nižší náklady na programování pro souběžnost. V takovém případě není nutné nakonfigurovat aplikaci pro zpracování konfliktů souběžnosti.

### <a name="pessimistic-concurrency-locking"></a>Pesimistická souběžnost (uzamčení)

Pokud vaše aplikace potřebuje se tak ztrátě dat ve scénářích souběžnosti, je to udělat jedním ze způsobů použití uzamčení databáze. Tomu se říká Pesimistická souběžnost. Například předtím, než se pustíte do čtení řádku z databáze, můžete požádat o zámek pro jen pro čtení nebo pro přístup k aktualizaci. Pokud řádek pro aktualizaci přístup, žádné uživatelé můžou zamknout řádku, buď pro jen pro čtení nebo aktualizaci přístup, protože by dostanou kopii dat, která se právě mění. Pokud řádek pro přístup jen pro čtení, ostatní také zařízení Uzamknout pro přístup jen pro čtení, ale ne pro aktualizace.

Zámky pro správu má nevýhody. Může být složité do programu. Vyžaduje významné databáze správy zdrojů, a to může způsobit problémy s výkonem jako počet uživatelů aplikace zvyšuje. Z těchto důvodů ne všechny systémy správy databáze nepodporují Pesimistická souběžnost. Entity Framework Core obsahuje předdefinovanou podporu pro ni a v tomto kurzu nezobrazí způsobu jeho implementace.

### <a name="optimistic-concurrency"></a>Optimistická souběžnost

Alternativa k Pesimistická souběžnost je Optimistická souběžnost. Povolení konfliktů souběžnosti, která se provede a reaguje správně, pokud tomu znamená, že optimistického řízení souběžnosti. Například Jana navštíví stránku Upravit oddělení a změní hodnotu rozpočtu pro anglickou oddělení z $350,000.00 na 0.00 $.

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

Jan klikne **Uložit** první a jí změnit návratu na indexovou stránku v prohlížeči se zobrazí.

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

Pak Jan klikne **Uložit** na stránce Upravit, která stále zobrazuje rozpočtu 350,000.00 $. Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.

Mezi možnosti patří následující:

* Můžete sledovat, které vlastnosti uživatele byl změněn a aktualizovat pouze odpovídající sloupce v databázi.

     V ukázkovém scénáři žádné by dojít ke ztrátě dat., protože různé vlastnosti byly aktualizovány dva uživatelé. Při příštím někdo přejde z anglické oddělení, zobrazí se Jana a John's na změny – datum zahájení o 9/1/2013 a rozpočet nulové dolarů. Tato metoda aktualizace může snížit počet konflikty, ke kterým může dojít ke ztrátě, ale nemůže zamezení ztrátě dat, pokud dojde ke změně konkurenční na stejnou vlastnost entity. Ať už rozhraní Entity Framework funguje tímto způsobem závisí na implementace aktualizace kódu. Není často praktické ve webové aplikaci, protože to může vyžadovat spravovat velké množství stavu aby bylo možné udržovat přehled o všech původní hodnoty vlastností pro entitu a nové hodnoty. Správa velkého objemu stavu může ovlivnit výkon aplikace, protože ji vyžaduje prostředky serveru nebo musí být součástí webové stránky (například v skrytá pole) nebo do souboru cookie.

* Můžete nechat John's na změnu Jana změna přepsána.

     Při příštím někdo přejde z anglické oddělení, zobrazí se 9/1/2013 a obnovený $350,000.00 hodnotu. Tento postup se nazývá *Wins, klient* nebo *poslední ve službě Wins* scénář. (Všechny hodnoty z klienta přednost co je v úložišti.) Jak jsme uvedli v úvodu do této části, pokud tak učiníte vytvářet kód pro zpracování souběžnosti, k tomu dochází automaticky.

* John's na změnu může zabránit aktualizují v databázi.

     By obvykle zobrazí chybovou zprávu, zobrazí jeho aktuální stav dat a mu umožní se jeho změny znovu použijte, pokud chce je. Tento postup se nazývá *Store Wins* scénář. (Hodnoty úložiště dat přednost hodnoty odeslány klientem.) V tomto kurzu budete implementovat scénář Store Wins. Tato metoda zajišťuje, že se žádné změny přepsán, aniž by uživatel se zobrazí upozornění na co se děje.

### <a name="detecting-concurrency-conflicts"></a>Zjišťování konfliktů souběžnosti

Konflikty lze vyřešit zpracování `DbConcurrencyException` výjimky, které vyvolá rozhraní Entity Framework. Pokud chcete zjistit, kdy se má vyvolat tyto výjimky, musí být schopen rozpoznat konflikty Entity Framework. Proto je nutné nakonfigurovat databázi a datový model odpovídajícím způsobem. Některé možnosti aktivace zjišťování konfliktů, patří:

* V tabulce databáze zahrnují sledování sloupec, který slouží k určení, kdy změnil řádek. Potom můžete nakonfigurovat rozhraní Entity Framework zahrnout sloupce Where – klauzule SQL aktualizace a odstranění příkazů.

     Datový typ sloupce pro sledování je obvykle `rowversion`. `rowversion` Hodnotu pořadové číslo, které se zvýší při každé aktualizaci řádku. V příkazu Update nebo Delete Where – klauzule obsahuje původní hodnota sloupce pro sledování (původní verze řádku). Pokud se změnila řádek aktualizován jiným uživatelem, hodnota v `rowversion` sloupce se liší od původní hodnotu, aby příkazu Update nebo Delete nelze najít řádek aktualizovat z důvodu Where – klauzule. Když najde Entity Framework, že byly aktualizovány žádné řádky podle Update nebo Delete příkazu (to znamená, když počet ovlivněných řádků je nula), interpretuje, který jako ke konfliktu souběžnosti.

* Konfigurace rozhraní Entity Framework pro zahrnutí původní hodnota každý sloupec v tabulce v Where klauzule příkazy Update a Delete.

     Jako první možnost, pokud něco v řádku změnilo od řádku se nejdřív přečíst Where – klauzule nevrátí řádek k aktualizaci, která nastavení interpretuje Entity Framework jako ke konfliktu souběžnosti. Pro databázové tabulky, které mají mnoho sloupců, tento přístup může vést k velmi velké Where klauzule a můžete požadovat, že udržujete velké množství stavu. Jak bylo uvedeno dříve, udržování velké množství stavu může ovlivnit výkon aplikace. Proto tento postup se obecně nedoporučuje, které není metoda použitá v tomto kurzu.

     Pokud chcete k implementaci tohoto přístupu se souběžností, budete muset označit všechny vlastnosti primárního klíče v entitě, kterou chcete sledovat souběžnosti pro tak, že přidáte `ConcurrencyCheck` atributu na ně. Tato změna umožňuje rozhraní Entity Framework zahrňte všechny sloupce v klauzuli Where příkazu SQL příkazy Update a Delete.

Ve zbývající části tohoto kurzu přidáte `rowversion` vlastnosti sledování do entity oddělení, vytvořit kontroler a zobrazení a otestovat a ověřit, že vše funguje správně.

## <a name="add-a-tracking-property"></a>Přidání vlastnosti sledování do

V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

`Timestamp` Atribut určuje, zda tento sloupec součástí Where klauzule příkazy Update a Delete odešlou do databáze. Atribut se nazývá `Timestamp` protože předchozích verzí SQL serveru použít SQL `timestamp` datového typu než SQL `rowversion` nahradili jsme ho. Typ formátu .NET pro `rowversion` bajtové pole.

Pokud chcete použít rozhraní fluent API, můžete použít `IsConcurrencyToken` – metoda (v *Data/SchoolContext.cs*) pro určení této vlastnosti sledování, jak je znázorněno v následujícím příkladu:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Přidáním vlastnosti změnit model databáze, takže je třeba provést další migraci.

Uložte změny a sestavte projekt a potom zadejte následující příkazy v příkazovém okně:

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Vytvoření kontroleru oddělení a zobrazení

Stejně jako dříve pro studenty, kurzy a vyučující, generování uživatelského rozhraní oddělení kontroler a zobrazení.

![Oddělení vygenerované uživatelské rozhraní](concurrency/_static/add-departments-controller.png)

V *DepartmentsController.cs* souborů, změňte všechny čtyři výskyty "FirstMidName" na "FullName" tak, aby oddělení správce rozevírací seznamy bude obsahovat úplný název instruktorem, nikoli pouze poslední název.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Aktualizace zobrazení Index

Generování uživatelského rozhraní stroj vytvořil RowVersion sloupec v indexu zobrazení, ale nebude se zobrazovat toto pole.

Nahraďte kód v *Views/Departments/Index.cshtml* následujícím kódem.

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

To se změní na záhlaví "Oddělení", odstraní sloupec RowVersion a zobrazí jméno a příjmení namísto křestní jméno správce.

## <a name="update-edit-methods"></a>Aktualizace metod Edit

V obou třídy MetadataExchangeClientMode `Edit` metoda a `Details` metodu, přidejte `AsNoTracking`. V třídy MetadataExchangeClientMode `Edit` metodu, přidejte předběžné načítání pro správce.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading&highlight=2,3)]

Nahraďte stávající kód httppost `Edit` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Kód začíná pokusu o čtení z oddělení aktualizovat. Pokud `SingleOrDefaultAsync` metoda vrátí hodnotu null, z oddělení byla odstraněna jiným uživatelem. V takovém případě kód používá hodnoty odeslaného formuláře vytvořit entitu oddělení tak, aby stránky pro úpravu můžete zobrazí znovu, zobrazí se chybová zpráva. Jako alternativu nebude muset znovu vytvořit entity oddělení, pokud zobrazení pouze chybové zprávy bez opětovné zobrazení pole oddělení.

Zobrazení ukládá původní `RowVersion` hodnotu ve skrytém poli a tato metoda přijímá hodnotu do `rowVersion` parametru. Před voláním `SaveChanges`, budete muset vytvořit z původní `RowVersion` hodnoty vlastnosti `OriginalValues` kolekce entity.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Pak při Entity Framework vytvoří příkaz SQL pro sadu VS11, tohoto příkazu bude obsahovat klauzuli WHERE, která hledá řádek, který má původní `RowVersion` hodnotu. Pokud žádné řádky jsou ovlivněny příkazu UPDATE (žádné řádky mít původní `RowVersion` hodnota), vyvolá rozhraní Entity Framework `DbUpdateConcurrencyException` výjimky.

Kód v bloku catch pro tuto výjimku získá ovlivněné oddělení entity, která má aktualizované hodnoty z `Entries` vlastnost v objektu výjimky.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

`Entries` Kolekce bude obsahovat jen jeden `EntityEntry` objektu.  Tento objekt slouží k získání nové hodnoty zadané uživatelem a hodnot v aktuální databázi.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Kód přidá vlastní chybovou zprávu pro každý sloupec, který má různých hodnot v databázi z co uživatel zadaný v úpravách stránky (pouze jedno pole je tady pro zkrácení).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Nakonec kód nastaví `RowVersion` hodnotu `departmentToUpdate` na novou hodnotu načtených z databáze. Tato nová `RowVersion` hodnota bude uložena ve skrytém poli, když upravit stránka se zobrazí znovu a další čas kliknutí **Uložit**, pouze souběžnosti chyby, ke kterým dochází, protože redisplay stránky pro úpravu bude zachycena.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu. V zobrazení `ModelState` hodnota pole má přednost před hodnoty vlastností modelu Pokud jsou obě přítomny.

## <a name="update-edit-view"></a>Aktualizovat zobrazení pro úpravy

V *Views/Departments/Edit.cshtml*, proveďte následující změny:

* Přidání skrytého pole k uložení `RowVersion` hodnotu vlastnosti, hned za skryté pole pro `DepartmentID` vlastnost.

* Přidejte do rozevíracího seznamu možnost "Vybrat Administrator".

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Testování konfliktů souběžnosti

Spusťte aplikaci a přejděte na stránku oddělení indexu. Klikněte pravým tlačítkem na **upravit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**, klikněte **upravit** hypertextového odkazu pro anglickou oddělení. Prohlížeč dvě karty se nyní zobrazují stejné informace.

Změňte pole na první záložce prohlížeče a klikněte na tlačítko **Uložit**.

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

Prohlížeč zobrazí indexovou stránku s změněné hodnoty.

Změňte pole na druhé záložce prohlížeče.

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

Klikněte na **Uložit**. Zobrazí chybová zpráva:

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

Klikněte na tlačítko **Uložit** znovu. Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče. Uložené hodnoty se zobrazí, jakmile se zobrazí stránka indexu.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Odstranění stránky Entity Framework detekuje souběžnosti konflikty způsobené někdo jinak úpravy oddělení podobným způsobem. Když třídy MetadataExchangeClientMode `Delete` metoda zobrazí potvrzení zobrazení, zobrazení zahrnuje původní `RowVersion` hodnotu ve skrytém poli. Hodnota je pak možné HttpPost `Delete` metodu, která je volána, když uživatel potvrdí odstranění. Entity Framework vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s původní `RowVersion` hodnotu. Pokud vliv na výsledky příkazu v nulový počet řádků (tj. řádek byl změněn, jakmile se zobrazí stránka potvrzení odstranění), je vyvolána výjimka souběžnosti a HttpGet `Delete` metoda je volána k chybě příznak nastaven na hodnotu true, pokud chcete znovu zobrazit potvrzovací stránku s chybovou zprávou. Je také možné, že vzhledem k tomu, že řádek byl odstraněn jiným uživatelem, tak v tom případě se zobrazí žádná chybová zpráva vliv nulový počet řádků.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Aktualizace metod Delete v oddělení kontroleru

V *DepartmentsController.cs*, nahraďte třídy MetadataExchangeClientMode `Delete` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

Metoda přijímá volitelný parametr, který označuje, zda je právě na stránce zobrazí znovu po chybě souběžnosti. Pokud tento příznak má hodnotu true a oddělení již existuje, byla odstraněna jiným uživatelem. V takovém případě kód provede přesměrování na indexovou stránku.  Pokud tento příznak má hodnotu true a oddělení neexistuje, byla změněna jiným uživatelem. V takovém případě kód odešle chybovou zprávu pro zobrazení s využitím `ViewData`.

Nahraďte kód v HttpPost `Delete` – metoda (s názvem `DeleteConfirmed`) následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

V automaticky generovaný kód, který jste právě nahrazen tato metoda přijaty pouze ID záznamu:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Změnili jste tento parametr k oddělení instanci entity vytvořené vazače modelu. To poskytuje EF přístup k hodnotě vlastnosti RowVersion kromě klíč záznamu.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Také jste změnili název metody akce z `DeleteConfirmed` k `Delete`. Automaticky generovaný kód používá název `DeleteConfirmed` poskytnout metoda HttpPost jedinečnou signaturu. (CLR vyžaduje přetížené metody s parametry jinou metodu.) Teď, když podpisy jsou jedinečné, můžete zůstat u konvence MVC a použijte stejný název pro odstranění metody HttpPost a HttpGet.

Pokud již byla odstraněna z oddělení, `AnyAsync` metoda vrátí hodnotu false a aplikace právě přejde zpět do metody indexu.

Pokud došlo k chybě souběžnosti je zachycena, kód znovu zobrazí na stránce potvrzení odstranění a zajišťuje, že příznak, který označují, že by se zobrazit zpráva chybě souběžnosti.

### <a name="update-the-delete-view"></a>Zobrazení aktualizovat, odstranit

V *Views/Departments/Delete.cshtml*, nahraďte následující kód, který přidá polem chybové zprávy a skrytá pole vlastností DepartmentID a RowVersion automaticky generovaný kód. Změny jsou zvýrazněné.

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

To provede následující změny:

* Přidá chybovou zprávu mezi `h2` a `h3` záhlaví.

* Nahradí celý název v FirstMidName **správce** pole.

* Odebere RowVersion pole.

* Přidá skryté pole pro `RowVersion` vlastnost.

Spusťte aplikaci a přejděte na stránku oddělení indexu. Klikněte pravým tlačítkem na **odstranit** hypertextového odkazu pro anglickou oddělení a vyberte **otevřít na nové kartě**, na první kartě klikněte na tlačítko **upravit** hypertextového odkazu pro anglickou oddělení.

V prvním okně, změňte jednu z hodnot a klikněte na tlačítko **Uložit**:

![Stránky pro úpravu oddělení po změně před delete](concurrency/_static/edit-after-change-for-delete.png)

Na druhé kartě klikněte **odstranit**. Zobrazí chybová zpráva souběžnosti a oddělení hodnoty se aktualizují s tím, co je aktuálně v databázi.

![Stránka potvrzení odstranění oddělení s došlo k chybě souběžnosti](concurrency/_static/delete-error.png)

Vyberete-li **odstranit** znovu, budete přesměrováni na indexovou stránku, který ukazuje, že byl odstraněn z oddělení.

## <a name="update-details-and-create-views"></a>Aktualizovat podrobnosti a vytvořit zobrazení

Můžete volitelně vyčistit v podrobnostech o automaticky generovaný kód a vytvořit zobrazení.

Nahraďte kód v *Views/Departments/Details.cshtml* odstranění RowVersion sloupce a zobrazit úplné jméno správce.

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Nahraďte kód v *Views/Departments/Create.cshtml* vyberte možnost přidat do rozevíracího seznamu.

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Další zdroje

 Další informace o tom, jak zpracovat souběžnosti v EF Core najdete v tématu [konfliktů souběžnosti](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Dozvěděli jste se o konfliktů souběžnosti
> * Přidání vlastnosti sledování do
> * Vytvořený kontroler oddělení a zobrazení
> * Aktualizace zobrazení indexu
> * Aktualizace metod Edit
> * Aktualizované zobrazení pro úpravy
> * Konflikty otestované souběžnosti
> * Aktualizovat stránku Delete
> * Aktualizovat podrobnosti a vytvořit zobrazení

Přejděte k dalším článku se dozvíte, jak implementovat tabulky na hierarchii dědičnosti pro entity instruktorem a studentů.
> [!div class="nextstepaction"]
> [Implementace tabulky za hierarchie dědičnosti](inheritance.md)
