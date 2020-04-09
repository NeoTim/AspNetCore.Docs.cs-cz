---
title: Razor Stránky s EF core v ASP.NET jádrem - CRUD - 2 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit, číst, aktualizovat, odstranit pomocí EF Core.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/crud
ms.openlocfilehash: 05519852fab22bd3ad5b77e3494b49191448286f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665646"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a>Razor Stránky s EF core v ASP.NET jádrem - CRUD - 2 z 8

[Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), a Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu je kód CRUD (vytvořit, číst, aktualizovat, odstranit) zkontrolován a přizpůsobit.

## <a name="no-repository"></a>Žádné úložiště

Někteří vývojáři používají vrstvu služby nebo vzor úložiště k vytvoření vrstvy abstrakce mezi ujtou vrstvou (Razor Pages) a vrstvou přístupu k datům. Tento výukový program to nedělá. Chcete-li minimalizovat složitost a udržet kurz zaměřený na EF Core, EF core kód je přidán přímo do tříd modelu stránky. 

## <a name="update-the-details-page"></a>Aktualizace stránky Podrobnosti

Kód scaffolded pro stránky Studenti neobsahuje data zápisu. V této části přidáte zápisy na stránku Podrobnosti.

### <a name="read-enrollments"></a>Čtení registrací

Chcete-li na stránce zobrazit údaje o zápisu studenta, musíte si je přečíst. Kód scaffolded v *Pages/Students/Details.cshtml.cs* čte pouze data studentů bez údajů o zápisu:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Nahraďte metodu `OnGetAsync` následujícím kódem pro čtení dat zápisu pro vybraného studenta. Změny jsou zvýrazněny.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) a [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) metody způsobit kontext `Student.Enrollments` načíst navigační vlastnost `Enrollment.Course` a v rámci každé registrace navigační vlastnost. Tyto metody jsou podrobně zkoumány v kurzu [související čtení dat.](xref:data/ef-rp/read-related-data)

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) Metoda zlepšuje výkon ve scénářích, kde entity vrácené nejsou aktualizovány v aktuálním kontextu. `AsNoTracking`je popsána dále v tomto kurzu.

### <a name="display-enrollments"></a>Zobrazit registrace

Nahraďte kód v *pages/students/details.cshtml* následujícím kódem, který zobrazí seznam registrací. Změny jsou zvýrazněny.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Předchozí kód smyčky prostřednictvím entit `Enrollments` v navigační vlastnosti. Pro každou registraci se zobrazí název kurzu a hodnocení. Název kurzu se načte z entity Kurz, `Course` která je uložena ve vlastnosti navigace entity Registrace.

Spusťte aplikaci, vyberte kartu **Studenti** a klikněte na odkaz **Podrobnosti** pro studenta. Zobrazí se seznam kurzů a hodnocení pro vybraného studenta.

### <a name="ways-to-read-one-entity"></a>Způsoby čtení jedné entity

Generovaný kód používá [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) ke čtení jedné entity. Tato metoda vrátí null, pokud není nic nalezeno; v opačném případě vrátí první nalezený řádek, který splňuje kritéria filtru dotazu. `FirstOrDefaultAsync`je obecně lepší volbou než následující alternativy:

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - vyvolá výjimku, pokud existuje více než jedna entita, která splňuje filtr dotazu. Chcete-li zjistit, zda více než jeden `SingleOrDefaultAsync` řádek může být vrácena dotazem, pokusí se načíst více řádků. Tato práce navíc není nutná, pokud dotaz může vrátit pouze jednu entitu, jako při hledání na jedinečný klíč.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Najde entitu s primárním klíčem (PK). Pokud entita s PK je sledována kontextem, je vrácena bez požadavku do databáze. Tato metoda je optimalizována pro vyhledat jednu `Include` `FindAsync`entitu, ale nelze volat s .  Takže pokud jsou zapotřebí související data, `FirstOrDefaultAsync` je lepší volbou.

### <a name="route-data-vs-query-string"></a>Směrovat data vs. řetězec dotazu

