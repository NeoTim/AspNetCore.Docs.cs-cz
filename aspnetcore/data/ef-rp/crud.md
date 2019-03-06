---
title: Stránky Razor s EF Core v ASP.NET Core - CRUD - 2, 8
author: rick-anderson
description: Ukazuje, jak vytvářet, číst, aktualizovat, odstranit pomocí EF Core
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/crud
ms.openlocfilehash: adb281277599456356251c6ee30772a25f14ac58
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346047"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a>Stránky Razor s EF Core v ASP.NET Core - CRUD - 2, 8

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

Podle [Petr Dykstra](https://github.com/tdykstra), [Jan Macek P](https://twitter.com/thereformedprog), a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

V tomto kurzu, vygenerované CRUD (vytváření, čtení, aktualizace nebo odstranění) se zkontroluje a vlastní kód.

Chcete-li minimalizovat složitost a ponechat tyto kurzy, zaměřuje na EF Core, EF Core kód slouží v modelech stránky. Někteří vývojáři použít model služby vrstvy nebo úložiště v vytvořit abstraktní vrstvu mezi uživatelského rozhraní (stránky Razor) a vrstva přístupu k datům.

V tomto kurzu, vytvořit, upravit, odstranit a podrobnosti stránky Razor *studenty* jsou zkoumány složky.

Automaticky generovaný kód používá následující vzor pro stránky Create, Edit a Delete:

* Získání a zobrazení požadovaných dat s metodou HTTP GET `OnGetAsync`.
* Uložit změny dat s metodou HTTP POST `OnPostAsync`.

Stránky Index a podrobnosti o získání a zobrazení požadovaných dat s metodou HTTP GET `OnGetAsync`

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync vs. FirstOrDefaultAsync

Generovaný kód používá [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), což je obecně upřednostňované nad [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

 `FirstOrDefaultAsync` je mnohem efektivnější než `SingleOrDefaultAsync` na načíst jednu entitu:

* Pokud kód potřebuje ověřit, jestli není více než jednu entitu vrácená z dotazu.
* `SingleOrDefaultAsync` načte víc dat a zbytečné funguje.
* `SingleOrDefaultAsync` vyvolá výjimku, pokud existuje více než jednu entitu, která odpovídá část filtru.
* `FirstOrDefaultAsync` nevyvolá, pokud existuje více než jednu entitu, která odpovídá část filtru.

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

Prakticky automaticky generovaný kód [asynchronně vyhledá](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) lze použít místo `FirstOrDefaultAsync`.

`FindAsync`:

* Vyhledá entitu s primární klíč (PK). Pokud entita s primárnímu Klíči sledován správou kontextu, je vrácen bez požadavek do databáze.
* Je snadné a stručné.
* Je optimalizovaný pro vyhledání jednu entitu.
* V některých situacích může mít výhody výkonu, ale zřídka se pro běžné webové aplikace.
* Implicitně používá [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) místo [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

Ale pokud budete chtít `Include` jinými entitami, pak `FindAsync` už není vhodné. To znamená, že budete muset opustit `FindAsync` a přesunout do dotazu v průběhu vaší aplikace.

## <a name="customize-the-details-page"></a>Přizpůsobení stránky podrobností

Přejděte do `Pages/Students` stránky. **Upravit**, **podrobnosti**, a **odstranit** vygeneroval odkazy [ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v *stránek/studenty / Index.cshtml* souboru.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Spusťte aplikaci a vyberte **podrobnosti** odkaz. Adresa URL je ve formátu `http://localhost:5000/Students/Details?id=2`. ID studenta je předán pomocí řetězce dotazu (`?id=2`).

Aktualizovat upravit, podrobnosti a odstranit stránky Razor k použití `"{id:int}"` šablonu trasy. Změnit direktivě stránky pro každou z těchto stránek z `@page` k `@page "{id:int}"`.

Požadavek na stránku se šablona trasy "{id: int}", která provádí **není** patří integer trasy hodnotu vrátí protokolu HTTP (Nenalezeno) chybu 404. Například `http://localhost:5000/Students/Details` vrátí chybu 404. Chcete-li nastavit ID volitelný, přidejte `?` pro dané omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Spuštění aplikace, klikněte na odkaz podrobnosti a ověřte adresu URL jako data trasy, která předává ID (`http://localhost:5000/Students/Details/2`).

Neměnit globálně `@page` k `@page "{id:int}"`, to tedy konce odkazy na domovské stránce a vytvářet stránky.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Přidání souvisejících dat

Nezahrnuje automaticky generovaný kód pro studenty indexovou stránku `Enrollments` vlastnost. V této části, obsah `Enrollments` kolekce se zobrazí na stránce podrobností.

`OnGetAsync` Metoda *Pages/Students/Details.cshtml.cs* používá `FirstOrDefaultAsync` metodu pro načtení jedné `Student` entity. Přidejte následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

[Zahrnout](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) a [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) metody způsobit kontextu načtení `Student.Enrollments` navigační vlastnost a v rámci každé registraci `Enrollment.Course` navigační vlastnost. Tyto metody jsou zkoumány podle podrobností v kurzu čtení souvisejících dat.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) metoda zvyšuje výkon ve scénářích po vrácení entity, které se neaktualizují v rámci aktuálního kontextu. `AsNoTracking` je popsána dále v tomto kurzu.

### <a name="display-related-enrollments-on-the-details-page"></a>Zobrazit související registrace na stránce s podrobnostmi

Otevřít *Pages/Students/Details.cshtml*. Přidejte následující zvýrazněný kód k zobrazení seznamu registrací:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Pokud po vložení kódu je nesprávné odsazení kódu, stiskněte kombinaci kláves CTRL-K-D na opravu.

Předchozí kód projde entity v `Enrollments` navigační vlastnost. Pro každé registraci zobrazí název kurzu a třída. Název kurzu je načten z kurzu entitu, která je uložena v `Course` navigační vlastnost entity registrace.

Spusťte aplikaci, vyberte **studenty** kartu a klikněte na tlačítko **podrobnosti** odkaz student. Zobrazí se seznam kurzů a známek studentů vybrané.

## <a name="update-the-create-page"></a>Aktualizovat stránku vytvořit

Aktualizace `OnPostAsync` metoda *Pages/Students/Create.cshtml.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Zkontrolujte [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) kódu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

V předchozím kódu `TryUpdateModelAsync<Student>` pokusí aktualizovat `emptyStudent` objektu z odeslaného formuláře hodnoty [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) vlastnost [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel). `TryUpdateModelAsync` Aktualizuje vlastnosti uvedené pouze (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).

V předchozím příkladu:

* Druhý argument (`"student", // Prefix`) je předpona, která se používá pro vyhledávání hodnoty. Není malá a velká písmena.
* Hodnoty odeslaného formuláře se převedou na typy v `Student` model pomocí [vazby modelu](xref:mvc/models/model-binding#how-model-binding-works).

<a id="overpost"></a>

### <a name="overposting"></a>Overposting

Pomocí `TryUpdateModel` aktualizovat pole odeslaných hodnot je z bezpečnostních důvodů, protože ta brání overposting. Předpokládejme například, že obsahuje entity studentů `Secret` vlastnost, která by neměla aktualizovat nebo přidat tato webová stránka:

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

I v případě, že aplikace nemá `Secret` nastavit pole při vytvoření/aktualizaci stránky Razor, přístup `Secret` hodnoty overposting. Přístup může použít například nástroj Fiddler nebo napsat určitého kódu JavaScript, k publikování `Secret` tvoří hodnotu. Původní kód neomezuje pole, která vazače modelu používá při vytváření instance studentů.

Všechno, co hodnota kyberzločinci zadaný pro `Secret` pole formuláře je aktualizována v databázi. Následující obrázek ukazuje přidání nástroj Fiddler `Secret` pole (hodnotu "OverPost") na hodnoty odeslaného formuláře.

![Přidání tajného kódu pole fiddleru](../ef-mvc/crud/_static/fiddler.png)

Hodnota "OverPost" se úspěšně přidal do `Secret` vlastností vloženého řádku. Návrhář aplikace nikdy určené `Secret` vlastnost nastavit na stránce vytvořit.

<a name="vm"></a>

### <a name="view-model"></a>Model zobrazení

Model zobrazení obvykle obsahuje podmnožinu vlastností obsažených v modelu v aplikaci použít. Aplikační model se často nazývá model domény. Model domény obvykle obsahuje všechny vlastnosti vyžaduje odpovídající entita v databázi. Model zobrazení obsahuje pouze vlastnosti, které jsou potřebné pro vrstvě uživatelského rozhraní (například vytvořit stránku). Kromě zobrazení modelu některé aplikace pomocí vazby modelu nebo vstupním modelu k předávání dat mezi třídy modelu stránky Razor Pages a prohlížečem. Vezměte v úvahu následující `Student` model zobrazení:

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Zobrazit modely poskytují alternativní způsob zabránění overposting. Model zobrazení obsahuje pouze vlastnosti k zobrazení (zobrazení) nebo aktualizovat.

Následující kód používá `StudentVM` model zobrazení k vytvoření nového objektu student:

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) metoda nastaví hodnoty tohoto objektu přečtením hodnoty z jiného [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) objektu. `SetValues` používá se shoda názvu vlastnosti. Typ modelu zobrazení nemusí být související s typem modelu, stejně musí mít vlastnosti, které odpovídají.

Pomocí `StudentVM` vyžaduje [CreateVM.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) aktualizují, aby využívaly `StudentVM` spíše než `Student`.

V Razor Pages `PageModel` odvozené třídy je model zobrazení.

## <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravu

Aktualizace modelu stránce pro stránky pro úpravu. Hlavní změny jsou zvýrazněny:

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Změny kódu jsou podobná stránka vytvořit s několika výjimkami:

* `OnPostAsync` má volitelnou `id` parametru.
* Aktuální studenta je načtena z databáze, místo vytvoření prázdné studentů.
* `FirstOrDefaultAsync` bylo nahrazeno tématem [asynchronně vyhledá](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). `FindAsync` Při výběru z primární klíč entity, je dobrou volbou. Zobrazit [asynchronně vyhledá](#FindAsync) Další informace.

### <a name="test-the-edit-and-create-pages"></a>Testování úpravy a vytváření stránek

Vytvořte a upravte několik entit studentů.

## <a name="entity-states"></a>Stavy entity

Kontext databáze uchovává informace o, jestli jsou synchronizované s jejich odpovídajících řádků v databázi entity v paměti. Informace o kontextu synchronizace DB Určuje, co se stane, když [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána. Například při vytvoření nové entity je předán [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) metoda, která stav entity je nastavený na [přidané](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Když `SaveChangesAsync` nazývá databáze kontextu vydá příkaz INSERT jazyka SQL.

Entity mohou být v jednom z [následující stavy](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: Entita ještě neexistuje v databázi. `SaveChanges` Metoda vydá příkaz INSERT.

* `Unchanged`: Je nutné uložit s touto entitou žádné změny. Entita má tento stav, když je pro čtení z databáze.

* `Modified`: Některé nebo všechny hodnoty vlastností entity byly změněny. `SaveChanges` Metoda vydá příkazu UPDATE.

* `Deleted`: Entita byla označena k odstranění. `SaveChanges` Metoda vydá příkaz DELETE.

* `Detached`: Entita není sledován správou kontext databáze.

V desktopové aplikace změny stavu se obvykle nastaví automaticky. Entita je určen pro čtení, dojde ke změně a stav entity automaticky změnil na `Modified`. Volání `SaveChanges` vytvoří prohlášení aktualizace SQL, která aktualizuje pouze změněné vlastnosti.

Ve webové aplikaci `DbContext` , která načte entity a zobrazí data je uvolněn po vykreslení stránky. Pokud na stránce `OnPostAsync` metoda je volána, provedení požadavku na nový web a s novou instancí `DbContext`. Znovu čtení entity v kontextu této nové simuluje klasické pracovní plochy zpracování.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

V této části kódu se přidá k implementaci vlastních chybových zpráv při volání `SaveChanges` selže. Přidejte řetězec tak, aby obsahovala možné chybové zprávy:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Nahradit `OnGetAsync` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Předchozí kód obsahuje volitelný parametr `saveChangesError`. `saveChangesError` Označuje, zda byla volána metoda po selhání, aby student objekt odstranit. Operace odstranění může selhat z důvodu přechodné problémy se sítí. Přechodných síťových chyb jsou pravděpodobně v cloudu. `saveChangesError`má hodnotu false, pokud na stránce Odstranit `OnGetAsync` je volána z uživatelského rozhraní. Když `OnGetAsync` je volán `OnPostAsync` (protože operace odstranění selhala), `saveChangesError` parametr má hodnotu true.

### <a name="the-delete-pages-onpostasync-method"></a>Metoda OnPostAsync stránky Delete

Nahradit `OnPostAsync` následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Předchozí kód načte vybranou entitu, pak zavolá [odebrat](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) metoda nastavit stav entity `Deleted`. Když `SaveChanges` nazývá SQL odstranit vygenerované příkaz. Pokud `Remove` selže:

* Zachycení výjimky databáze.
* Odstranění stránky `OnGetAsync` metoda je volána s `saveChangesError=true`.

### <a name="update-the-delete-razor-page"></a>Aktualizace stránky Razor Delete

Přidejte následující zvýrazněný chybová zpráva na stránku odstranit Razor.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Testování, odstranění.

## <a name="common-errors"></a>Běžné chyby

Studenti na Index nebo jiné odkazy nefungují:

Ověřte stránky Razor obsahuje správný `@page` směrnice. Například by studenty/indexovou stránku Razor **není** obsahovat šablonu trasy:

```cshtml
@page "{id:int}"
```

Musí zahrnovat každou stránku Razor `@page` směrnice.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Verzi tohoto kurzu na webu YouTube](https://www.youtube.com/watch?v=F0SP7Ry4flQ&list=PLnVvOWNfuhGV45HnNgd0vuIIkoQ1UaVBf)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/intro)
> [další](xref:data/ef-rp/sort-filter-page)
