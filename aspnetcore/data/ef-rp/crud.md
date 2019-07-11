---
title: Stránky Razor s EF Core v ASP.NET Core - CRUD - 2, 8
author: rick-anderson
description: Ukazuje, jak vytvářet, číst, aktualizovat, odstranit pomocí EF Core
ms.author: riande
ms.date: 06/31/2017
uid: data/ef-rp/crud
ms.openlocfilehash: 9dd9ed7c39e3cf0e6e04c90e00d8b22d99988e9b
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813694"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a><span data-ttu-id="c5ae9-103">Stránky Razor s EF Core v ASP.NET Core - CRUD - 2, 8</span><span class="sxs-lookup"><span data-stu-id="c5ae9-103">Razor Pages with EF Core in ASP.NET Core - CRUD - 2 of 8</span></span>

<span data-ttu-id="c5ae9-104">Podle [Petr Dykstra](https://github.com/tdykstra), [Jan Macek P](https://twitter.com/thereformedprog), a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c5ae9-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="c5ae9-105">V tomto kurzu, vygenerované CRUD (vytváření, čtení, aktualizace nebo odstranění) se zkontroluje a vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="c5ae9-106">Chcete-li minimalizovat složitost a ponechat tyto kurzy, zaměřuje na EF Core, EF Core kód slouží v modelech stránky.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-106">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="c5ae9-107">Někteří vývojáři použít model služby vrstvy nebo úložiště v vytvořit abstraktní vrstvu mezi uživatelského rozhraní (stránky Razor) a vrstva přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-107">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="c5ae9-108">V tomto kurzu, vytvořit, upravit, odstranit a podrobnosti stránky Razor *studenty* jsou zkoumány složky.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-108">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="c5ae9-109">Automaticky generovaný kód používá následující vzor pro stránky Create, Edit a Delete:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-109">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="c5ae9-110">Získání a zobrazení požadovaných dat s metodou HTTP GET `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-110">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="c5ae9-111">Uložit změny dat s metodou HTTP POST `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-111">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="c5ae9-112">Stránky Index a podrobnosti o získání a zobrazení požadovaných dat s metodou HTTP GET `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="c5ae9-112">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="c5ae9-113">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span><span class="sxs-lookup"><span data-stu-id="c5ae9-113">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="c5ae9-114">Generovaný kód používá [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), což je obecně upřednostňované nad [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-114">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="c5ae9-115">`FirstOrDefaultAsync` je mnohem efektivnější než `SingleOrDefaultAsync` na načíst jednu entitu:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-115">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="c5ae9-116">Pokud kód potřebuje ověřit, jestli není více než jednu entitu vrácená z dotazu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-116">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="c5ae9-117">`SingleOrDefaultAsync` načte víc dat a zbytečné funguje.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-117">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="c5ae9-118">`SingleOrDefaultAsync` vyvolá výjimku, pokud existuje více než jednu entitu, která odpovídá část filtru.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-118">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="c5ae9-119">`FirstOrDefaultAsync` nevyvolá, pokud existuje více než jednu entitu, která odpovídá část filtru.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-119">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="c5ae9-120">FindAsync</span><span class="sxs-lookup"><span data-stu-id="c5ae9-120">FindAsync</span></span>

<span data-ttu-id="c5ae9-121">Prakticky automaticky generovaný kód [asynchronně vyhledá](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) lze použít místo `FirstOrDefaultAsync`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-121">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="c5ae9-122">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-122">`FindAsync`:</span></span>

* <span data-ttu-id="c5ae9-123">Vyhledá entitu s primární klíč (PK).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-123">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="c5ae9-124">Pokud entita s primárnímu Klíči sledován správou kontextu, je vrácen bez požadavek do databáze.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-124">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="c5ae9-125">Je snadné a stručné.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-125">Is simple and concise.</span></span>
* <span data-ttu-id="c5ae9-126">Je optimalizovaný pro vyhledání jednu entitu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-126">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="c5ae9-127">V některých situacích může mít výhody výkonu, ale zřídka se pro běžné webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-127">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="c5ae9-128">Implicitně používá [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) místo [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-128">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="c5ae9-129">Ale pokud budete chtít `Include` jinými entitami, pak `FindAsync` už není vhodné.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-129">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="c5ae9-130">To znamená, že budete muset opustit `FindAsync` a přesunout do dotazu v průběhu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-130">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="c5ae9-131">Přizpůsobení stránky podrobností</span><span class="sxs-lookup"><span data-stu-id="c5ae9-131">Customize the Details page</span></span>

<span data-ttu-id="c5ae9-132">Přejděte do `Pages/Students` stránky.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-132">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="c5ae9-133">**Upravit**, **podrobnosti**, a **odstranit** vygeneroval odkazy [ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v *stránek/studenty / Index.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-133">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="c5ae9-134">Spusťte aplikaci a vyberte **podrobnosti** odkaz.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-134">Run the app and select a **Details** link.</span></span> <span data-ttu-id="c5ae9-135">Adresa URL je ve formátu `http://localhost:5000/Students/Details?id=2`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-135">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="c5ae9-136">ID studenta je předán pomocí řetězce dotazu (`?id=2`).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-136">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="c5ae9-137">Aktualizovat upravit, podrobnosti a odstranit stránky Razor k použití `"{id:int}"` šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-137">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="c5ae9-138">Změnit direktivě stránky pro každou z těchto stránek z `@page` k `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-138">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="c5ae9-139">Požadavek na stránku se šablona trasy "{id: int}", která provádí **není** patří integer trasy hodnotu vrátí protokolu HTTP (Nenalezeno) chybu 404.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-139">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="c5ae9-140">Například `http://localhost:5000/Students/Details` vrátí chybu 404.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-140">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="c5ae9-141">Chcete-li nastavit ID volitelný, přidejte `?` pro dané omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-141">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="c5ae9-142">Spuštění aplikace, klikněte na odkaz podrobnosti a ověřte adresu URL jako data trasy, která předává ID (`http://localhost:5000/Students/Details/2`).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-142">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="c5ae9-143">Neměnit globálně `@page` k `@page "{id:int}"`, to tedy konce odkazy na domovské stránce a vytvářet stránky.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-143">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="c5ae9-144">Přidání souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="c5ae9-144">Add related data</span></span>

<span data-ttu-id="c5ae9-145">Nezahrnuje automaticky generovaný kód pro studenty indexovou stránku `Enrollments` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-145">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="c5ae9-146">V této části, obsah `Enrollments` kolekce se zobrazí na stránce podrobností.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-146">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="c5ae9-147">`OnGetAsync` Metoda *Pages/Students/Details.cshtml.cs* používá `FirstOrDefaultAsync` metodu pro načtení jedné `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-147">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="c5ae9-148">Přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-148">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="c5ae9-149">[Zahrnout](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) a [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) metody způsobit kontextu načtení `Student.Enrollments` navigační vlastnost a v rámci každé registraci `Enrollment.Course` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-149">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="c5ae9-150">Tyto metody jsou zkoumány podle podrobností v kurzu čtení souvisejících dat.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-150">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="c5ae9-151">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) metoda zvyšuje výkon ve scénářích po vrácení entity, které se neaktualizují v rámci aktuálního kontextu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-151">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="c5ae9-152">`AsNoTracking` je popsána dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-152">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="c5ae9-153">Zobrazit související registrace na stránce s podrobnostmi</span><span class="sxs-lookup"><span data-stu-id="c5ae9-153">Display related enrollments on the Details page</span></span>

<span data-ttu-id="c5ae9-154">Otevřít *Pages/Students/Details.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-154">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="c5ae9-155">Přidejte následující zvýrazněný kód k zobrazení seznamu registrací:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-155">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="c5ae9-156">Pokud po vložení kódu je nesprávné odsazení kódu, stiskněte kombinaci kláves CTRL-K-D na opravu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-156">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="c5ae9-157">Předchozí kód projde entity v `Enrollments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-157">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="c5ae9-158">Pro každé registraci zobrazí název kurzu a třída.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-158">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="c5ae9-159">Název kurzu je načten z kurzu entitu, která je uložena v `Course` navigační vlastnost entity registrace.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-159">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="c5ae9-160">Spusťte aplikaci, vyberte **studenty** kartu a klikněte na tlačítko **podrobnosti** odkaz student.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-160">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="c5ae9-161">Zobrazí se seznam kurzů a známek studentů vybrané.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-161">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="c5ae9-162">Aktualizovat stránku vytvořit</span><span class="sxs-lookup"><span data-stu-id="c5ae9-162">Update the Create page</span></span>

<span data-ttu-id="c5ae9-163">Aktualizace `OnPostAsync` metoda *Pages/Students/Create.cshtml.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-163">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="c5ae9-164">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="c5ae9-164">TryUpdateModelAsync</span></span>

<span data-ttu-id="c5ae9-165">Zkontrolujte [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) kódu:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-165">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="c5ae9-166">V předchozím kódu `TryUpdateModelAsync<Student>` pokusí aktualizovat `emptyStudent` objektu z odeslaného formuláře hodnoty [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) vlastnost [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-166">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="c5ae9-167">`TryUpdateModelAsync` Aktualizuje vlastnosti uvedené pouze (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-167">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="c5ae9-168">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-168">In the preceding sample:</span></span>

* <span data-ttu-id="c5ae9-169">Druhý argument (`"student", // Prefix`) je předpona, která se používá pro vyhledávání hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-169">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="c5ae9-170">Není malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-170">It's not case sensitive.</span></span>
* <span data-ttu-id="c5ae9-171">Hodnoty odeslaného formuláře se převedou na typy v `Student` model pomocí [vazby modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-171">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="c5ae9-172">Overposting</span><span class="sxs-lookup"><span data-stu-id="c5ae9-172">Overposting</span></span>

<span data-ttu-id="c5ae9-173">Pomocí `TryUpdateModel` aktualizovat pole odeslaných hodnot je z bezpečnostních důvodů, protože ta brání overposting.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-173">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="c5ae9-174">Předpokládejme například, že obsahuje entity studentů `Secret` vlastnost, která by neměla aktualizovat nebo přidat tato webová stránka:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-174">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="c5ae9-175">I v případě, že aplikace nemá `Secret` nastavit pole při vytvoření/aktualizaci stránky Razor, přístup `Secret` hodnoty overposting.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-175">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="c5ae9-176">Přístup může použít například nástroj Fiddler nebo napsat určitého kódu JavaScript, k publikování `Secret` tvoří hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-176">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="c5ae9-177">Původní kód neomezuje pole, která vazače modelu používá při vytváření instance studentů.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-177">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="c5ae9-178">Všechno, co hodnota kyberzločinci zadaný pro `Secret` pole formuláře je aktualizována v databázi.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-178">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="c5ae9-179">Následující obrázek ukazuje přidání nástroj Fiddler `Secret` pole (hodnotu "OverPost") na hodnoty odeslaného formuláře.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-179">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Přidání tajného kódu pole fiddleru](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="c5ae9-181">Hodnota "OverPost" se úspěšně přidal do `Secret` vlastností vloženého řádku.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-181">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="c5ae9-182">Návrhář aplikace nikdy určené `Secret` vlastnost nastavit na stránce vytvořit.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-182">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="c5ae9-183">Model zobrazení</span><span class="sxs-lookup"><span data-stu-id="c5ae9-183">View model</span></span>

<span data-ttu-id="c5ae9-184">Model zobrazení obvykle obsahuje podmnožinu vlastností obsažených v modelu v aplikaci použít.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-184">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="c5ae9-185">Aplikační model se často nazývá model domény.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-185">The application model is often called the domain model.</span></span> <span data-ttu-id="c5ae9-186">Model domény obvykle obsahuje všechny vlastnosti vyžaduje odpovídající entita v databázi.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-186">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="c5ae9-187">Model zobrazení obsahuje pouze vlastnosti, které jsou potřebné pro vrstvě uživatelského rozhraní (například vytvořit stránku).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-187">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="c5ae9-188">Kromě zobrazení modelu některé aplikace pomocí vazby modelu nebo vstupním modelu k předávání dat mezi třídy modelu stránky Razor Pages a prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-188">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="c5ae9-189">Vezměte v úvahu následující `Student` model zobrazení:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-189">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="c5ae9-190">Zobrazit modely poskytují alternativní způsob zabránění overposting.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-190">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="c5ae9-191">Model zobrazení obsahuje pouze vlastnosti k zobrazení (zobrazení) nebo aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-191">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="c5ae9-192">Následující kód používá `StudentVM` model zobrazení k vytvoření nového objektu student:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-192">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="c5ae9-193">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) metoda nastaví hodnoty tohoto objektu přečtením hodnoty z jiného [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) objektu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-193">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="c5ae9-194">`SetValues` používá se shoda názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-194">`SetValues` uses property name matching.</span></span> <span data-ttu-id="c5ae9-195">Typ modelu zobrazení nemusí být související s typem modelu, stejně musí mít vlastnosti, které odpovídají.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-195">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="c5ae9-196">Pomocí `StudentVM` vyžaduje [CreateVM.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) aktualizují, aby využívaly `StudentVM` spíše než `Student`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-196">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="c5ae9-197">V Razor Pages `PageModel` odvozené třídy je model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-197">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="c5ae9-198">Aktualizace stránky pro úpravu</span><span class="sxs-lookup"><span data-stu-id="c5ae9-198">Update the Edit page</span></span>

<span data-ttu-id="c5ae9-199">Aktualizace modelu stránce pro stránky pro úpravu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-199">Update the page model for the Edit page.</span></span> <span data-ttu-id="c5ae9-200">Hlavní změny jsou zvýrazněny:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-200">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="c5ae9-201">Změny kódu jsou podobná stránka vytvořit s několika výjimkami:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-201">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="c5ae9-202">`OnPostAsync` má volitelnou `id` parametru.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-202">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="c5ae9-203">Aktuální studenta je načtena z databáze, místo vytvoření prázdné studentů.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-203">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="c5ae9-204">`FirstOrDefaultAsync` bylo nahrazeno tématem [asynchronně vyhledá](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-204">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="c5ae9-205">`FindAsync` Při výběru z primární klíč entity, je dobrou volbou.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-205">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="c5ae9-206">Zobrazit [asynchronně vyhledá](#FindAsync) Další informace.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-206">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="c5ae9-207">Testování úpravy a vytváření stránek</span><span class="sxs-lookup"><span data-stu-id="c5ae9-207">Test the Edit and Create pages</span></span>

<span data-ttu-id="c5ae9-208">Vytvořte a upravte několik entit studentů.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-208">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="c5ae9-209">Stavy entity</span><span class="sxs-lookup"><span data-stu-id="c5ae9-209">Entity States</span></span>

<span data-ttu-id="c5ae9-210">Kontext databáze uchovává informace o, jestli jsou synchronizované s jejich odpovídajících řádků v databázi entity v paměti.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-210">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="c5ae9-211">Informace o kontextu synchronizace DB Určuje, co se stane, když [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) je volána.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-211">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="c5ae9-212">Například při vytvoření nové entity je předán [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) metoda, která stav entity je nastavený na [přidané](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span><span class="sxs-lookup"><span data-stu-id="c5ae9-212">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="c5ae9-213">Když `SaveChangesAsync` nazývá databáze kontextu vydá příkaz INSERT jazyka SQL.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-213">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="c5ae9-214">Entity mohou být v jednom z [následující stavy](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span><span class="sxs-lookup"><span data-stu-id="c5ae9-214">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="c5ae9-215">`Added`: Entita ještě neexistuje v databázi.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-215">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="c5ae9-216">`SaveChanges` Metoda vydá příkaz INSERT.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-216">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="c5ae9-217">`Unchanged`: Je nutné uložit s touto entitou žádné změny.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-217">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="c5ae9-218">Entita má tento stav, když je pro čtení z databáze.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-218">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="c5ae9-219">`Modified`: Některé nebo všechny hodnoty vlastností entity byly změněny.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-219">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="c5ae9-220">`SaveChanges` Metoda vydá příkazu UPDATE.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-220">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="c5ae9-221">`Deleted`: Entita byla označena k odstranění.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-221">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="c5ae9-222">`SaveChanges` Metoda vydá příkaz DELETE.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-222">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="c5ae9-223">`Detached`: Entita není sledován správou kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-223">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="c5ae9-224">V desktopové aplikace změny stavu se obvykle nastaví automaticky.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-224">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="c5ae9-225">Entita je určen pro čtení, dojde ke změně a stav entity automaticky změnil na `Modified`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-225">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="c5ae9-226">Volání `SaveChanges` vytvoří prohlášení aktualizace SQL, která aktualizuje pouze změněné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-226">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="c5ae9-227">Ve webové aplikaci `DbContext` , která načte entity a zobrazí data je uvolněn po vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-227">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="c5ae9-228">Pokud na stránce `OnPostAsync` metoda je volána, provedení požadavku na nový web a s novou instancí `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-228">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="c5ae9-229">Znovu čtení entity v kontextu této nové simuluje klasické pracovní plochy zpracování.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-229">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="c5ae9-230">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="c5ae9-230">Update the Delete page</span></span>

<span data-ttu-id="c5ae9-231">V této části kódu se přidá k implementaci vlastních chybových zpráv při volání `SaveChanges` selže.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-231">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="c5ae9-232">Přidejte řetězec tak, aby obsahovala možné chybové zprávy:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-232">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="c5ae9-233">Nahradit `OnGetAsync` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-233">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="c5ae9-234">Předchozí kód obsahuje volitelný parametr `saveChangesError`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-234">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="c5ae9-235">`saveChangesError` Označuje, zda byla volána metoda po selhání, aby student objekt odstranit.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-235">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="c5ae9-236">Operace odstranění může selhat z důvodu přechodné problémy se sítí.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-236">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="c5ae9-237">Přechodných síťových chyb jsou pravděpodobně v cloudu.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-237">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="c5ae9-238">`saveChangesError`má hodnotu false, pokud na stránce Odstranit `OnGetAsync` je volána z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-238">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="c5ae9-239">Když `OnGetAsync` je volán `OnPostAsync` (protože operace odstranění selhala), `saveChangesError` parametr má hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-239">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="c5ae9-240">Metoda OnPostAsync stránky Delete</span><span class="sxs-lookup"><span data-stu-id="c5ae9-240">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="c5ae9-241">Nahradit `OnPostAsync` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-241">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="c5ae9-242">Předchozí kód načte vybranou entitu, pak zavolá [odebrat](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) metoda nastavit stav entity `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-242">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="c5ae9-243">Když `SaveChanges` nazývá SQL odstranit vygenerované příkaz.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-243">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="c5ae9-244">Pokud `Remove` selže:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-244">If `Remove` fails:</span></span>

* <span data-ttu-id="c5ae9-245">Zachycení výjimky databáze.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-245">The DB exception is caught.</span></span>
* <span data-ttu-id="c5ae9-246">Odstranění stránky `OnGetAsync` metoda je volána s `saveChangesError=true`.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-246">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="c5ae9-247">Aktualizace stránky Razor Delete</span><span class="sxs-lookup"><span data-stu-id="c5ae9-247">Update the Delete Razor Page</span></span>

<span data-ttu-id="c5ae9-248">Přidejte následující zvýrazněný chybová zpráva na stránku odstranit Razor.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-248">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="c5ae9-249">Testování, odstranění.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-249">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="c5ae9-250">Běžné chyby</span><span class="sxs-lookup"><span data-stu-id="c5ae9-250">Common errors</span></span>

<span data-ttu-id="c5ae9-251">Studenti na Index nebo jiné odkazy nefungují:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-251">Students/Index or other links don't work:</span></span>

<span data-ttu-id="c5ae9-252">Ověřte stránky Razor obsahuje správný `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-252">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="c5ae9-253">Například by studenty/indexovou stránku Razor **není** obsahovat šablonu trasy:</span><span class="sxs-lookup"><span data-stu-id="c5ae9-253">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="c5ae9-254">Musí zahrnovat každou stránku Razor `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="c5ae9-254">Each Razor Page must include the `@page` directive.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="c5ae9-255">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c5ae9-255">Additional resources</span></span>

* [<span data-ttu-id="c5ae9-256">Verzi tohoto kurzu na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="c5ae9-256">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="c5ae9-257">[Předchozí](xref:data/ef-rp/intro)
> [další](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="c5ae9-257">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>
