---
title: Razor Pages s EF Core v ASP.NET Core-CRUD-2 z 8
author: rick-anderson
description: Ukazuje, jak vytvořit, číst, aktualizovat a odstranit pomocí EF Core.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/crud
ms.openlocfilehash: 05519852fab22bd3ad5b77e3494b49191448286f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665646"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a>Razor Pages s EF Core v ASP.NET Core-CRUD-2 z 8

Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se vyhodnotí a přizpůsobí se vygenerovaný kód CRUD (vytváření, čtení, aktualizace, odstranění).

## <a name="no-repository"></a>Žádné úložiště

Někteří vývojáři používají vrstvu služby nebo model úložiště k vytvoření abstrakce vrstvy mezi uživatelským rozhraním (Razor Pages) a vrstvou přístupu k datům. V tomto kurzu tento kurz neudělá. K minimalizaci složitosti a udržování kurzu zaměřeného na EF Core je EF Core kód přidán přímo do tříd modelu stránky. 

## <a name="update-the-details-page"></a>Aktualizace stránky s podrobnostmi

Generovaný kód pro stránky studentů neobsahuje registrační data. V této části přidáte registrace na stránku podrobností.

### <a name="read-enrollments"></a>Čtení zápisů

Chcete-li zobrazit data o registraci studenta na stránce, je nutné si ji přečíst. Generovaný kód na *stránkách/Students/details. cshtml. cs* čte pouze data studenta bez registračních údajů:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Metodu `OnGetAsync` nahraďte následujícím kódem a načtěte data zápisu pro vybraného studenta. Změny jsou zvýrazněné.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

Metody [include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) a [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) způsobují, že kontext načte vlastnost navigace `Student.Enrollments` a v rámci každého zápisu `Enrollment.Course` navigační vlastnost. Tyto metody jsou podrobně prověřeny v kurzu [čtení souvisejících dat](xref:data/ef-rp/read-related-data) .

Metoda [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) vylepšuje výkon ve scénářích, kde vracené entity nejsou aktualizovány v aktuálním kontextu. `AsNoTracking` jsou popsány dále v tomto kurzu.

### <a name="display-enrollments"></a>Zobrazit registrace

Nahraďte kód na *stránkách/Students/details. cshtml* pomocí následujícího kódu pro zobrazení seznamu zápisů. Změny jsou zvýrazněné.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Předchozí kód projde entitami v navigační vlastnosti `Enrollments`. U každého zápisu se zobrazí titul kurzu a stupeň. Název kurzu se načte z entity kurzu, která je uložená v navigační vlastnosti `Course` entity registrace.

Spusťte aplikaci, vyberte kartu **Students** a klikněte na odkaz **Podrobnosti** pro studenta. Zobrazí se seznam kurzů a stupňů pro vybraného studenta.

### <a name="ways-to-read-one-entity"></a>Způsoby, jak číst jednu entitu

Vygenerovaný kód používá [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) ke čtení jedné entity. Tato metoda vrátí hodnotu null, pokud není nic nalezeno. v opačném případě vrátí první nalezený řádek, který splňuje kritéria filtru dotazu. `FirstOrDefaultAsync` je všeobecně lepší volbou než v následujících alternativách:

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) – vyvolá výjimku, pokud existuje více než jedna entita, která vyhovuje filtru dotazu. Chcete-li zjistit, zda dotaz vrátil více než jeden řádek, `SingleOrDefaultAsync` se pokusí načíst více řádků. Tato doplňková práce není nutná, pokud dotaz může vracet pouze jednu entitu, stejně jako při vyhledávání jedinečného klíče.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) – najde entitu s primárním klíčem (PK). Pokud je entita s PK sledována kontextem, je vrácena bez požadavku na databázi. Tato metoda je optimalizovaná tak, aby vyhledávala jednu entitu, ale `Include` s `FindAsync`nemůžete volat.  Takže pokud jsou potřeba související data, `FirstOrDefaultAsync` je lepší volbou.

### <a name="route-data-vs-query-string"></a>Směrování dat a řetězce dotazu

