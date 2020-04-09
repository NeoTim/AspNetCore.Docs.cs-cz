---
title: 'Kurz: Zpracování souběžnosti - ASP.NET MVC s EF Core'
description: Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat stejnou entitu ve stejnou dobu.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 6839e383093b993ff55095f26cf88cd68708f001
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657393"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Kurz: Zpracování souběžnosti - ASP.NET MVC s EF Core

V předchozích kurzech jste se naučili aktualizovat data. Tento kurz ukazuje, jak zpracovat konflikty, když více uživatelů aktualizovat stejnou entitu ve stejnou dobu.

Vytvoříte webové stránky, které pracují s entitou Oddělení a zpracovávají chyby souběžnosti. Následující ilustrace znázornit upravit a odstranit stránky, včetně některých zpráv, které jsou zobrazeny, pokud dojde ke konfliktu souběžnosti.

![Stránka Úpravy oddělení](concurrency/_static/edit-error.png)

![Stránka Odstranit oddělení](concurrency/_static/delete-error.png)

V tomto kurzu jste:

> [!div class="checklist"]
> * Informace o konfliktech souběžnosti
> * Přidání vlastnosti sledování
> * Vytvořit řadič a zobrazení oddělení
> * Aktualizovat zobrazení indexu
> * Aktualizovat metody úprav
> * Aktualizovat zobrazení pro úpravy
> * Testovat konflikty souběžnosti
> * Aktualizace stránky Odstranit
> * Aktualizovat podrobnosti a vytvořit zobrazení

## <a name="prerequisites"></a>Požadavky

* [Aktualizace souvisejících dat](update-related-data.md)

## <a name="concurrency-conflicts"></a>Konflikty souběžnosti

Ke konfliktu souběžnosti dochází, když jeden uživatel zobrazí data entity, aby ji mohl upravit, a potom jiný uživatel aktualizuje data stejné entity před zápisem změny prvního uživatele do databáze. Pokud nepovolíte zjišťování takových konfliktů, ten, kdo aktualizuje databázi naposledy přepíše změny jiného uživatele. V mnoha aplikacích je toto riziko přijatelné: pokud existuje jen málo uživatelů nebo málo aktualizací nebo pokud není opravdu důležité, pokud jsou některé změny přepsány, náklady na programování souběžnosti mohou převážit nad přínosem. V takovém případě není nutné konfigurovat aplikaci pro zpracování konfliktů souběžnosti.

### <a name="pessimistic-concurrency-locking"></a>Pesimistický souběžnost (zamykání)

Pokud vaše aplikace potřebuje zabránit náhodné ztrátě dat ve scénářích souběžnosti, jedním ze způsobů, jak to udělat, je použití uzamčení databáze. Tomu se říká pesimistické souběžnosti. Například před čtením řádku z databáze, požádat o zámek pro jen pro čtení nebo pro přístup k aktualizaci. Pokud uzamknete řádek pro přístup k aktualizaci, žádný jiný uživatel nemá povoleno uzamknout řádek buď pro přístup jen pro čtení, nebo pro přístup k aktualizaci, protože by získali kopii dat, která se právě mění. Pokud uzamknete řádek pro přístup jen pro čtení, ostatní mohou také zamknout pro přístup jen pro čtení, ale ne pro aktualizaci.

Správa zámků má nevýhody. Program může být složitý. Vyžaduje významné prostředky pro správu databáze a může způsobit problémy s výkonem, protože se zvyšuje počet uživatelů aplikace. Z těchto důvodů ne všechny systémy správy databáze podporují pesimistické souběžnosti. Entity Framework Core neposkytuje žádnou integrovanou podporu pro něj a tento kurz neukazuje, jak ji implementovat.

### <a name="optimistic-concurrency"></a>Optimistická metoda souběžného zpracování

Alternativou k pesimistické souběžnosti je optimistická souběžnost. Optimistická souběžnost znamená povolení konfliktů souběžnosti a následné odpovídající reakci, pokud ano. Jana například navštíví stránku Úpravy oddělení a změní částku rozpočtu pro anglické oddělení z 350 000,00 USD na 0,00 USD.

![Změna rozpočtu na 0](concurrency/_static/change-budget.png)

Před kliknutím na tlačítko **Uložit**jan navštíví stejnou stránku a změní pole Datum zahájení z 9.1.2007 na 1.

![Změna počátečního data na rok 2013](concurrency/_static/change-date.png)