Adresa URL stránky Podrobnosti je `https://localhost:<port>/Students/Details?id=1`. Hodnota primárního klíče entity je v řetězci dotazu. Někteří vývojáři dávají přednost předání hodnoty `https://localhost:<port>/Students/Details/1`klíče v datech trasy: . Další informace naleznete [v tématu Aktualizace generovaného kódu](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Aktualizace stránky Vytvořit

Kód scaffolded `OnPostAsync` pro vytvořit stránku je náchylný k [overpostování](#overposting). Nahraďte metodu `OnPostAsync` v *Pages/Students/Create.cshtml.cs* následujícím kódem.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Předchozí kód vytvoří objekt Student a potom použije zaúčtovaná pole formuláře k aktualizaci vlastností objektu Student. Metoda [TryUpdateModelAsync:](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_)

* Použije zaúčtované hodnoty formuláře z vlastnosti [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) v [modelu PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).
* Aktualizuje pouze uvedené`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`vlastnosti ( ).
* Vyhledá pole formuláře s předponou "student". Například, `Student.FirstMidName`. Nerozlišuje se na malá a velká písmena.
* Používá systém [vazby modelu](xref:mvc/models/model-binding) k převodu hodnot formuláře `Student` z řetězců na typy v modelu. Například `EnrollmentDate` musí být převedenna DateTime.

Spusťte aplikaci a vytvořte studentskou entitu pro testování stránky Vytvořit.

## <a name="overposting"></a>Přeúčtování

Použití `TryUpdateModel` k aktualizaci polí se zaúčtovaných hodnot je osvědčeným postupem zabezpečení, protože zabraňuje přeúčtování. Předpokládejme například, že `Secret` entita Student obsahuje vlastnost, kterou by tato webová stránka neměla aktualizovat nebo přidat:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

I v případě, že `Secret` aplikace nemá pole na vytvoření nebo aktualizaci `Secret` Razor Page, hacker může nastavit hodnotu overpostování. Hacker by mohl použít nástroj, jako je Fiddler, nebo `Secret` napsat nějaký JavaScript, k odeslání hodnoty formuláře. Původní kód neomezuje pole, která pořadač modelu používá při vytváření instance Student.

Jakákoli hodnota, kterou `Secret` hacker zadal pro pole formuláře, je v databázi aktualizována. Následující obrázek znázorňuje nástroj `Secret` Šumař, který přidává pole (s hodnotou "OverPost") k zaúčtovanéhodnotě formuláře.

![Fiddler přidání tajné pole](../ef-mvc/crud/_static/fiddler.png)

Hodnota OverPost je úspěšně přidána `Secret` do vlastnosti vloženého řádku. To se stane, i když `Secret` návrhář aplikace nikdy nezamýšlel vlastnost nastavit pomocí vytvořit stránku.

### <a name="view-model"></a>Zobrazit model

Zobrazit modely poskytují alternativní způsob, jak zabránit overpostování.

Aplikační model se často nazývá model domény. Model domény obvykle obsahuje všechny vlastnosti vyžadované odpovídající entitou v databázi. Model zobrazení obsahuje pouze vlastnosti potřebné pro rozhraní, pro které se používá (například vytvořit stránku).

Kromě modelu zobrazení některé aplikace používají model vazby nebo vstupní model k předání dat mezi třídou modelu stránky Razor Pages a prohlížečem. 

Zvažte `Student` následující model zobrazení:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

Následující kód používá `StudentVM` model zobrazení k vytvoření nového studenta:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Metoda [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) nastaví hodnoty tohoto objektu čtením hodnot z jiného objektu [PropertyValues.](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) `SetValues`používá odpovídající název vlastnosti. Typ modelu zobrazení nemusí souviset s typem modelu, musí mít pouze vlastnosti, které odpovídají.

Použití `StudentVM` vyžaduje [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) aktualizovat `StudentVM` použít `Student`spíše než .

## <a name="update-the-edit-page"></a>Aktualizace stránky Úpravy

V *části Stránky/Studenti/Edit.cshtml.cs*nahraďte metody `OnGetAsync` a `OnPostAsync` následujícím kódem.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Změny kódu jsou podobné stránce Vytvořit s několika výjimkami:

* `FirstOrDefaultAsync`byla nahrazena službou [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). Pokud nemusíte zahrnout související data, `FindAsync` je efektivnější.
* `OnPostAsync`má `id` parametr.
* Aktuální student je načíst z databáze, spíše než vytvořit prázdný student.

Spusťte aplikaci a otestujte ji vytvořením a úpravou studenta.

## <a name="entity-states"></a>Stavy entity

Kontext databáze sleduje, zda entity v paměti jsou synchronizovány s jejich odpovídající řádky v databázi. Tato informace o sledování určuje, co se stane, když [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána. Například při předání nové entity metodě [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) je stav této entity nastaven na [hodnotu Přidáno](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Při `SaveChangesAsync` volání kontext databáze vydá příkaz SQL INSERT.

Účetní jednotka se může napletat v jednom z [následujících stavů](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: Entita v databázi ještě neexistuje. Metoda `SaveChanges` vydá příkaz INSERT.

* `Unchanged`: S touto entitou není nutné ukládat žádné změny. Entita má tento stav při čtení z databáze.

* `Modified`: Některé nebo všechny hodnoty vlastností entity byly změněny. Metoda `SaveChanges` vydává příkaz UPDATE.

* `Deleted`: Entita byla označena k odstranění. Metoda `SaveChanges` vydává příkaz DELETE.

* `Detached`: Entita není sledována kontextem databáze.

V aplikaci klasické pracovní plochy se změny stavu obvykle nastavují automaticky. Entita je přečtena, jsou provedeny změny a `Modified`stav entity se automaticky změní na . Volání `SaveChanges` generuje příkaz SQL UPDATE, který aktualizuje pouze změněné vlastnosti.

Ve webové aplikaci, `DbContext` která čte entitu a zobrazuje data, je uvolněna po vykreslení stránky. Při volání `OnPostAsync` metody stránky je nový webový požadavek a s novou `DbContext`instancí . Opětovné čtení entity v tomto novém kontextu simuluje zpracování plochy.

## <a name="update-the-delete-page"></a>Aktualizace stránky Odstranit

V této části implementovat vlastní chybovou `SaveChanges` zprávu při volání nezdaří.

Nahraďte kód v *Pages/Students/Delete.cshtml.cs* následujícím kódem. Změny jsou zvýrazněny (kromě vyčištění `using` příkazů).

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

Předchozí kód přidá volitelný `saveChangesError` parametr `OnGetAsync` k podpisu metody. `saveChangesError`označuje, zda byla metoda volána po selhání odstranění objektu studenta. Operace odstranění může selhat z důvodu přechodných problémů se sítí. Přechodné síťové chyby jsou pravděpodobnější, když je databáze v cloudu. Parametr `saveChangesError` je false při `OnGetAsync` odstranění stránky je volána z ui. Když `OnGetAsync` je `OnPostAsync` volána (protože operace `saveChangesError` odstranění se nezdařilo), parametr je true.

Metoda `OnPostAsync` načte vybranou entitu [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) a pak zavolá metodu `Deleted`Remove a nastaví stav entity na . Při `SaveChanges` volání je generován příkaz SQL DELETE. Pokud `Remove` selže:

* Je zachycena výjimka databáze.
* Metoda Delete `OnGetAsync` pages je `saveChangesError=true`volána pomocí .

Přidejte chybovou zprávu na stránku Odstranit holicí strojek *(Pages/Students/Delete.cshtml*):

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Spusťte aplikaci a odstraňte studenta a otestujte stránku Delete.

## <a name="next-steps"></a>Další kroky

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/intro)
> [Další kurz](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu je kód CRUD (vytvořit, číst, aktualizovat, odstranit) zkontrolován a přizpůsobit.

Chcete-li minimalizovat složitost a zachovat tyto kurzy zaměřené na EF Core, EF core kód se používá v modelech stránky. Někteří vývojáři používají vrstvu služby nebo vzor úložiště v aplikaci k vytvoření vrstvy abstrakce mezi ui (Razor Pages) a vrstvou přístupu k datům.

V tomto kurzu jsou zkontrolovány stránky Create, Edit, Delete a Details Razor ve složce *Students.*

Kód skládaný pomocí šaškáře používá následující vzorek pro stránky Vytvořit, Upravit a Odstranit:

* Získejte a zobrazte požadovaná `OnGetAsync`data pomocí metody HTTP GET .
* Uložte změny dat pomocí metody `OnPostAsync`HTTP POST .

Stránky Rejstřík a podrobnosti získají a zobrazí požadovaná data pomocí metody HTTP GET`OnGetAsync`

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync vs. FirstOrDefaultAsync

Generovaný kód používá [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), který je obecně upřednostňován před [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

 `FirstOrDefaultAsync`je efektivnější `SingleOrDefaultAsync` než při načítání jedné entity:

* Pokud kód potřebuje ověřit, že není více než jedna entita vrácena z dotazu.
* `SingleOrDefaultAsync`načte více dat a dělá zbytečnou práci.
* `SingleOrDefaultAsync`vyvolá výjimku, pokud existuje více než jedna entita, která odpovídá součásti filtru.
* `FirstOrDefaultAsync`nevyvolá, pokud je více než jedna entita, která se hodí k části filtru.

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

Ve velké části kód scaffolded [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) lze `FirstOrDefaultAsync`použít místo .

`FindAsync`:

* Najde entitu s primárním klíčem (PK). Pokud entita s PK je sledována kontextem, je vrácena bez požadavku do DB.
* Je jednoduché a stručné.
* Je optimalizován pro vyhledat jednu entitu.
* V některých situacích může mít výhody perf, ale to se u typických webových aplikací stává jen zřídka.
* Implicitně používá [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) místo [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

Ale pokud chcete `Include` jiné entity, `FindAsync` pak již není vhodné. To znamená, že budete `FindAsync` muset opustit a přesunout se do dotazu, jak vaše aplikace postupuje.

## <a name="customize-the-details-page"></a>Přizpůsobení stránky Podrobnosti

Přejděte `Pages/Students` na stránku. Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značky ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru *Pages/Students/Index.cshtml.*

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Spusťte aplikaci a vyberte odkaz **Podrobnosti.** Adresa URL je `http://localhost:5000/Students/Details?id=2`formuláře . ID studenta je předáno`?id=2`pomocí řetězce dotazu ( ).

Aktualizujte stránky Upravit, Podrobnosti a `"{id:int}"` Odstranit holicí strojek, abyste použili šablonu trasy. Změňte direktivu stránky `@page` `@page "{id:int}"`pro každou z těchto stránek z na .

Požadavek na stránku se šablonou postupu {id:int}, která **neobsahuje** hodnotu celého postupu, vrátí chybu HTTP 404 (nebyl nalezen). Například `http://localhost:5000/Students/Details` vrátí chybu 404. Chcete-li, aby id `?` volitelné, připojit k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Spusťte aplikaci, klikněte na odkaz Podrobnosti a ověřte, zda adresa URL předává ID jako data trasy (`http://localhost:5000/Students/Details/2`).

Neměňte `@page` globálně `@page "{id:int}"`na , tím přerušíte odkazy na stránky Domů a Vytvořit.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Přidání souvisejících dat

Kód scaffolded pro stránku Studentindex neobsahuje `Enrollments` vlastnost. V této části je `Enrollments` obsah kolekce zobrazen na stránce Podrobnosti.

Metoda `OnGetAsync` *Pages/Students/Details.cshtml.cs* používá `FirstOrDefaultAsync` metodu k `Student` načtení jedné entity. Přidejte následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) a [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) metody způsobit kontext `Student.Enrollments` načíst navigační vlastnost `Enrollment.Course` a v rámci každé registrace navigační vlastnost. Tyto metody jsou podrobně zkoumány v kurzu týkající se čtení dat.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) Metoda zlepšuje výkon ve scénářích, kdy entity vrácené nejsou aktualizovány v aktuálním kontextu. `AsNoTracking`je popsána dále v tomto kurzu.

### <a name="display-related-enrollments-on-the-details-page"></a>Zobrazení souvisejících registrací na stránce Podrobnosti

Otevřít *stránky/Studenti/Details.cshtml*. Přidejte následující zvýrazněný kód, který zobrazí seznam registrací:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Pokud je odsazení kódu po vložení kódu nesprávné, opravte jej stisknutím kláves CTRL-K-D.

Předchozí kód smyčky prostřednictvím entit `Enrollments` v navigační vlastnosti. Pro každou registraci se zobrazí název kurzu a hodnocení. Název kurzu se načte z entity Kurz, `Course` která je uložena ve vlastnosti navigace entity Registrace.

Spusťte aplikaci, vyberte kartu **Studenti** a klikněte na odkaz **Podrobnosti** pro studenta. Zobrazí se seznam kurzů a hodnocení pro vybraného studenta.

## <a name="update-the-create-page"></a>Aktualizace stránky Vytvořit

Aktualizujte `OnPostAsync` metodu na *pages/students/create.cshtml.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Zkontrolujte kód [TryUpdateModelAsync:](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_)

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

V předchozím kódu `TryUpdateModelAsync<Student>` se pokusí `emptyStudent` aktualizovat objekt pomocí zaúčtovaných hodnot formuláře z vlastnosti [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) v [modelu PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel). `TryUpdateModelAsync`aktualizuje pouze uvedené`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`vlastnosti ( ).

V předchozím vzorku:

* Druhý argument`"student", // Prefix`( ) je předpona používá k vyhledávání hodnot. Nerozlišuje se na malá a velká písmena.
* Zaúčtované hodnoty formuláře jsou převedeny na typy v `Student` modelu pomocí [vazby modelu](xref:mvc/models/model-binding).

<a id="overpost"></a>

### <a name="overposting"></a>Přeúčtování

Použití `TryUpdateModel` k aktualizaci polí se zaúčtovaných hodnot je osvědčeným postupem zabezpečení, protože zabraňuje přeúčtování. Předpokládejme například, že `Secret` entita Student obsahuje vlastnost, kterou by tato webová stránka neměla aktualizovat nebo přidat:

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

I v případě, že `Secret` aplikace nemá pole na create / update `Secret` Razor Page, hacker může nastavit hodnotu overpostování. Hacker by mohl použít nástroj, jako je Fiddler, nebo `Secret` napsat nějaký JavaScript, k odeslání hodnoty formuláře. Původní kód neomezuje pole, která pořadač modelu používá při vytváření instance Student.

Jakákoli hodnota, kterou `Secret` hacker zadal pro pole formuláře, je v db aktualizována. Následující obrázek znázorňuje nástroj `Secret` Šumař, který přidává pole (s hodnotou "OverPost") k zaúčtovanéhodnotě formuláře.

![Fiddler přidání tajné pole](../ef-mvc/crud/_static/fiddler.png)

Hodnota OverPost je úspěšně přidána `Secret` do vlastnosti vloženého řádku. Návrhář aplikace nikdy `Secret` nezamýšlel nastavit vlastnost pomocí stránky Vytvořit.

<a name="vm"></a>

### <a name="view-model"></a>Zobrazit model

Model zobrazení obvykle obsahuje podmnožinu vlastností zahrnutých v modelu používaném aplikací. Aplikační model se často nazývá model domény. Model domény obvykle obsahuje všechny vlastnosti vyžadované odpovídající entitou v DB. Model zobrazení obsahuje pouze vlastnosti potřebné pro vrstvu ui (například vytvořit stránku). Kromě modelu zobrazení některé aplikace používají model vazby nebo vstupní model k předání dat mezi třídou modelu stránky Razor Pages a prohlížečem. Zvažte `Student` následující model zobrazení:

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Zobrazit modely poskytují alternativní způsob, jak zabránit overpostování. Model pohledu obsahuje pouze vlastnosti, které mají být zobrazeny (zobrazeny) nebo aktualizovány.

Následující kód používá `StudentVM` model zobrazení k vytvoření nového studenta:

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Metoda [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) nastaví hodnoty tohoto objektu čtením hodnot z jiného objektu [PropertyValues.](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) `SetValues`používá odpovídající název vlastnosti. Typ modelu zobrazení nemusí souviset s typem modelu, musí mít pouze vlastnosti, které odpovídají.

Použití `StudentVM` vyžaduje [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) aktualizovat `StudentVM` použít `Student`spíše než .

V Razor Pages `PageModel` je odvozená třída model pohledu.

## <a name="update-the-edit-page"></a>Aktualizace stránky Úpravy

Aktualizujte model stránky pro stránku Úpravy. Hlavní změny jsou zvýrazněny:

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Změny kódu jsou podobné stránce Vytvořit s několika výjimkami:

* `OnPostAsync`má volitelný `id` parametr.
* Aktuální student je načten z DB, spíše než vytvořit prázdný student.
* `FirstOrDefaultAsync`byla nahrazena službou [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). `FindAsync`je dobrou volbou při výběru entity z primárního klíče. Další informace naleznete v [tématu FindAsync.](#FindAsync)

### <a name="test-the-edit-and-create-pages"></a>Testování stránek Úpravy a vytváření

Vytvořte a upravte několik studentských entit.

## <a name="entity-states"></a>Stavy entity

Kontext DB udržuje přehled o tom, zda jsou entity v paměti synchronizovány s odpovídajícími řádky v DB. Informace o synchronizaci kontextu DB určuje, co se stane, když [saveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána. Například při předání nové entity metodě [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) je stav této entity nastaven na [hodnotu Přidáno](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Při `SaveChangesAsync` volání kontextu DB vydá příkaz SQL INSERT.

Účetní jednotka se může napletat v jednom z [následujících stavů](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: Entita v databázi ještě neexistuje. Metoda `SaveChanges` vydá příkaz INSERT.

* `Unchanged`: S touto entitou není nutné ukládat žádné změny. Entita má tento stav při čtení z DB.

* `Modified`: Některé nebo všechny hodnoty vlastností entity byly změněny. Metoda `SaveChanges` vydává příkaz UPDATE.

* `Deleted`: Entita byla označena k odstranění. Metoda `SaveChanges` vydává příkaz DELETE.

* `Detached`: Entita není sledována kontextem DB.

V aplikaci klasické pracovní plochy se změny stavu obvykle nastavují automaticky. Entita je přečtena, jsou provedeny změny a `Modified`stav entity se automaticky změní na . Volání `SaveChanges` generuje příkaz SQL UPDATE, který aktualizuje pouze změněné vlastnosti.

Ve webové aplikaci, `DbContext` která čte entitu a zobrazuje data, je uvolněna po vykreslení stránky. Při volání `OnPostAsync` metody stránky je nový webový požadavek a s novou `DbContext`instancí . Opětovné čtení entity v tomto novém kontextu simuluje zpracování plochy.

## <a name="update-the-delete-page"></a>Aktualizace stránky Odstranit

V této části je přidán kód k implementaci vlastní `SaveChanges` chybové zprávy při volání nezdaří. Přidejte řetězec, který bude obsahovat možné chybové zprávy:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Nahraďte metodu `OnGetAsync` následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Předchozí kód obsahuje volitelný `saveChangesError`parametr . `saveChangesError`označuje, zda byla metoda volána po selhání odstranění objektu studenta. Operace odstranění může selhat z důvodu přechodných problémů se sítí. Přechodné síťové chyby jsou pravděpodobnější v cloudu. `saveChangesError`je false, když `OnGetAsync` je stránka Delete volána z ui. Když `OnGetAsync` je `OnPostAsync` volána (protože operace `saveChangesError` odstranění se nezdařilo), parametr je true.

### <a name="the-delete-pages-onpostasync-method"></a>Metoda Odstranit stránky OnPostAsync

`OnPostAsync` Nahraďte následující kód:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Předchozí kód načte vybranou entitu [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) a pak zavolá metodu `Deleted`Remove a nastaví stav entity na . Při `SaveChanges` volání je generován příkaz SQL DELETE. Pokud `Remove` selže:

* Db výjimka je zachycena.
* Metoda Delete `OnGetAsync` pages je `saveChangesError=true`volána pomocí .

### <a name="update-the-delete-razor-page"></a>Aktualizace stránky Odstranit břitvu

Přidejte následující zvýrazněnou chybovou zprávu na stránku Odstranit holicí strojek.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Test Delete.

## <a name="common-errors"></a>Běžné chyby

Studenti / Index nebo jiné odkazy nefungují:

Ověřte, že `@page` stránka Razor obsahuje správnou direktivu. Například Stránka Žiletka Studentů/Index by **neměla** obsahovat šablonu trasy:

```cshtml
@page "{id:int}"
```

Každá stránka Razor `@page` musí obsahovat direktivu.



## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/intro)
> [další](xref:data/ef-rp/sort-filter-page)

::: moniker-end
