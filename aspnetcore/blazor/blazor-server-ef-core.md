---
title: ASP.NET Core Blazor Server s Entity Framework Core (EFCore)
author: JeremyLikness
description: Pokyny k používání EF Core v Blazor Server aplikacích
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 2ea702aa73a2981afc223e5c1700d6ec2dc62df4
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227770"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="6726c-103">ASP.NET Core Blazor Server s Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="6726c-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="6726c-104">Podle: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="6726c-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6726c-105">Blazor Server je stavová architektura aplikace.</span><span class="sxs-lookup"><span data-stu-id="6726c-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="6726c-106">Aplikace udržuje průběžné připojení k serveru a stav uživatele je uložený v paměti serveru v *okruhu*.</span><span class="sxs-lookup"><span data-stu-id="6726c-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="6726c-107">Jedním z příkladů stavu uživatele jsou data uchovávaná v instancích služby [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , které jsou vymezeny pro okruh.</span><span class="sxs-lookup"><span data-stu-id="6726c-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="6726c-108">Jedinečný aplikační model, který Blazor Server poskytuje, vyžaduje zvláštní přístup k použití Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6726c-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span> 

> [!NOTE]
> <span data-ttu-id="6726c-109">Tento článek se zabývá EF Core v Blazor Server aplikacích.</span><span class="sxs-lookup"><span data-stu-id="6726c-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="6726c-110">Blazor WebAssembly aplikace běží v izolovaném prostoru (sandbox) websestavení, které brání většině přímých připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="6726c-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="6726c-111">Spuštění EF Core v systému Blazor WebAssembly je nad rámec tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="6726c-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="6726c-112">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="6726c-112">Sample app</span></span>

<span data-ttu-id="6726c-113">Ukázková aplikace byla sestavena jako referenční dokumentace pro Blazor Server aplikace, které používají EF Core.</span><span class="sxs-lookup"><span data-stu-id="6726c-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="6726c-114">Ukázková aplikace obsahuje mřížku s operacemi řazení a filtrování, odstraňování, přidávání a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="6726c-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="6726c-115">Ukázka ukazuje použití EF Core ke zpracování optimistické souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="6726c-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="6726c-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6726c-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6726c-117">Ukázka používá místní databázi [SQLite](https://www.sqlite.org/index.html) , aby ji bylo možné použít na libovolné platformě.</span><span class="sxs-lookup"><span data-stu-id="6726c-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="6726c-118">Ukázka také nakonfiguruje protokolování databáze tak, aby zobrazovalo vygenerované dotazy SQL.</span><span class="sxs-lookup"><span data-stu-id="6726c-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="6726c-119">Nakonfiguruje se v `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="6726c-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

<span data-ttu-id="6726c-120">Komponenty mřížka, přidání a zobrazení používají vzor "kontext pro operaci", ve kterém je vytvořen kontext pro každou operaci.</span><span class="sxs-lookup"><span data-stu-id="6726c-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="6726c-121">Součást pro úpravy používá vzor "kontext pro komponentu", kde je vytvořen kontext pro každou součást.</span><span class="sxs-lookup"><span data-stu-id="6726c-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

## <a name="database-access"></a><span data-ttu-id="6726c-122">Přístup k databázi</span><span class="sxs-lookup"><span data-stu-id="6726c-122">Database access</span></span>

<span data-ttu-id="6726c-123">EF Core spoléhá na a <xref:Microsoft.EntityFrameworkCore.DbContext> jako způsob [Konfigurace přístupu k databázi](/ef/core/miscellaneous/configuring-dbcontext) a fungování jako [_pracovní jednotky_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="6726c-123">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [_unit of work_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="6726c-124">EF Core poskytuje <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozšíření pro ASP.NET Core aplikace, které ve výchozím nastavení registrují kontext jako službu s _vymezeným oborem_ .</span><span class="sxs-lookup"><span data-stu-id="6726c-124">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a _scoped_ service by default.</span></span> <span data-ttu-id="6726c-125">V Blazor Server aplikacích můžou být vymezené registrace služeb problematické, protože instance je sdílená napříč součástmi v rámci okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="6726c-125">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="6726c-126"><xref:Microsoft.EntityFrameworkCore.DbContext> není bezpečné pro přístup z více vláken a není navržené pro souběžné použití.</span><span class="sxs-lookup"><span data-stu-id="6726c-126"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="6726c-127">Stávající životnosti jsou z těchto důvodů nevhodná:</span><span class="sxs-lookup"><span data-stu-id="6726c-127">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="6726c-128">**Singleton** sdílí stav napříč všemi uživateli aplikace a vede k nevhodnému souběžnému použití.</span><span class="sxs-lookup"><span data-stu-id="6726c-128">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="6726c-129">**Obor** (výchozí) představuje podobný problém mezi komponentami stejného uživatele.</span><span class="sxs-lookup"><span data-stu-id="6726c-129">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="6726c-130">**Přechodný** výsledek nové instance na žádost; avšak protože komponenty mohou být dlouhodobě dlouhodobé, výsledkem je delší kontext, než může být určena.</span><span class="sxs-lookup"><span data-stu-id="6726c-130">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="6726c-131">Následující doporučení jsou navržená tak, aby poskytovala konzistentní přístup k používání EF Core v Blazor Server aplikacích.</span><span class="sxs-lookup"><span data-stu-id="6726c-131">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span> 

* <span data-ttu-id="6726c-132">Ve výchozím nastavení zvažte použití jednoho kontextu na operaci.</span><span class="sxs-lookup"><span data-stu-id="6726c-132">By default, consider using one context per operation.</span></span> <span data-ttu-id="6726c-133">Kontext je navržen pro rychlou a nízkou režii vytváření instancí:</span><span class="sxs-lookup"><span data-stu-id="6726c-133">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="6726c-134">Použití příznaku k zabránění více souběžných operací:</span><span class="sxs-lookup"><span data-stu-id="6726c-134">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* <span data-ttu-id="6726c-135">U delších operací, které využijí [sledování změn](/ef/core/querying/tracking) nebo [řízení souběžnosti](/ef/core/saving/concurrency)EF Core, nastavte kontext na životní [dobu součásti](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="6726c-135">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="6726c-136">Nové instance DbContext</span><span class="sxs-lookup"><span data-stu-id="6726c-136">New DbContext instances</span></span>

<span data-ttu-id="6726c-137">Nejrychlejší způsob, jak vytvořit novou <xref:Microsoft.EntityFrameworkCore.DbContext> instanci, je použít `new` k vytvoření nové instance.</span><span class="sxs-lookup"><span data-stu-id="6726c-137">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="6726c-138">Existuje však několik scénářů, které mohou vyžadovat řešení dalších závislostí.</span><span class="sxs-lookup"><span data-stu-id="6726c-138">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="6726c-139">Například lze chtít použít [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) ke konfiguraci kontextu.</span><span class="sxs-lookup"><span data-stu-id="6726c-139">For example, you may wish to use [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span> 

<span data-ttu-id="6726c-140">Doporučeným řešením pro vytvoření nového <xref:Microsoft.EntityFrameworkCore.DbContext> se závislostmi je použití továrny.</span><span class="sxs-lookup"><span data-stu-id="6726c-140">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="6726c-141">EF Core 5,0 nebo novější poskytuje integrovaný objekt pro vytváření nových kontextů.</span><span class="sxs-lookup"><span data-stu-id="6726c-141">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="6726c-142">Následující příklad konfiguruje [SQLite](https://www.sqlite.org/index.html) a povoluje protokolování dat.</span><span class="sxs-lookup"><span data-stu-id="6726c-142">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="6726c-143">Kód používá metodu rozšíření ke konfiguraci objektu pro vytváření databáze pro DI a poskytuje výchozí možnosti:</span><span class="sxs-lookup"><span data-stu-id="6726c-143">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

<span data-ttu-id="6726c-144">Továrna je vložena do komponent a slouží k vytváření nových instancí.</span><span class="sxs-lookup"><span data-stu-id="6726c-144">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="6726c-145">Například v `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="6726c-145">For example, in `Pages/Index.razor`:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="6726c-146">Rozsah pro životní dobu součásti</span><span class="sxs-lookup"><span data-stu-id="6726c-146">Scope to the component lifetime</span></span>

<span data-ttu-id="6726c-147">Možná budete chtít vytvořit <xref:Microsoft.EntityFrameworkCore.DbContext> , který existuje pro celou dobu platnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="6726c-147">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="6726c-148">To vám umožní použít ho jako [pracovní jednotku](https://martinfowler.com/eaaCatalog/unitOfWork.html) a využít vestavěné funkce, jako je sledování změn a řešení souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="6726c-148">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="6726c-149">Pomocí továrny můžete vytvořit kontext a sledovat ho po dobu života součásti.</span><span class="sxs-lookup"><span data-stu-id="6726c-149">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="6726c-150">Nejprve implementujte <xref:System.IDisposable> a založit objekt pro vytváření, jak je znázorněno v `Pages/EditContact.razor` následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6726c-150">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

<span data-ttu-id="6726c-151">Ukázková aplikace zajistí, že se kontakt odstraní při vyřazení součásti:</span><span class="sxs-lookup"><span data-stu-id="6726c-151">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

<span data-ttu-id="6726c-152">Nakonec `OnInitializedAsync` je přepsáno, aby se vytvořil nový kontext.</span><span class="sxs-lookup"><span data-stu-id="6726c-152">Finally, `OnInitializedAsync` is overridden to create a new context.</span></span> <span data-ttu-id="6726c-153">V ukázkové aplikaci `OnInitializedAsync` načte kontakt ve stejné metodě:</span><span class="sxs-lookup"><span data-stu-id="6726c-153">In the sample app, `OnInitializedAsync` loads the contact in the same method:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="6726c-154">Blazor Server je stavová architektura aplikace.</span><span class="sxs-lookup"><span data-stu-id="6726c-154">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="6726c-155">Aplikace udržuje průběžné připojení k serveru a stav uživatele je uložený v paměti serveru v *okruhu*.</span><span class="sxs-lookup"><span data-stu-id="6726c-155">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="6726c-156">Jedním z příkladů stavu uživatele jsou data uchovávaná v instancích služby [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , které jsou vymezeny pro okruh.</span><span class="sxs-lookup"><span data-stu-id="6726c-156">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="6726c-157">Jedinečný aplikační model, který Blazor Server poskytuje, vyžaduje zvláštní přístup k použití Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6726c-157">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span> 

> [!NOTE]
> <span data-ttu-id="6726c-158">Tento článek se zabývá EF Core v Blazor Server aplikacích.</span><span class="sxs-lookup"><span data-stu-id="6726c-158">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="6726c-159">Blazor WebAssembly aplikace běží v izolovaném prostoru (sandbox) websestavení, které brání většině přímých připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="6726c-159">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="6726c-160">Spuštění EF Core v systému Blazor WebAssembly je nad rámec tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="6726c-160">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="6726c-161">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="6726c-161">Sample app</span></span>

<span data-ttu-id="6726c-162">Ukázková aplikace byla sestavena jako referenční dokumentace pro Blazor Server aplikace, které používají EF Core.</span><span class="sxs-lookup"><span data-stu-id="6726c-162">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="6726c-163">Ukázková aplikace obsahuje mřížku s operacemi řazení a filtrování, odstraňování, přidávání a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="6726c-163">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="6726c-164">Ukázka ukazuje použití EF Core ke zpracování optimistické souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="6726c-164">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="6726c-165">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6726c-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6726c-166">Ukázka používá místní databázi [SQLite](https://www.sqlite.org/index.html) , aby ji bylo možné použít na libovolné platformě.</span><span class="sxs-lookup"><span data-stu-id="6726c-166">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="6726c-167">Ukázka také nakonfiguruje protokolování databáze tak, aby zobrazovalo vygenerované dotazy SQL.</span><span class="sxs-lookup"><span data-stu-id="6726c-167">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="6726c-168">Nakonfiguruje se v `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="6726c-168">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

<span data-ttu-id="6726c-169">Komponenty mřížka, přidání a zobrazení používají vzor "kontext pro operaci", ve kterém je vytvořen kontext pro každou operaci.</span><span class="sxs-lookup"><span data-stu-id="6726c-169">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="6726c-170">Součást pro úpravy používá vzor "kontext pro komponentu", kde je vytvořen kontext pro každou součást.</span><span class="sxs-lookup"><span data-stu-id="6726c-170">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

## <a name="database-access"></a><span data-ttu-id="6726c-171">Přístup k databázi</span><span class="sxs-lookup"><span data-stu-id="6726c-171">Database access</span></span>

<span data-ttu-id="6726c-172">EF Core spoléhá na a <xref:Microsoft.EntityFrameworkCore.DbContext> jako způsob [Konfigurace přístupu k databázi](/ef/core/miscellaneous/configuring-dbcontext) a fungování jako [_pracovní jednotky_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="6726c-172">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [_unit of work_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="6726c-173">EF Core poskytuje <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozšíření pro ASP.NET Core aplikace, které ve výchozím nastavení registrují kontext jako službu s _vymezeným oborem_ .</span><span class="sxs-lookup"><span data-stu-id="6726c-173">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a _scoped_ service by default.</span></span> <span data-ttu-id="6726c-174">V Blazor Server aplikacích může to být problematické, protože instance je sdílená napříč součástmi v rámci okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="6726c-174">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="6726c-175"><xref:Microsoft.EntityFrameworkCore.DbContext> není bezpečné pro přístup z více vláken a není navržené pro souběžné použití.</span><span class="sxs-lookup"><span data-stu-id="6726c-175"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="6726c-176">Stávající životnosti jsou z těchto důvodů nevhodná:</span><span class="sxs-lookup"><span data-stu-id="6726c-176">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="6726c-177">**Singleton** sdílí stav napříč všemi uživateli aplikace a vede k nevhodnému souběžnému použití.</span><span class="sxs-lookup"><span data-stu-id="6726c-177">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="6726c-178">**Obor** (výchozí) představuje podobný problém mezi komponentami stejného uživatele.</span><span class="sxs-lookup"><span data-stu-id="6726c-178">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="6726c-179">**Přechodný** výsledek nové instance na žádost; avšak protože komponenty mohou být dlouhodobě dlouhodobé, výsledkem je delší kontext, než může být určena.</span><span class="sxs-lookup"><span data-stu-id="6726c-179">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="6726c-180">Přístup k databázi</span><span class="sxs-lookup"><span data-stu-id="6726c-180">Database access</span></span>

<span data-ttu-id="6726c-181">Následující doporučení jsou navržená tak, aby poskytovala konzistentní přístup k používání EF Core v Blazor Server aplikacích.</span><span class="sxs-lookup"><span data-stu-id="6726c-181">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span> 

* <span data-ttu-id="6726c-182">Ve výchozím nastavení zvažte použití jednoho kontextu na operaci.</span><span class="sxs-lookup"><span data-stu-id="6726c-182">By default, consider using one context per operation.</span></span> <span data-ttu-id="6726c-183">Kontext je navržen pro rychlou a nízkou režii vytváření instancí:</span><span class="sxs-lookup"><span data-stu-id="6726c-183">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="6726c-184">Použití příznaku k zabránění více souběžných operací:</span><span class="sxs-lookup"><span data-stu-id="6726c-184">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* <span data-ttu-id="6726c-185">U delších operací, které využijí [sledování změn](/ef/core/querying/tracking) nebo [řízení souběžnosti](/ef/core/saving/concurrency)EF Core, nastavte kontext na životní [dobu součásti](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="6726c-185">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="6726c-186">Vytvoření nových instancí DbContext</span><span class="sxs-lookup"><span data-stu-id="6726c-186">Create new DbContext instances</span></span>

<span data-ttu-id="6726c-187">Nejrychlejší způsob, jak vytvořit novou <xref:Microsoft.EntityFrameworkCore.DbContext> instanci, je použít `new` k vytvoření nové instance.</span><span class="sxs-lookup"><span data-stu-id="6726c-187">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="6726c-188">Existuje však několik scénářů, které mohou vyžadovat řešení dalších závislostí.</span><span class="sxs-lookup"><span data-stu-id="6726c-188">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="6726c-189">Například lze chtít použít [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) ke konfiguraci kontextu.</span><span class="sxs-lookup"><span data-stu-id="6726c-189">For example, you may wish to use [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span> 

<span data-ttu-id="6726c-190">Doporučeným řešením pro vytvoření nového <xref:Microsoft.EntityFrameworkCore.DbContext> se závislostmi je použití továrny.</span><span class="sxs-lookup"><span data-stu-id="6726c-190">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="6726c-191">Ukázková aplikace implementuje svou vlastní továrnu v nástroji `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="6726c-191">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span> 

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="6726c-192">Následující příklad konfiguruje [SQLite](https://www.sqlite.org/index.html) a povoluje protokolování dat.</span><span class="sxs-lookup"><span data-stu-id="6726c-192">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="6726c-193">Kód používá metodu rozšíření ke konfiguraci objektu pro vytváření databáze pro DI a poskytuje výchozí možnosti:</span><span class="sxs-lookup"><span data-stu-id="6726c-193">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

<span data-ttu-id="6726c-194">Továrna je vložena do komponent a slouží k vytváření nových instancí.</span><span class="sxs-lookup"><span data-stu-id="6726c-194">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="6726c-195">Například v `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="6726c-195">For example, in `Pages/Index.razor`:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="6726c-196">Rozsah pro životní dobu součásti</span><span class="sxs-lookup"><span data-stu-id="6726c-196">Scope to the component lifetime</span></span>

<span data-ttu-id="6726c-197">Možná budete chtít vytvořit <xref:Microsoft.EntityFrameworkCore.DbContext> , který existuje pro celou dobu platnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="6726c-197">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="6726c-198">To vám umožní použít ho jako [pracovní jednotku](https://martinfowler.com/eaaCatalog/unitOfWork.html) a využít vestavěné funkce, jako je sledování změn a řešení souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="6726c-198">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="6726c-199">Pomocí továrny můžete vytvořit kontext a sledovat ho po dobu života součásti.</span><span class="sxs-lookup"><span data-stu-id="6726c-199">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="6726c-200">Nejprve implementujte <xref:System.IDisposable> a založit objekt pro vytváření, jak je znázorněno v `Pages/EditContact.razor` následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6726c-200">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

<span data-ttu-id="6726c-201">Ukázková aplikace zajistí, že se kontakt odstraní při vyřazení součásti:</span><span class="sxs-lookup"><span data-stu-id="6726c-201">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

<span data-ttu-id="6726c-202">Nakonec `OnInitializedAsync` je přepsáno, aby se vytvořil nový kontext.</span><span class="sxs-lookup"><span data-stu-id="6726c-202">Finally, `OnInitializedAsync` is overridden to create a new context.</span></span> <span data-ttu-id="6726c-203">V ukázkové aplikaci `OnInitializedAsync` načte kontakt ve stejné metodě:</span><span class="sxs-lookup"><span data-stu-id="6726c-203">In the sample app, `OnInitializedAsync` loads the contact in the same method:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6726c-204">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6726c-204">Additional resources</span></span>

> [<span data-ttu-id="6726c-205">Dokumentace k EF Core</span><span class="sxs-lookup"><span data-stu-id="6726c-205">EF Core documentation</span></span>](/ef/)