Jan klikne na **Uložit** jako první a uvidí její změnu, když se prohlížeč vrátí na stránku Rejstřík.

![Rozpočet změněn na nulu](concurrency/_static/budget-zero.png)

Potom Jan klikne na **Uložit** na stránce Úpravy, která stále zobrazuje rozpočet 350 000,00 USD. Co se stane dál, je určena tím, jak zpracovat konflikty souběžnosti.

Některé z možností patří následující:

* Můžete sledovat, kterou vlastnost uživatel upravil, a aktualizovat pouze odpovídající sloupce v databázi.

     V ukázkovém scénáři by nedošlo ke ztrátě žádných dat, protože dva uživatelé aktualizovali různé vlastnosti. Příště, když někdo prochází anglické oddělení, uvidí jak Jane a John změny - datum zahájení 9 / 1 / 2013 a rozpočet nula dolarů. Tato metoda aktualizace může snížit počet konfliktů, které by mohly vést ke ztrátě dat, ale nemůže zabránit ztrátě dat, pokud jsou provedeny konkurenční změny ve stejné vlastnosti entity. Zda entity Framework funguje tímto způsobem závisí na tom, jak implementovat kód aktualizace. Často to není praktické ve webové aplikaci, protože to může vyžadovat udržovat velké množství stavu, aby bylo možné sledovat všechny původní hodnoty vlastností pro entitu, jakož i nové hodnoty. Udržování velkého množství stavu může ovlivnit výkon aplikace, protože vyžaduje prostředky serveru nebo musí být zahrnuto do samotné webové stránky (například ve skrytých polích) nebo do souboru cookie.

* Můžeš nechat Johnovu změnu přepsat Janeinu změnu.

     Příště někdo prochází anglické oddělení, uvidí 9 / 1 / 2013 a obnovenou hodnotu $ 350,000.00. To se nazývá *klient wins* nebo poslední *ve wins* scénář. (Všechny hodnoty od klienta mají přednost před tím, co je v úložišti dat.) Jak je uvedeno v úvodu k této části, pokud neprovedete žádné kódování pro zpracování souběžnosti, k tomu dojde automaticky.

* Můžete zabránit janově změně v aktualizaci v databázi.

     Obvykle byste zobrazit chybovou zprávu, zobrazit aktuální stav dat a umožnit mu znovu použít změny, pokud stále chce, aby byly tyto. Tento scénář se nazývá scénář *Store Wins.* (Hodnoty úložiště dat mají přednost před hodnotami odeslané klientem.) Budete implementovat store wins scénář v tomto kurzu. Tato metoda zajišťuje, že žádné změny jsou přepsány bez uživatele upozorněni na to, co se děje.

### <a name="detecting-concurrency-conflicts"></a>Zjišťování konfliktů souběžnosti

Konflikty můžete vyřešit `DbConcurrencyException` zpracováním výjimek, které lze vyvolat v rámci entit. Chcete-li vědět, kdy vyvolat tyto výjimky, entity framework musí být schopen detekovat konflikty. Proto je nutné odpovídajícím způsobem nakonfigurovat databázi a datový model. Mezi možnosti povolení zjišťování konfliktů patří následující:

* V databázové tabulce zahrňte sledovací sloupec, který lze použít k určení, kdy byl řádek změněn. Potom můžete nakonfigurovat entity Framework zahrnout tento sloupec v Where klauzule SQL Update nebo Delete příkazy.

     Datový typ sloupce sledování je `rowversion`obvykle . Hodnota `rowversion` je pořadové číslo, které se při každé aktualizaci řádku zintáží. V příkazu Aktualizovat nebo Odstranit obsahuje klauzule Where původní hodnotu sledovacího sloupce (původní verze řádku). Pokud byl řádek, který je aktualizován, změněn `rowversion` jiným uživatelem, hodnota ve sloupci se liší od původní hodnoty, takže příkaz Update nebo Delete nemůže najít řádek, který chcete aktualizovat z důvodu klauzule Where. Když entity Framework zjistí, že žádné řádky byly aktualizovány update nebo Delete příkaz (to znamená, když počet ovlivněných řádků je nula), interpretuje, že jako konflikt souběžnosti.

