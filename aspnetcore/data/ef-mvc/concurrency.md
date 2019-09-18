---
title: 'Kurz: Zpracování souběžnosti – ASP.NET MVC pomocí EF Core'
description: Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: e8c88ed2811ad221d94c963c6e14fea9bc1607ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080448"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Kurz: Zpracování souběžnosti – ASP.NET MVC pomocí EF Core

V předchozích kurzech jste zjistili, jak aktualizovat data. Tento kurz ukazuje, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.

Vytvoříte webové stránky, které budou fungovat s entitou oddělení a budou zpracovávat chyby souběžnosti. Následující ilustrace znázorňují stránky upravit a odstranit, včetně některých zpráv zobrazených v případě, že dojde ke konfliktu souběžnosti.

![Stránka pro úpravy oddělení](concurrency/_static/edit-error.png)

![Stránka pro odstranění oddělení](concurrency/_static/delete-error.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Další informace o konfliktech souběžnosti
> * Přidat vlastnost sledování
> * Vytvoření kontroleru a zobrazení oddělení
> * Aktualizovat zobrazení indexu
> * Aktualizovat metody úprav
> * Aktualizovat zobrazení pro úpravy
> * Konflikty testů v souběžnosti
> * Aktualizovat stránku Delete
> * Aktualizovat podrobnosti a vytvořit zobrazení

## <a name="prerequisites"></a>Požadavky

* [Aktualizace souvisejících dat](update-related-data.md)

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dojde, když jeden uživatel zobrazuje data entity, aby je mohl upravit, a poté jiný uživatel aktualizuje data stejné entity před zápisem prvního uživatele do databáze. Pokud nepovolíte detekci takových konfliktů, si aktualizace databáze poslední přepíše změny provedené ostatními uživateli. V mnoha aplikacích je toto riziko přijatelné: v případě, že existuje několik uživatelů nebo málo aktualizací, nebo pokud jsou nějaké změny přepsány, náklady na programování pro souběžnost můžou převážit výhody. V takovém případě nemusíte konfigurovat aplikaci pro zpracování konfliktů souběžnosti.

### <a name="pessimistic-concurrency-locking"></a>Pesimistická souběžnost (uzamykání)

Pokud vaše aplikace potřebuje zabránit náhodné ztrátě dat ve scénářích souběžnosti, stačí jeden způsob, jak to provést, pomocí zámků databáze. Tato metoda se nazývá pesimistická souběžnost. Například před čtením řádku z databáze si vyžádáte zámek jen pro čtení nebo pro přístup k aktualizacím. Pokud zamknete řádek pro přístup k aktualizacím, žádní jiní uživatelé nemůžou Uzamknout řádek buď pro čtení, nebo pro přístup k aktualizacím, protože by získali kopii dat, která se v procesu mění. Pokud zamknete řádek pro přístup jen pro čtení, můžou ho jiní uživatelé taky uzamknout pro přístup jen pro čtení, ale ne pro aktualizace.

Správa zámků má nevýhody. Může být komplexní pro program. Vyžaduje významné prostředky správy databáze a může způsobit problémy s výkonem, protože se zvyšuje počet uživatelů aplikace. Z těchto důvodů ne všechny systémy správy databáze podporují pesimistickou souběžnost. Entity Framework Core pro něj neposkytuje žádnou integrovanou podporu a v tomto kurzu se vám nezobrazí, jak ho implementovat.

### <a name="optimistic-concurrency"></a>Optimistická metoda souběžného zpracování

Alternativou k pesimistické souběžnosti je Optimistická souběžnost. Optimistická souběžnost znamená, že může dojít ke konfliktům souběžnosti a v případě, že je funguje správně. Jana například navštíví stránku pro úpravu oddělení a změní částku rozpočtu pro anglické oddělení z $350 000,00 na $0,00.

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

Předtím, než Jan klikne **Uložit**, Jan navštíví na stejnou stránku a změny pole Datum zahájení 9/1/2013 z 9/1/2007.

![Změna počátečního data a 2013](concurrency/_static/change-date.png)

Jana klikne na **Uložit** a po návratu prohlížeče na stránku indexu uvidí jeho změnu.

![Změnit na hodnotu nula rozpočtu](concurrency/_static/budget-zero.png)

Potom Jan klikne na **Uložit** na stránce pro úpravy, která stále zobrazuje rozpočet $350 000,00. Co bude dál se určuje podle způsobu zpracování konfliktů souběžnosti.

Mezi tyto možnosti patří:

* Můžete sledovat, kterou vlastnost uživatel změnil, a aktualizovat pouze odpovídající sloupce v databázi.

     V ukázkovém scénáři by se neztratila žádná data, protože dva uživatelé aktualizovali různé vlastnosti. Když někdo příště prochází v anglickém oddělení, uvidí změny Jana i Jan – počáteční datum 9/1/2013 a rozpočet s nulovými dolary. Tato metoda aktualizace může snížit počet konfliktů, které by mohly mít za následek ztrátu dat, ale nemůže se vyhnout ztrátě dat, pokud se ve stejné vlastnosti entity provedou konkurenční změny. To, zda Entity Framework funguje tímto způsobem závisí na způsobu implementace kódu aktualizace. V rámci webové aplikace to často není praktické, protože může vyžadovat, abyste zachovali velké množství stavu, aby bylo možné sledovat všechny původní hodnoty vlastností pro entitu a také nové hodnoty. Udržování velkých objemů stavu může ovlivnit výkon aplikace, protože buď vyžaduje prostředky serveru, nebo musí být součástí samotné webové stránky (například v skrytých polích) nebo v souboru cookie.

* Můžete nechat John's na změnu Jana změna přepsána.

     Když někdo příště prochází v anglickém oddělení, uvidí 9/1/2013 a obnovenou hodnotu $350 000,00. To se označuje jako *klient WINS* nebo *Poslední ve scénáři WINS* . (Všechny hodnoty z klienta přednost co je v úložišti.) Jak je uvedeno v části Úvod do této části, pokud neuděláte žádné kódování pro zpracování souběžnosti, k tomu dojde automaticky.

* Můžete zabránit tomu, aby se změnila aktualizace od Jan v databázi.

     Obvykle byste zobrazili chybovou zprávu, zobrazit její aktuální stav a umožnit jim, aby znovu použila své změny, pokud to ještě chce udělat. Tento postup se nazývá *Store Wins* scénář. (Hodnoty úložiště dat přednost hodnoty odeslány klientem.) V tomto kurzu implementujete scénář služby WINS pro Store. Tato metoda zajistí, že nedojde k přepsání žádné změny bez upozornění uživatele na to, co se děje.

### <a name="detecting-concurrency-conflicts"></a>Zjišťování konfliktů souběžnosti

Konflikty můžete vyřešit zpracováním `DbConcurrencyException` výjimek, které Entity Framework vyvolá. Chcete-li zjistit, kdy vyvolat tyto výjimky, Entity Framework musí být schopna detekovat konflikty. Proto je nutné správně nakonfigurovat databázi a datový model. Mezi možnosti pro povolení detekce konfliktů patří následující:

* V tabulce databáze zahrňte sloupec sledování, který se dá použít k určení, kdy došlo ke změně řádku. Pak můžete nakonfigurovat Entity Framework pro zahrnutí tohoto sloupce do klauzule WHERE příkazů SQL Update nebo DELETE.

     Datový typ sloupce sledování je obvykle `rowversion`. `rowversion` Hodnota je sekvenční číslo, které se zvýší pokaždé, když se řádek aktualizuje. V příkazu Update nebo DELETE zahrnuje klauzule WHERE původní hodnotu sloupce sledování (původní verze řádku). Pokud byl aktualizovaný řádek změněn jiným uživatelem, hodnota ve `rowversion` sloupci se liší od původní hodnoty, takže příkaz Update nebo DELETE nemůže najít řádek, který se má aktualizovat z klauzule WHERE. Pokud Entity Framework zjistí, že nebyly aktualizovány žádné řádky pomocí příkazu Update nebo Delete (tj. Pokud je počet ovlivněných řádků nula), je interpretována jako konflikt souběžnosti.

* Nakonfigurujte Entity Framework tak, aby zahrnoval původní hodnoty všech sloupců v tabulce v klauzuli WHERE příkazů Update a DELETE.

     Stejně jako v první možnosti, pokud se cokoli na řádku od prvního načtení řádku změnilo, klauzule WHERE nevrátí řádek, který se má aktualizovat, což Entity Framework interpretuje jako konflikt souběžnosti. U databázových tabulek, které mají mnoho sloupců, může tento přístup mít za následek velmi velké klauzule WHERE a může vyžadovat, abyste zachovali velké objemy stavů. Jak bylo uvedeno dříve, údržba velkých objemů stavu může ovlivnit výkon aplikace. Proto se tento přístup obecně nedoporučuje a nejedná se o metodu používanou v tomto kurzu.

     Pokud chcete tento přístup implementovat do souběžnosti, je nutné označit všechny vlastnosti neprimárního klíče v entitě, pro kterou chcete sledovat souběžnost, přidáním `ConcurrencyCheck` atributu do těchto. Tato změna umožňuje Entity Framework zahrnout všechny sloupce v klauzuli WHERE SQL příkazu Update a DELETE.

Ve zbývající části tohoto kurzu přidáte `rowversion` k entitě oddělení vlastnost sledování, vytvoříte kontroler a zobrazení a otestujete, jestli vše funguje správně.

## <a name="add-a-tracking-property"></a>Přidat vlastnost sledování

V *Models/Department.cs*, přidání vlastnosti sledování do s názvem RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

`Timestamp` Atribut určuje, zda bude tento sloupec zahrnut v klauzuli WHERE příkazů Update a DELETE odeslaných do databáze. Atribut je volán `Timestamp` , protože předchozí verze SQL Server používaly datový typ `timestamp` SQL předtím, než ho `rowversion` SQL nahradil. Typ .NET pro `rowversion` je pole bajtů.

Pokud dáváte přednost používání rozhraní Fluent API, můžete použít `IsConcurrencyToken` metodu (v *data/SchoolContext. cs*) k určení vlastnosti sledování, jak je znázorněno v následujícím příkladu:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Přidáním vlastnosti, kterou jste změnili databázový model, takže je nutné provést další migraci.

Uložte změny a sestavte projekt a pak v příkazovém okně zadejte následující příkazy:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Vytvoření kontroleru a zobrazení oddělení

Generování uživatelského rozhraní a zobrazení jako dříve pro studenty, kurzy a instruktory.

![Oddělení uživatelského rozhraní](concurrency/_static/add-departments-controller.png)

V souboru *DepartmentsController.cs* změňte všechny čtyři výskyty řetězce "FirstMidName" na "FullName", aby rozevírací seznamy správce oddělení obsahovaly celé jméno instruktora, nikoli jenom příjmení.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Aktualizovat zobrazení indexu

Modul generování uživatelského rozhraní vytvořil v zobrazení indexu sloupec RowVersion, ale toto pole by se nemělo zobrazovat.

Nahraďte kód v *zobrazeních/departments/index. cshtml* následujícím kódem.

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Tím se změní nadpis na "oddělení", odstraní se sloupec RowVersion a pro správce se zobrazí jméno a příjmení místo křestní jméno.

## <a name="update-edit-methods"></a>Aktualizovat metody úprav

V metodě HttpGet `Edit` `Details` a metodě přidejte `AsNoTracking`. V metodě HttpGet `Edit` přidejte Eager načítání pro správce.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

Nahraďte existující kód pro metodu HTTPPOST `Edit` následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Kód začíná tím, že se pokusí přečíst oddělení, které se má aktualizovat. `FirstOrDefaultAsync` Pokud metoda vrátí hodnotu null, oddělení bylo odstraněno jiným uživatelem. V takovém případě kód používá hodnoty vystaveného formuláře k vytvoření entity oddělení, aby se stránka pro úpravy mohla znovu zobrazit s chybovou zprávou. Jako alternativu nebudete muset entitu oddělení znovu vytvořit, pokud se zobrazí pouze chybová zpráva bez zobrazení polí oddělení.

Zobrazení ukládá původní `RowVersion` hodnotu do skrytého pole a tato metoda obdrží tuto hodnotu `rowVersion` v parametru. Před voláním `SaveChanges`je nutné umístit tuto původní `RowVersion` hodnotu vlastnosti do `OriginalValues` kolekce pro entitu.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Pak když Entity Framework vytvoří příkaz SQL Update, bude tento příkaz obsahovat klauzuli WHERE, která vyhledá řádek, který má původní `RowVersion` hodnotu. Pokud nejsou žádné řádky ovlivněny příkazem Update (žádné řádky nemají původní `RowVersion` hodnotu), Entity Framework `DbUpdateConcurrencyException` vyvolá výjimku.

Kód v bloku catch pro tuto výjimku získá entitu ovlivněného oddělení, která má aktualizované hodnoty z `Entries` vlastnosti objektu Exception.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

Kolekce bude obsahovat pouze jeden `EntityEntry` objekt. `Entries`  Tento objekt lze použít k získání nových hodnot zadaných uživatelem a aktuálními hodnotami databáze.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Kód přidá vlastní chybovou zprávu pro každý sloupec, který má jiné hodnoty databáze než uživatel zadaný na stránce pro úpravy (tady se zobrazí pouze jedno pole pro zkrácení).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Nakonec kód nastaví `RowVersion` hodnotu `departmentToUpdate` na novou hodnotu načtenou z databáze. Tato nová `RowVersion` hodnota se uloží do skrytého pole, když se znovu zobrazí stránka pro úpravy, a když uživatel příště klikne na **Uložit**, zachytí se jenom chyby souběžnosti, ke kterým dojde od zobrazení stránky pro úpravy.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

`ModelState.Remove` Příkazu se totiž `ModelState` má starý `RowVersion` hodnotu. V zobrazení `ModelState` má hodnota pro pole přednost před hodnotami vlastností modelu, pokud jsou oba přítomny.

## <a name="update-edit-view"></a>Aktualizovat zobrazení pro úpravy

V *zobrazení/oddělení/upravit. cshtml*proveďte následující změny:

* Přidejte skryté pole pro uložení `RowVersion` hodnoty vlastnosti hned za skryté pole `DepartmentID` pro vlastnost.

* Přidejte v rozevíracím seznamu možnost vybrat správce.

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Konflikty testů v souběžnosti

Spusťte aplikaci a pokračujte na stránku s indexem oddělení. Klikněte pravým tlačítkem **myši na hypertextový odkaz pro** jazykové oddělení a vyberte **otevřít na nové kartě**a pak klikněte na hypertextový odkaz pro **Úpravy** v anglickém oddělení. Dvě karty prohlížeče nyní zobrazují stejné informace.

Změňte pole na první kartě prohlížeče a klikněte na **Uložit**.

![Upravit oddělení po změně – stránka 1](concurrency/_static/edit-after-change-1.png)

Prohlížeč zobrazí stránku indexu se změněnou hodnotou.

Změňte pole na druhé kartě prohlížeče.

![Upravit oddělení po změně – stránka 2](concurrency/_static/edit-after-change-2.png)

Klikněte na **Uložit**. Zobrazí se chybová zpráva:

![Oddělení upravit stránku chybová zpráva](concurrency/_static/edit-error.png)

Klikněte na tlačítko **Uložit** znovu. Uložená hodnota, kterou jste zadali na druhé záložce prohlížeče. Po zobrazení stránky index se zobrazí uložené hodnoty.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

Pro stránku odstranění Entity Framework detekuje konflikty souběžnosti způsobené někým jiným upravováním oddělení podobným způsobem. Když metoda HttpGet `Delete` zobrazí zobrazení potvrzení, zobrazení obsahuje původní `RowVersion` hodnotu ve skrytém poli. Tato hodnota je pak k dispozici metodě `Delete` HTTPPOST, která je volána, když uživatel potvrdí odstranění. Když Entity Framework vytvoří příkaz SQL DELETE, zahrnuje klauzuli WHERE s původní `RowVersion` hodnotou. Pokud tento příkaz má vliv na nulové řádky (což znamená, že řádek byl změněn po zobrazení stránky pro potvrzení odstranění), je vyvolána výjimka souběžnosti a metoda HttpGet `Delete` je volána s příznakem chyby nastaveným na hodnotu true, aby bylo možné znovu zobrazit potvrzovací stránka s chybovou zprávou Je také možné, že byly ovlivněny nulové řádky, protože řádek byl odstraněn jiným uživatelem, takže v takovém případě se nezobrazí žádná chybová zpráva.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Aktualizace metod Delete v řadiči oddělení

V *DepartmentsController.cs*nahraďte metodu HttpGet `Delete` následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

Metoda přijímá volitelný parametr, který označuje, zda se stránka po chybě souběžného zpracování znovu zobrazuje. Pokud má tento příznak hodnotu true a zadané oddělení už neexistuje, odstranil jiný uživatel. V takovém případě kód přesměruje na stránku indexu.  Pokud má tento příznak hodnotu true a oddělení existuje, změnil ho jiný uživatel. V takovém případě kód pošle do zobrazení chybovou zprávu pomocí `ViewData`.

Nahraďte kód v metodě HTTPPOST `Delete` (s názvem `DeleteConfirmed`) následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

V právě vygenerovaném kódu jste tuto metodu přijali pouze s ID záznamu:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Změnili jste tento parametr na instanci entity oddělení vytvořenou pomocí pořadače modelů. Díky tomu má EF přístup k hodnotě vlastnosti RowVersion kromě klíče záznamu.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Také jste změnili název metody akce z `DeleteConfirmed` na. `Delete` Generovaný kód použil název `DeleteConfirmed` , který metodě HTTPPOST poskytne jedinečný podpis. (CLR vyžaduje, aby byly přetížené metody pro různé parametry metody.) Teď, když jsou podpisy jedinečné, můžete s úmluvou MVC pracovat a používat stejný název pro metody Delete HttpPost a HttpGet.

Pokud je oddělení již odstraněno, `AnyAsync` vrátí metoda hodnotu false a aplikace se vrátí zpět do metody indexu.

Pokud je zachycena chyba souběžnosti, kód znovu zobrazí stránku pro potvrzení odstranění a poskytne příznak označující, že by měla zobrazit chybovou zprávu o souběžnosti.

### <a name="update-the-delete-view"></a>Aktualizace zobrazení pro odstranění

V *zobrazení/oddělení/odstranění. cshtml*nahraďte kód generovaný následujícím kódem, který přidá pole chybové zprávy a skrytá pole pro vlastnosti DepartmentID a rowversion. Změny jsou zvýrazněné.

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Provede tyto změny:

* Přidá chybovou zprávu mezi `h2` nadpisy a. `h3`

* Nahradí celý název v FirstMidName **správce** pole.

* Odebere pole RowVersion.

* Přidá skryté pole pro `RowVersion` vlastnost.

Spusťte aplikaci a pokračujte na stránku s indexem oddělení. Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro anglické oddělení a vyberte **otevřít na nové kartě**a pak na první kartě klikněte na odkaz **Upravit** pro anglické oddělení.

V prvním okně změňte jednu z hodnot a klikněte na **Uložit**:

![Stránka pro úpravu oddělení po změně před odstraněním](concurrency/_static/edit-after-change-for-delete.png)

Na druhé kartě klikněte na **Odstranit**. Zobrazí se chybová zpráva o souběžnosti a hodnoty oddělení se aktualizují s tím, co je aktuálně v databázi.

![Stránka pro potvrzení odstranění oddělení s chybou souběžnosti](concurrency/_static/delete-error.png)

Pokud znovu kliknete na tlačítko **Odstranit** , budete přesměrováni na stránku index, která ukazuje, že oddělení bylo odstraněno.

## <a name="update-details-and-create-views"></a>Aktualizovat podrobnosti a vytvořit zobrazení

Volitelně můžete vyčistit kód vygenerovaný v podrobnostech a vytvořit zobrazení.

Nahraďte kód v *zobrazeních/departments/details. cshtml* pro odstranění sloupce rowversion a zobrazením úplného jména správce.

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Chcete-li přidat možnost výběru do rozevíracího seznamu, nahraďte kód v *zobrazeních/departments/Create. cshtml* .

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Další zdroje

 Další informace o zpracování souběžnosti v EF Core naleznete v tématu [konflikty souběžnosti](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Dozvědělo se o konfliktech souběžnosti
> * Přidala se vlastnost sledování.
> * Vytvořen kontroler a zobrazení oddělení
> * Aktualizované zobrazení indexu
> * Aktualizované metody úprav
> * Aktualizace zobrazení pro úpravy
> * Testované konflikty souběžnosti
> * Aktualizace stránky odstranění
> * Aktualizované podrobnosti a vytváření zobrazení

Přejděte k dalšímu kurzu, kde se dozvíte, jak implementovat dědičnost tabulek na hierarchii pro entity instruktor a student.

> [!div class="nextstepaction"]
> [Generace Implementace dědičnosti tabulek na hierarchii](inheritance.md)