Adresa URL stránky s podrobnostmi je `https://localhost:<port>/Students/Details?id=1`. Hodnota primárního klíče entity je v řetězci dotazu. Někteří vývojáři dávají přednost předávání hodnoty klíče v datech trasy: `https://localhost:<port>/Students/Details/1`. Další informace najdete v tématu [aktualizace generovaného kódu](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Aktualizace stránky pro vytvoření

Kód vygenerovaný `OnPostAsync` pro stránku vytvořit je zranitelný vůči [přestavení](#overposting). Metodu `OnPostAsync` na *stránkách/Students/Create. cshtml. cs* nahraďte následujícím kódem.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Předchozí kód vytvoří objekt studenta a pak použije odeslaná pole formuláře k aktualizaci vlastností objektu studenta. Metoda [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) :

* Používá hodnoty odeslaného formuláře z vlastnosti [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) v [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).
* Aktualizuje pouze uvedené vlastnosti (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).
* Vyhledá pole formuláře s předponou "student". například `Student.FirstMidName`. Nerozlišuje velká a malá písmena.
* Používá systém [vázání modelů](xref:mvc/models/model-binding) k převodu hodnot formuláře z řetězců na typy v modelu `Student`. Například `EnrollmentDate` musí být převedena na hodnotu DateTime.

Spusťte aplikaci a vytvořte entitu studenta k otestování stránky vytvořit.

## <a name="overposting"></a>Přestavování

Použití `TryUpdateModel` k aktualizaci polí pomocí publikovaných hodnot je osvědčeným postupem zabezpečení, protože brání přestavení. Předpokládejme například, že entita student zahrnuje vlastnost `Secret`, kterou by tato webová stránka neměla aktualizovat ani přidat:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

I v případě, že aplikace nemá na stránce Vytvořit nebo aktualizovat Razor pole `Secret`, může hacker nastavit hodnotu `Secret` přeúčtováním. Počítačový podvodník může k odeslání `Secret` hodnoty formuláře použít nějaký nástroj, jako je například Fiddler, nebo napsat nějaký JavaScript. Původní kód neomezuje pole, která používá pořadač modelů při vytváření instance studenta.

Bez ohledu na hodnotu se v databázi aktualizuje počítačový podvodník zadaný pro pole `Secret` formuláře. Následující obrázek ukazuje nástroj Fiddler, který přidá pole `Secret` (s hodnotou "Repost") do publikovaných hodnot formuláře.

![Fiddler přidání tajného pole](../ef-mvc/crud/_static/fiddler.png)

Hodnota "přeúčtování" se úspěšně přidala do vlastnosti `Secret` vloženého řádku. K tomu dochází, i když Návrhář aplikace nikdy neurčil vlastnost `Secret`, která se má nastavit pomocí stránky vytvořit.

### <a name="view-model"></a>Zobrazit model

Modely zobrazení poskytují alternativní způsob, jak zabránit přestavení.

Aplikační model se často označuje jako doménový model. Doménový model obvykle obsahuje všechny vlastnosti požadované odpovídající entitou v databázi. Model zobrazení obsahuje pouze vlastnosti, které jsou potřebné pro uživatelské rozhraní, které je použito pro (například stránku vytvořit).

Kromě modelu zobrazení některé aplikace používají model vazby nebo vstupní model k předávání dat mezi třídou Razor Pagesho modelu stránky a prohlížečem. 

Vezměte v úvahu následující model zobrazení `Student`:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

Následující kód používá model zobrazení `StudentVM` k vytvoření nového studenta:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Metoda [NastavitHodnotu](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) nastaví hodnoty tohoto objektu čtením hodnot z jiného objektu [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) . `SetValues` používá spárování názvů vlastností. Typ modelu zobrazení nemusí být v souvislosti s typem modelu, stačí mít vlastnosti, které odpovídají.

Použití `StudentVM` vyžaduje, aby se [vytvořila aplikace Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) , aby používala `StudentVM` místo `Student`.

## <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravu

Na *stránce Pages/Students/Edit. cshtml. cs*, nahraďte `OnGetAsync` a `OnPostAsync` metody následujícím kódem.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Změny kódu jsou podobné na stránce vytvořit s několika výjimkami:

* `FirstOrDefaultAsync` bylo nahrazeno [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). Pokud nemusíte zahrnovat související data, `FindAsync` je efektivnější.
* `OnPostAsync` má parametr `id`.
* Aktuální student se načte z databáze místo vytvoření prázdného studenta.

Spusťte aplikaci a otestujte ji vytvořením a úpravou studenta.

## <a name="entity-states"></a>Stavy entit

Kontext databáze uchovává přehled o tom, zda jsou entity v paměti synchronizovány s odpovídajícími řádky v databázi. Tyto informace o sledování určují, co se stane, když se zavolá [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) . Například při předání nové entity metodě [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) se stav této entity nastaví na [přidáno](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Při volání `SaveChangesAsync`, kontext databáze vydá příkaz SQL INSERT.

Entita může být v jednom z [následujících stavů](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: entita v databázi ještě neexistuje. Metoda `SaveChanges` vydá příkaz INSERT.

* `Unchanged`: s touto entitou není nutné ukládat žádné změny. Entita má tento stav při čtení z databáze.

* `Modified`: změnily se některé nebo všechny hodnoty vlastnosti entity. Metoda `SaveChanges` vydá příkaz UPDATE.

* `Deleted`: entita byla označena k odstranění. Metoda `SaveChanges` vydá příkaz DELETE.

* `Detached`: entita není sledována kontextem databáze.

V desktopové aplikaci se změny stavu obvykle nastaví automaticky. Entita je čtena, změny jsou provedeny a stav entity se automaticky změní na `Modified`. Volání `SaveChanges` generuje příkaz SQL UPDATE, který aktualizuje pouze změněné vlastnosti.

V rámci webové aplikace `DbContext`, který čte entitu, a zobrazuje data vyřazení po vykreslení stránky. Když je volána metoda `OnPostAsync` stránky, je vytvořena nová webová žádost a s novou instancí `DbContext`. Přečtení entity v tomto novém kontextu simuluje zpracování plochy.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

V této části implementujete vlastní chybovou zprávu, když se volání `SaveChanges` nezdařilo.

Nahraďte kód na *stránkách/Students/DELETE. cshtml. cs* následujícím kódem. Změny jsou zvýrazněny (jiné než vyčištění příkazů `using`).

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

Předchozí kód přidá volitelný parametr `saveChangesError` do signatury metody `OnGetAsync`. `saveChangesError` určuje, zda byla metoda volána po neúspěšném odstranění objektu student. Operace odstranění může selhat kvůli přechodným problémům se sítí. Přechodné chyby sítě jsou pravděpodobnější, když je databáze v cloudu. Parametr `saveChangesError` má hodnotu false při volání `OnGetAsync` odstranění stránky z uživatelského rozhraní. Pokud je zavolána `OnGetAsync` `OnPostAsync` (protože operace odstranění se nezdařila), parametr `saveChangesError` má hodnotu true.

Metoda `OnPostAsync` načte vybranou entitu a pak zavolá metodu [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) pro nastavení stavu entity na hodnotu `Deleted`. Při volání `SaveChanges` se vygeneruje příkaz SQL DELETE. Pokud `Remove` selžou:

* Je zachycena výjimka databáze.
* Metoda Delete Pages `OnGetAsync` je volána s `saveChangesError=true`.

Přidat chybovou zprávu na stránku odstranění Razor (*Pages/Students/DELETE. cshtml*):

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Spusťte aplikaci a odstraňte studenta a otestujte stránku odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/intro)
> [Další kurz](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se vyhodnotí a přizpůsobí se vygenerovaný kód CRUD (vytváření, čtení, aktualizace, odstranění).

Pro minimalizaci složitosti a udržování těchto kurzů zaměřených na EF Core se v modelech stránek používá EF Core kód. Někteří vývojáři používají vrstvu služby nebo model úložiště v nástroji k vytvoření abstrakce vrstvy mezi uživatelským rozhraním (Razor Pages) a vrstvou přístupu k datům.

V tomto kurzu se zkontrolují Razor Pages vytvoření, úpravy, odstranění a podrobnosti ve složce *Students* .

Generovaný kód používá následující vzor pro stránky vytvořit, upravit a odstranit:

* Získejte a zobrazte požadovaná data pomocí metody HTTP GET `OnGetAsync`.
* Uložte změny dat pomocí `OnPostAsync`metody HTTP POST.

Stránky index a podrobnosti získávají a zobrazují požadovaná data pomocí metody HTTP GET `OnGetAsync`

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync vs. FirstOrDefaultAsync

Vygenerovaný kód používá [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), což je obecně upřednostňováno nad [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

 `FirstOrDefaultAsync` je efektivnější než `SingleOrDefaultAsync` při načítání jedné entity:

* Pokud kód nepotřebuje ověřit, že dotaz nemá více než jednu entitu.
* `SingleOrDefaultAsync` načte více dat a nepotřebnou práci.
* `SingleOrDefaultAsync` vyvolá výjimku, pokud existuje více než jedna entita, která odpovídá části filtru.
* `FirstOrDefaultAsync` nevyvolá, pokud existuje více než jedna entita, která odpovídá součásti filtru.

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

V podstatě z vygenerovaného kódu lze [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) použít místo `FirstOrDefaultAsync`.

`FindAsync`:

* Najde entitu s primárním klíčem (PK). Pokud je entita s PK sledována kontextem, je vrácena bez požadavku na databázi.
* Je jednoduchý a stručný.
* Je optimalizováno pro vyhledání jedné entity.
* Může mít výhody výkonu v některých situacích, ale zřídka se pro běžné webové aplikace děje.
* Implicitně používá [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) namísto [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).

Pokud však chcete `Include` jiné entity, `FindAsync` již není vhodné. To znamená, že možná budete muset opustit `FindAsync` a přejít na dotaz, jak vaše aplikace pokračuje.

## <a name="customize-the-details-page"></a>Přizpůsobení stránky s podrobnostmi

Přejděte na stránku `Pages/Students`. Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru *Pages/Students/index. cshtml* .

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Spusťte aplikaci a vyberte odkaz **Podrobnosti** . Adresa URL má `http://localhost:5000/Students/Details?id=2`ve formátu. ID studenta se předává pomocí řetězce dotazu (`?id=2`).

Aktualizujte Razor Pages upravit, podrobnosti a odstranit, aby se použila šablona `"{id:int}"` trasy. Změňte direktivu Page pro každou z těchto stránek z `@page` na `@page "{id:int}"`.

Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje hodnotu** trasy typu Integer, vrací chybu HTTP 404 (Nenalezeno). `http://localhost:5000/Students/Details` například vrátí chybu 404. Pokud chcete ID nastavit jako volitelné, přidejte `?` k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Spusťte aplikaci, klikněte na odkaz Podrobnosti a ověřte, že adresa URL odesílá ID jako data trasy (`http://localhost:5000/Students/Details/2`).

Neměňte globálně `@page` `@page "{id:int}"`, takže dojde k přerušení odkazů na domovskou stránku a vytváření stránek.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>Přidat související data

Generovaný kód stránky indexu studentů neobsahuje vlastnost `Enrollments`. V této části se obsah kolekce `Enrollments` zobrazuje na stránce s podrobnostmi.

Metoda `OnGetAsync` *Pages/students. cshtml. cs* používá metodu `FirstOrDefaultAsync` k načtení jedné entity `Student`. Přidejte následující zvýrazněný kód:

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

Metody [include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) a [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) způsobují, že kontext načte vlastnost navigace `Student.Enrollments` a v rámci každého zápisu `Enrollment.Course` navigační vlastnost. Tyto metody jsou podrobně prověřeny v kurzu pro data související s čtením.

Metoda [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) zvyšuje výkon ve scénářích, kdy se vracené entity v aktuálním kontextu neaktualizují. `AsNoTracking` jsou popsány dále v tomto kurzu.

### <a name="display-related-enrollments-on-the-details-page"></a>Zobrazit související registrace na stránce s podrobnostmi

Otevřete *stránky/studenty/podrobnosti. cshtml*. Přidejte následující zvýrazněný kód pro zobrazení seznamu zápisů:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Pokud je odsazení kódu po vložení kódu nesprávné, stiskněte CTRL + K-D a opravte ho.

Předchozí kód projde entitami v navigační vlastnosti `Enrollments`. U každého zápisu se zobrazí titul kurzu a stupeň. Název kurzu se načte z entity kurzu, která je uložená v navigační vlastnosti `Course` entity registrace.

Spusťte aplikaci, vyberte kartu **Students** a klikněte na odkaz **Podrobnosti** pro studenta. Zobrazí se seznam kurzů a stupňů pro vybraného studenta.

## <a name="update-the-create-page"></a>Aktualizace stránky pro vytvoření

Aktualizujte metodu `OnPostAsync` na *stránkách/studentů/vytvořte. cshtml. cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Projděte si kód [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) :

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

V předchozím kódu se `TryUpdateModelAsync<Student>` pokusí aktualizovat objekt `emptyStudent` pomocí publikovaných hodnot formuláře z vlastnosti [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) v [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel). `TryUpdateModelAsync` aktualizuje jenom uvedené vlastnosti (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).

V předchozí ukázce:

* Druhý argument (`"student", // Prefix`) je předpona používá k vyhledání hodnot. Nerozlišuje velká a malá písmena.
* Hodnoty odeslaného formuláře jsou převedeny na typy v `Student` modelu pomocí [vazby modelu](xref:mvc/models/model-binding).

<a id="overpost"></a>

### <a name="overposting"></a>Přestavování

Použití `TryUpdateModel` k aktualizaci polí pomocí publikovaných hodnot je osvědčeným postupem zabezpečení, protože brání přestavení. Předpokládejme například, že entita student zahrnuje vlastnost `Secret`, kterou by tato webová stránka neměla aktualizovat ani přidat:

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

I v případě, že aplikace nemá na stránce vytvořit/aktualizovat Razor `Secret` pole, může hacker nastavit hodnotu `Secret` přeúčtováním. Počítačový podvodník může k odeslání `Secret` hodnoty formuláře použít nějaký nástroj, jako je například Fiddler, nebo napsat nějaký JavaScript. Původní kód neomezuje pole, která používá pořadač modelů při vytváření instance studenta.

Bez ohledu na hodnotu se v databázi aktualizuje počítačový podvodník zadaný pro pole `Secret` formuláře. Následující obrázek ukazuje nástroj Fiddler, který přidá pole `Secret` (s hodnotou "Repost") do publikovaných hodnot formuláře.

![Fiddler přidání tajného pole](../ef-mvc/crud/_static/fiddler.png)

Hodnota "přeúčtování" se úspěšně přidala do vlastnosti `Secret` vloženého řádku. Návrhář aplikace nikdy neurčil vlastnost `Secret`, která se má nastavit pomocí stránky vytvořit.

<a name="vm"></a>

### <a name="view-model"></a>Zobrazit model

Model zobrazení obvykle obsahuje podmnožinu vlastností obsažených v modelu používaném aplikací. Aplikační model se často označuje jako doménový model. Doménový model obvykle obsahuje všechny vlastnosti požadované odpovídající entitou v databázi. Model zobrazení obsahuje pouze vlastnosti, které jsou potřebné pro vrstvu uživatelského rozhraní (například stránka vytvořit). Kromě modelu zobrazení některé aplikace používají model vazby nebo vstupní model k předávání dat mezi třídou Razor Pagesho modelu stránky a prohlížečem. Vezměte v úvahu následující model zobrazení `Student`:

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Modely zobrazení poskytují alternativní způsob, jak zabránit přestavení. Model zobrazení obsahuje pouze vlastnosti, které lze zobrazit (Zobrazit) nebo aktualizovat.

Následující kód používá model zobrazení `StudentVM` k vytvoření nového studenta:

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

Metoda [NastavitHodnotu](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) nastaví hodnoty tohoto objektu čtením hodnot z jiného objektu [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) . `SetValues` používá spárování názvů vlastností. Typ modelu zobrazení nemusí být v souvislosti s typem modelu, stačí mít vlastnosti, které odpovídají.

Použití `StudentVM` vyžaduje, aby bylo [CreateVM. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) Aktualizováno pro použití `StudentVM` namísto `Student`.

V Razor Pages je `PageModel` odvozenou třídou model zobrazení.

## <a name="update-the-edit-page"></a>Aktualizace stránky pro úpravu

Aktualizujte model stránky pro stránku pro úpravy. Hlavní změny jsou zvýrazněné:

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Změny kódu jsou podobné na stránce vytvořit s několika výjimkami:

* `OnPostAsync` má volitelný parametr `id`.
* Aktuální student se načte z databáze místo vytvoření prázdného studenta.
* `FirstOrDefaultAsync` bylo nahrazeno [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync). `FindAsync` je vhodná volba pro výběr entity z primárního klíče. Další informace najdete v tématu [FindAsync](#FindAsync) .

### <a name="test-the-edit-and-create-pages"></a>Testování stránek pro úpravy a vytváření

Vytvořte a upravte několik entit studenta.

## <a name="entity-states"></a>Stavy entit

Kontext databáze uchovává přehled o tom, zda jsou entity v paměti synchronizovány s odpovídajícími řádky v databázi. Informace o synchronizaci kontextu databáze určují, co se stane, když se zavolá [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) . Například při předání nové entity metodě [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) se stav této entity nastaví na [přidáno](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added). Při volání `SaveChangesAsync` vyvolá kontext databáze příkaz SQL INSERT.

Entita může být v jednom z [následujících stavů](/dotnet/api/microsoft.entityframeworkcore.entitystate):

* `Added`: entita ještě v databázi neexistuje. Metoda `SaveChanges` vydá příkaz INSERT.

* `Unchanged`: s touto entitou není nutné ukládat žádné změny. Entita má tento stav při čtení z databáze.

* `Modified`: změnily se některé nebo všechny hodnoty vlastnosti entity. Metoda `SaveChanges` vydá příkaz UPDATE.

* `Deleted`: entita byla označena k odstranění. Metoda `SaveChanges` vydá příkaz DELETE.

* `Detached`: entita není sledována kontextem databáze.

V desktopové aplikaci se změny stavu obvykle nastaví automaticky. Entita je čtena, změny jsou provedeny a stav entity bude automaticky změněn na `Modified`. Volání `SaveChanges` generuje příkaz SQL UPDATE, který aktualizuje pouze změněné vlastnosti.

V rámci webové aplikace `DbContext`, který čte entitu, a zobrazuje data vyřazení po vykreslení stránky. Když je volána metoda `OnPostAsync` stránky, je vytvořena nová webová žádost a s novou instancí `DbContext`. Opětovné čtení entity v tomto novém kontextu simuluje zpracování plochy.

## <a name="update-the-delete-page"></a>Aktualizovat stránku Delete

V této části je kód přidán k implementaci vlastní chybové zprávy, když se volání `SaveChanges` nezdařilo. Přidejte řetězec, který bude obsahovat možné chybové zprávy:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

Nahraďte metodu `OnGetAsync` následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Předchozí kód obsahuje nepovinný parametr `saveChangesError`. `saveChangesError` určuje, zda byla metoda volána po neúspěšném odstranění objektu student. Operace odstranění může selhat kvůli přechodným problémům se sítí. Přechodné chyby sítě jsou pravděpodobnější v cloudu. `saveChangesError`je false, pokud je `OnGetAsync` odstranění stránky voláno z uživatelského rozhraní. Pokud je zavolána `OnGetAsync` `OnPostAsync` (protože operace odstranění se nezdařila), parametr `saveChangesError` má hodnotu true.

### <a name="the-delete-pages-onpostasync-method"></a>Metoda Delete Pages OnPostAsync

Nahraďte `OnPostAsync` následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Předchozí kód načte vybranou entitu a pak zavolá metodu [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) pro nastavení stavu entity na `Deleted`. Při volání `SaveChanges` se vygeneruje příkaz SQL DELETE. Pokud `Remove` selžou:

* Výjimka databáze je zachycena.
* Metoda Delete Pages `OnGetAsync` je volána s `saveChangesError=true`.

### <a name="update-the-delete-razor-page"></a>Aktualizace stránky odstranit Razor

Na stránku odstranit Razor přidejte následující zvýrazněnou chybovou zprávu.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Odstraňte test.

## <a name="common-errors"></a>Běžné chyby

Studenti/index nebo jiné odkazy nefungují:

Ověřte, zda stránka Razor obsahuje správnou direktivu `@page`. Například stránka student/index **Razor by neměla** obsahovat šablonu směrování:

```cshtml
@page "{id:int}"
```

Každá stránka Razor musí obsahovat direktivu `@page`.



## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/intro)
> [Další](xref:data/ef-rp/sort-filter-page)

::: moniker-end