* Nakonfigurujte entity Framework tak, aby zahrnoval původní hodnoty každého sloupce v tabulce v commands Where update and Delete.

     Stejně jako v první možnosti, pokud se něco v řádku změnilo od prvního čtení řádku, klauzule Where nevrátí řádek k aktualizaci, který rozhraní Entity Framework interpretuje jako konflikt souběžnosti. Pro databázové tabulky, které mají mnoho sloupců, tento přístup může mít za následek velmi velké Where klauzule a může vyžadovat udržovat velké množství stavu. Jak již bylo uvedeno dříve, udržování velkého množství stavu může ovlivnit výkon aplikace. Proto tento přístup se obecně nedoporučuje a není metoda použitá v tomto kurzu.

     Pokud chcete implementovat tento přístup k souběžnosti, musíte označit všechny vlastnosti bez primárního klíče v entitě, pro kterou chcete sledovat souběžnost, přidáním atributu. `ConcurrencyCheck` Tato změna umožňuje entity Framework zahrnout všechny sloupce v SQL Where klauzule Update a Delete příkazy.

Ve zbývající části tohoto kurzu přidáte `rowversion` vlastnost sledování do entity oddělení, vytvoříte řadič a zobrazení a otestujete, zda vše funguje správně.

## <a name="add-a-tracking-property"></a>Přidání vlastnosti sledování

V *souboru Models/Department.cs*přidejte vlastnost sledování s názvem RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Atribut `Timestamp` určuje, že tento sloupec bude zahrnut do příkazu Where update and Delete odeslaných do databáze. Atribut je `Timestamp` volán, protože předchozí verze `timestamp` serveru SQL Server `rowversion` používaly datový typ SQL před tím, než jej sql nahradil. Typ .NET `rowversion` pro je bajtové pole.

Pokud dáváte přednost použití fluent API, `IsConcurrencyToken` můžete použít metodu (v *Data/SchoolContext.cs)* k určení vlastnosti sledování, jak je znázorněno v následujícím příkladu:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Přidáním vlastnosti jste změnili model databáze, takže je třeba provést další migraci.

Uložte změny a vytvořte projekt a zadejte do příkazového okna následující příkazy:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Vytvořit řadič a zobrazení oddělení

Přilejme scaffold a departments controller a zobrazení jako dříve pro studenty, kurzy a instruktory.

![Lešení oddělení](concurrency/_static/add-departments-controller.png)

V *souboru DepartmentsController.cs* změňte všechny čtyři výskyty "FirstMidName" na "FullName" tak, aby rozevírací seznamy správce oddělení bude obsahovat celé jméno instruktora, nikoli pouze příjmení.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Aktualizovat zobrazení indexu

Modul zasazení vytvořil sloupec RowVersion v zobrazení Rejstříku, ale toto pole by se nemělo zobrazit.

Nahraďte kód v *zobrazení/departments/Index.cshtml* následujícím kódem.

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Tím se změní nadpis na "Oddělení", odstraní sloupec RowVersion a zobrazí celé jméno namísto křestního jména pro správce.

## <a name="update-edit-methods"></a>Aktualizovat metody úprav

V metodě `Edit` HttpGet `Details` i metodě přidejte `AsNoTracking`. V Metodě `Edit` HttpGet přidejte dychtivé načítání pro správce.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

Nahraďte existující kód `Edit` metody HttpPost následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Kód začíná pokusem o čtení oddělení, které má být aktualizováno. Pokud `FirstOrDefaultAsync` metoda vrátí hodnotu null, oddělení bylo odstraněno jiným uživatelem. V takovém případě kód používá zaúčtované hodnoty formuláře k vytvoření entity oddělení, aby bylo možné znovu zobrazit stránku Úpravy s chybovou zprávou. Jako alternativu byste nemuseli znovu vytvořit entitu oddělení, pokud zobrazíte pouze chybovou zprávu bez opětovného zobrazení polí oddělení.

Zobrazení ukládá původní `RowVersion` hodnotu ve skrytém poli a tato `rowVersion` metoda obdrží tuto hodnotu v parametru. Před voláním `SaveChanges`budete muset umístit `RowVersion` tuto původní `OriginalValues` hodnotu vlastnosti do kolekce pro entitu.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Potom při entity Framework vytvoří příkaz SQL UPDATE, tento příkaz bude obsahovat WHERE `RowVersion` klauzule, která hledá řádek, který má původní hodnotu. Pokud žádné řádky jsou ovlivněny příkazem UPDATE `RowVersion` (žádné řádky mají původní `DbUpdateConcurrencyException` hodnotu), entity framework vyvolá výjimku.

Kód v bloku catch pro tuto výjimku získá entitu `Entries` ovlivněné oddělení, která má aktualizované hodnoty z vlastnosti na objektu výjimky.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

Kolekce `Entries` bude mít `EntityEntry` pouze jeden objekt.  Tento objekt můžete použít k získání nových hodnot zadaných uživatelem a aktuálních hodnot databáze.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Kód přidá vlastní chybovou zprávu pro každý sloupec, který má hodnoty databáze odlišné od toho, co uživatel zadal na stránce Upravit (pouze jedno pole je zde zobrazeno pro stručnost).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Nakonec kód nastaví `RowVersion` hodnotu `departmentToUpdate` na novou hodnotu načtenou z databáze. Tato `RowVersion` nová hodnota bude uložena ve skrytém poli při opětovném zobrazení stránky Úprava a při příštím klepnutí na tlačítko **Uložit**budou zachyceny pouze chyby souběžnosti, ke kterým dojde od opětovného zobrazení stránky Úpravy.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

Příkaz `ModelState.Remove` je povinný, `ModelState` protože `RowVersion` má starou hodnotu. V zobrazení má `ModelState` hodnota pole přednost před hodnotami vlastností modelu, pokud jsou k dispozici obě.

## <a name="update-edit-view"></a>Aktualizovat zobrazení pro úpravy

V *části Zobrazení/Oddělení/Edit.cshtml*proveďte následující změny:

* Přidejte skryté pole `RowVersion` pro uložení hodnoty vlastnosti bezprostředně `DepartmentID` za skrytým polem pro vlastnost.

* Do rozevíracího seznamu přidejte možnost Vybrat správce.

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Testovat konflikty souběžnosti

Spusťte aplikaci a přejděte na stránku Index oddělení. Klikněte pravým tlačítkem myši na hypertextový odkaz **Upravit** pro anglické oddělení a **vyberte Otevřít na nové kartě**a potom klikněte na odkaz **Upravit** pro anglické oddělení. Dvě karty prohlížeče nyní zobrazují stejné informace.

Změňte pole na první kartě prohlížeče a klepněte na tlačítko **Uložit**.

![Oddělení Upravit stránku 1 po změně](concurrency/_static/edit-after-change-1.png)

Prohlížeč zobrazí stránku Rejstřík se změněnou hodnotou.

Změna pole na druhé kartě prohlížeče

![Oddělení Upravit stránku 2 po změně](concurrency/_static/edit-after-change-2.png)

Klikněte na **Uložit**. Zobrazí se chybová zpráva:

![Chybová zpráva Upravit stránku oddělení](concurrency/_static/edit-error.png)

Klikněte znovu na **Uložit.** Hodnota zadaná na druhé kartě prohlížeče se uloží. Uložené hodnoty se zobrazí, když se zobrazí stránka Rejstřík.

## <a name="update-the-delete-page"></a>Aktualizace stránky Odstranit

Pro Odstranit stránky entity framework zjistí konflikty souběžnosti způsobené někdo jiný úpravy oddělení podobným způsobem. Když metoda `Delete` HttpGet zobrazí zobrazení potvrzení, zobrazení `RowVersion` obsahuje původní hodnotu ve skrytém poli. Tato hodnota je pak k `Delete` dispozici pro metodu HttpPost, která je volána, když uživatel potvrdí odstranění. Když entity Framework vytvoří příkaz SQL DELETE, obsahuje klauzuli WHERE s původní `RowVersion` hodnotou. Pokud příkaz má za následek nulové řádky ovlivněny (což znamená, že řádek byl změněn po odstranění potvrzení `Delete` stránka byla zobrazena), je vyvolána výjimka souběžnosti a httpGet metoda je volána s příznakem chyby nastavena na hodnotu true, aby se znovu zobrazit potvrzovací stránku s chybovou zprávou. Je také možné, že byly ovlivněny nulové řádky, protože řádek byl odstraněn jiným uživatelem, takže v takovém případě se nezobrazí žádná chybová zpráva.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Aktualizace metod Delete v řadiči Departments

V *DepartmentsController.cs*nahraďte `Delete` metodu HttpGet následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

Metoda přijímá volitelný parametr, který označuje, zda je stránka znovu zobrazena po chybě souběžnosti. Pokud je tento příznak pravdivý a zadané oddělení již neexistuje, byl odstraněn jiným uživatelem. V takovém případě kód přesměruje na stránku Index.  Pokud je tento příznak pravdivý a oddělení existuje, byl změněn jiným uživatelem. V takovém případě kód odešle chybovou `ViewData`zprávu zobrazení pomocí .

Nahraďte kód v `Delete` metodě `DeleteConfirmed`HttpPost (s názvem) následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

V kódu scaffolded, který jste právě nahradili, tato metoda přijala pouze ID záznamu:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Tento parametr jste změnili na instanci entity oddělení vytvořenou pořadačmodelu. To poskytuje ef přístup k RowVersion hodnotu vlastnosti kromě klíče záznamu.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Změnili jste také název `DeleteConfirmed` metody `Delete`akce z na . Kód scaffolded používal `DeleteConfirmed` název k tomu, aby metoda HttpPost měla jedinečný podpis. (CLR vyžaduje přetížené metody mít různé parametry metody.) Nyní, když podpisy jsou jedinečné, můžete se držet konvence MVC a použít stejný název pro metody httppost a httpget odstranění.

Pokud je oddělení již `AnyAsync` odstraněno, metoda vrátí false a aplikace se vrátí zpět k metodě Index.

Pokud je zachycena chyba souběžnosti, kód znovu zobrazí stránku potvrzení odstranit a poskytuje příznak, který označuje, že by měl zobrazit chybovou zprávu souběžnosti.

### <a name="update-the-delete-view"></a>Aktualizace zobrazení Odstranit

V *zobrazení/departments/Delete.cshtml*nahraďte kód sklopný pomocí následujícího kódu, který přidá pole chybové zprávy a skrytá pole pro vlastnosti DepartmentID a RowVersion. Změny jsou zvýrazněny.

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Tím se změní následující změny:

* Přidá chybovou zprávu `h2` `h3` mezi nadpisy a nadpisy.

* Nahradí firstmidname v poli **Správce** názvem FullName.

* Odebere pole RowVersion.

* Přidá skryté pole `RowVersion` pro vlastnost.

Spusťte aplikaci a přejděte na stránku Index oddělení. Klikněte pravým tlačítkem myši na hypertextový odkaz **Odstranit** pro anglické oddělení a **vyberte otevřít na nové kartě**a potom na první kartě klikněte na odkaz **Upravit** hypertextový odkaz pro anglické oddělení.

V prvním okně změňte jednu z hodnot a klepněte na **Uložit**:

![Stránka Úpravy oddělení po změně před odstraněním](concurrency/_static/edit-after-change-for-delete.png)

Na druhé kartě klepněte na **tlačítko Odstranit**. Zobrazí se chybová zpráva souběžnosti a hodnoty oddělení jsou aktualizovány s tím, co je aktuálně v databázi.

![Stránka potvrzení odstranění oddělení s chybou souběžnosti](concurrency/_static/delete-error.png)

Pokud znovu kliknete na **Odstranit,** budete přesměrováni na stránku Rejstřík, která ukazuje, že oddělení bylo odstraněno.

## <a name="update-details-and-create-views"></a>Aktualizovat podrobnosti a vytvořit zobrazení

Volitelně můžete vyčistit kód scaffolded v podrobnosti a vytvořit zobrazení.

Nahraďte kód v *zobrazení/oddělení/Details.cshtml* odstranit RowVersion sloupec a zobrazit úplný název správce.

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Nahraďte kód v *zobrazení/oddělení/Create.cshtml* a přidejte možnost Select do rozevíracího seznamu.

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Další zdroje

 Další informace o tom, jak zpracovat souběžnost v ef core, naleznete v [tématu konflikty souběžnosti](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Informace o konfliktech souběžnosti
> * Přidána vlastnost sledování
> * Řadič a zobrazení vytvořených oddělení
> * Aktualizované zobrazení indexu
> * Aktualizované metody úprav
> * Aktualizované zobrazení úprav
> * Testované konflikty souběžnosti
> * Stránka Odstranit byla aktualizována
> * Aktualizované podrobnosti a vytvoření zobrazení

Přejdete k dalšímu kurzu, kde se dozvíte, jak implementovat dědičnost tabulky na hierarchii pro entity instruktora a studenta.

> [!div class="nextstepaction"]
> [Další: Implementace dědičnosti tabulky na hierarchii](inheritance.md)
