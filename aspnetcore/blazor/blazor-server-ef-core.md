---
title: ASP.NET Core Blazor Server s Entity Framework Core (EFCore)
author: JeremyLikness
description: Pokyny k používání EF Core v Blazor Server aplikacích
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: a1b295b2ce42bc5ee06b8b9579ea2c70d480580a
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009658"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="d29e6-103">ASP.NET Core Blazor Server s Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="d29e6-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="d29e6-104">Podle: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="d29e6-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d29e6-105">Blazor Server je stavová architektura aplikace.</span><span class="sxs-lookup"><span data-stu-id="d29e6-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="d29e6-106">Aplikace udržuje průběžné připojení k serveru a stav uživatele je uložený v paměti serveru v *okruhu*.</span><span class="sxs-lookup"><span data-stu-id="d29e6-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="d29e6-107">Jedním z příkladů stavu uživatele jsou data uchovávaná v instancích služby [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , které jsou vymezeny pro okruh.</span><span class="sxs-lookup"><span data-stu-id="d29e6-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="d29e6-108">Jedinečný aplikační model, který Blazor Server poskytuje, vyžaduje zvláštní přístup k použití Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d29e6-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d29e6-109">Tento článek se zabývá EF Core v Blazor Server aplikacích.</span><span class="sxs-lookup"><span data-stu-id="d29e6-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="d29e6-110">Blazor WebAssembly aplikace běží v izolovaném prostoru (sandbox) websestavení, které brání většině přímých připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="d29e6-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="d29e6-111">Spuštění EF Core v systému Blazor WebAssembly je nad rámec tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="d29e6-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="d29e6-112">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="d29e6-112">Sample app</span></span>

<span data-ttu-id="d29e6-113">Ukázková aplikace byla sestavena jako referenční dokumentace pro Blazor Server aplikace, které používají EF Core.</span><span class="sxs-lookup"><span data-stu-id="d29e6-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="d29e6-114">Ukázková aplikace obsahuje mřížku s operacemi řazení a filtrování, odstraňování, přidávání a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="d29e6-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="d29e6-115">Ukázka ukazuje použití EF Core ke zpracování optimistické souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="d29e6-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="d29e6-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d29e6-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d29e6-117">Ukázka používá místní databázi [SQLite](https://www.sqlite.org/index.html) , aby ji bylo možné použít na libovolné platformě.</span><span class="sxs-lookup"><span data-stu-id="d29e6-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="d29e6-118">Ukázka také nakonfiguruje protokolování databáze tak, aby zobrazovalo vygenerované dotazy SQL.</span><span class="sxs-lookup"><span data-stu-id="d29e6-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="d29e6-119">Nakonfiguruje se v `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="d29e6-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="d29e6-120">Komponenty mřížka, přidání a zobrazení používají vzor "kontext pro operaci", ve kterém je vytvořen kontext pro každou operaci.</span><span class="sxs-lookup"><span data-stu-id="d29e6-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="d29e6-121">Součást pro úpravy používá vzor "kontext pro komponentu", kde je vytvořen kontext pro každou součást.</span><span class="sxs-lookup"><span data-stu-id="d29e6-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="d29e6-122">Některé příklady kódu v tomto tématu vyžadují obory názvů a služby, které se nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="d29e6-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="d29e6-123">Pokud si chcete prohlédnout plně funkční kód, včetně požadavků [`@using`](xref:mvc/views/razor#using) a [`@inject`](xref:mvc/views/razor#inject) direktiv pro Razor Příklady, přečtěte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="d29e6-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="d29e6-124">Přístup k databázi</span><span class="sxs-lookup"><span data-stu-id="d29e6-124">Database access</span></span>

<span data-ttu-id="d29e6-125">EF Core spoléhá na a <xref:Microsoft.EntityFrameworkCore.DbContext> jako způsob [Konfigurace přístupu k databázi](/ef/core/miscellaneous/configuring-dbcontext) a fungování jako [*pracovní jednotky*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="d29e6-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="d29e6-126">EF Core poskytuje <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozšíření pro ASP.NET Core aplikace, které ve výchozím nastavení registrují kontext jako službu s *vymezeným oborem* .</span><span class="sxs-lookup"><span data-stu-id="d29e6-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="d29e6-127">V Blazor Server aplikacích můžou být vymezené registrace služeb problematické, protože instance je sdílená napříč součástmi v rámci okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="d29e6-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="d29e6-128"><xref:Microsoft.EntityFrameworkCore.DbContext> není bezpečné pro přístup z více vláken a není navržené pro souběžné použití.</span><span class="sxs-lookup"><span data-stu-id="d29e6-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="d29e6-129">Stávající životnosti jsou z těchto důvodů nevhodná:</span><span class="sxs-lookup"><span data-stu-id="d29e6-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="d29e6-130">**Singleton** sdílí stav napříč všemi uživateli aplikace a vede k nevhodnému souběžnému použití.</span><span class="sxs-lookup"><span data-stu-id="d29e6-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="d29e6-131">**Obor** (výchozí) představuje podobný problém mezi komponentami stejného uživatele.</span><span class="sxs-lookup"><span data-stu-id="d29e6-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="d29e6-132">**Přechodný** výsledek nové instance na žádost; avšak protože komponenty mohou být dlouhodobě dlouhodobé, výsledkem je delší kontext, než může být určena.</span><span class="sxs-lookup"><span data-stu-id="d29e6-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="d29e6-133">Následující doporučení jsou navržená tak, aby poskytovala konzistentní přístup k používání EF Core v Blazor Server aplikacích.</span><span class="sxs-lookup"><span data-stu-id="d29e6-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="d29e6-134">Ve výchozím nastavení zvažte použití jednoho kontextu na operaci.</span><span class="sxs-lookup"><span data-stu-id="d29e6-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="d29e6-135">Kontext je navržen pro rychlou a nízkou režii vytváření instancí:</span><span class="sxs-lookup"><span data-stu-id="d29e6-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="d29e6-136">Použití příznaku k zabránění více souběžných operací:</span><span class="sxs-lookup"><span data-stu-id="d29e6-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="d29e6-137">Umístěte operace za `Loading = true;` řádek v `try` bloku.</span><span class="sxs-lookup"><span data-stu-id="d29e6-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="d29e6-138">U delších operací, které využijí [sledování změn](/ef/core/querying/tracking) nebo [řízení souběžnosti](/ef/core/saving/concurrency)EF Core, nastavte kontext na životní [dobu součásti](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="d29e6-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="d29e6-139">Nové instance DbContext</span><span class="sxs-lookup"><span data-stu-id="d29e6-139">New DbContext instances</span></span>

<span data-ttu-id="d29e6-140">Nejrychlejší způsob, jak vytvořit novou <xref:Microsoft.EntityFrameworkCore.DbContext> instanci, je použít `new` k vytvoření nové instance.</span><span class="sxs-lookup"><span data-stu-id="d29e6-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="d29e6-141">Existuje však několik scénářů, které mohou vyžadovat řešení dalších závislostí.</span><span class="sxs-lookup"><span data-stu-id="d29e6-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="d29e6-142">Například lze chtít použít [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) ke konfiguraci kontextu.</span><span class="sxs-lookup"><span data-stu-id="d29e6-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="d29e6-143">Doporučeným řešením pro vytvoření nového <xref:Microsoft.EntityFrameworkCore.DbContext> se závislostmi je použití továrny.</span><span class="sxs-lookup"><span data-stu-id="d29e6-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="d29e6-144">EF Core 5,0 nebo novější poskytuje integrovaný objekt pro vytváření nových kontextů.</span><span class="sxs-lookup"><span data-stu-id="d29e6-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="d29e6-145">Následující příklad konfiguruje [SQLite](https://www.sqlite.org/index.html) a povoluje protokolování dat.</span><span class="sxs-lookup"><span data-stu-id="d29e6-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="d29e6-146">Kód používá metodu rozšíření ke konfiguraci objektu pro vytváření databáze pro DI a poskytuje výchozí možnosti:</span><span class="sxs-lookup"><span data-stu-id="d29e6-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="d29e6-147">Továrna je vložena do komponent a slouží k vytváření nových instancí.</span><span class="sxs-lookup"><span data-stu-id="d29e6-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="d29e6-148">Například v `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="d29e6-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="d29e6-149">`Wrapper` je [odkaz na komponentu](xref:blazor/components/index#capture-references-to-components) `GridWrapper` komponenty.</span><span class="sxs-lookup"><span data-stu-id="d29e6-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="d29e6-150">Viz `Index` součást ( `Pages/Index.razor` ) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="d29e6-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="d29e6-151">Rozsah pro životní dobu součásti</span><span class="sxs-lookup"><span data-stu-id="d29e6-151">Scope to the component lifetime</span></span>

<span data-ttu-id="d29e6-152">Možná budete chtít vytvořit <xref:Microsoft.EntityFrameworkCore.DbContext> , který existuje pro celou dobu platnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="d29e6-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="d29e6-153">To vám umožní použít ho jako [pracovní jednotku](https://martinfowler.com/eaaCatalog/unitOfWork.html) a využít vestavěné funkce, jako je sledování změn a řešení souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="d29e6-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="d29e6-154">Pomocí továrny můžete vytvořit kontext a sledovat ho po dobu života součásti.</span><span class="sxs-lookup"><span data-stu-id="d29e6-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="d29e6-155">Nejprve implementujte <xref:System.IDisposable> a založit objekt pro vytváření, jak je znázorněno v `Pages/EditContact.razor` následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d29e6-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="d29e6-156">Ukázková aplikace zajišťuje, že je kontext vyřazený, když je součást Vyřazená:</span><span class="sxs-lookup"><span data-stu-id="d29e6-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="d29e6-157">Nakonec [`OnInitializedAsync`](xref:blazor/components/lifecycle) je přepsáno, aby se vytvořil nový kontext.</span><span class="sxs-lookup"><span data-stu-id="d29e6-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="d29e6-158">V ukázkové aplikaci [`OnInitializedAsync`](xref:blazor/components/lifecycle) načte kontakt ve stejné metodě:</span><span class="sxs-lookup"><span data-stu-id="d29e6-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="d29e6-159">Blazor Server je stavová architektura aplikace.</span><span class="sxs-lookup"><span data-stu-id="d29e6-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="d29e6-160">Aplikace udržuje průběžné připojení k serveru a stav uživatele je uložený v paměti serveru v *okruhu*.</span><span class="sxs-lookup"><span data-stu-id="d29e6-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="d29e6-161">Jedním z příkladů stavu uživatele jsou data uchovávaná v instancích služby [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , které jsou vymezeny pro okruh.</span><span class="sxs-lookup"><span data-stu-id="d29e6-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="d29e6-162">Jedinečný aplikační model, který Blazor Server poskytuje, vyžaduje zvláštní přístup k použití Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d29e6-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d29e6-163">Tento článek se zabývá EF Core v Blazor Server aplikacích.</span><span class="sxs-lookup"><span data-stu-id="d29e6-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="d29e6-164">Blazor WebAssembly aplikace běží v izolovaném prostoru (sandbox) websestavení, které brání většině přímých připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="d29e6-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="d29e6-165">Spuštění EF Core v systému Blazor WebAssembly je nad rámec tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="d29e6-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="d29e6-166">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="d29e6-166">Sample app</span></span>

<span data-ttu-id="d29e6-167">Ukázková aplikace byla sestavena jako referenční dokumentace pro Blazor Server aplikace, které používají EF Core.</span><span class="sxs-lookup"><span data-stu-id="d29e6-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="d29e6-168">Ukázková aplikace obsahuje mřížku s operacemi řazení a filtrování, odstraňování, přidávání a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="d29e6-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="d29e6-169">Ukázka ukazuje použití EF Core ke zpracování optimistické souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="d29e6-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="d29e6-170">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d29e6-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d29e6-171">Ukázka používá místní databázi [SQLite](https://www.sqlite.org/index.html) , aby ji bylo možné použít na libovolné platformě.</span><span class="sxs-lookup"><span data-stu-id="d29e6-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="d29e6-172">Ukázka také nakonfiguruje protokolování databáze tak, aby zobrazovalo vygenerované dotazy SQL.</span><span class="sxs-lookup"><span data-stu-id="d29e6-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="d29e6-173">Nakonfiguruje se v `appsettings.Development.json` :</span><span class="sxs-lookup"><span data-stu-id="d29e6-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="d29e6-174">Komponenty mřížka, přidání a zobrazení používají vzor "kontext pro operaci", ve kterém je vytvořen kontext pro každou operaci.</span><span class="sxs-lookup"><span data-stu-id="d29e6-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="d29e6-175">Součást pro úpravy používá vzor "kontext pro komponentu", kde je vytvořen kontext pro každou součást.</span><span class="sxs-lookup"><span data-stu-id="d29e6-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="d29e6-176">Některé příklady kódu v tomto tématu vyžadují obory názvů a služby, které se nezobrazují.</span><span class="sxs-lookup"><span data-stu-id="d29e6-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="d29e6-177">Pokud si chcete prohlédnout plně funkční kód, včetně požadavků [`@using`](xref:mvc/views/razor#using) a [`@inject`](xref:mvc/views/razor#inject) direktiv pro Razor Příklady, přečtěte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span><span class="sxs-lookup"><span data-stu-id="d29e6-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="d29e6-178">Přístup k databázi</span><span class="sxs-lookup"><span data-stu-id="d29e6-178">Database access</span></span>

<span data-ttu-id="d29e6-179">EF Core spoléhá na a <xref:Microsoft.EntityFrameworkCore.DbContext> jako způsob [Konfigurace přístupu k databázi](/ef/core/miscellaneous/configuring-dbcontext) a fungování jako [*pracovní jednotky*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="d29e6-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="d29e6-180">EF Core poskytuje <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozšíření pro ASP.NET Core aplikace, které ve výchozím nastavení registrují kontext jako službu s *vymezeným oborem* .</span><span class="sxs-lookup"><span data-stu-id="d29e6-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="d29e6-181">V Blazor Server aplikacích může to být problematické, protože instance je sdílená napříč součástmi v rámci okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="d29e6-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="d29e6-182"><xref:Microsoft.EntityFrameworkCore.DbContext> není bezpečné pro přístup z více vláken a není navržené pro souběžné použití.</span><span class="sxs-lookup"><span data-stu-id="d29e6-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="d29e6-183">Stávající životnosti jsou z těchto důvodů nevhodná:</span><span class="sxs-lookup"><span data-stu-id="d29e6-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="d29e6-184">**Singleton** sdílí stav napříč všemi uživateli aplikace a vede k nevhodnému souběžnému použití.</span><span class="sxs-lookup"><span data-stu-id="d29e6-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="d29e6-185">**Obor** (výchozí) představuje podobný problém mezi komponentami stejného uživatele.</span><span class="sxs-lookup"><span data-stu-id="d29e6-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="d29e6-186">**Přechodný** výsledek nové instance na žádost; avšak protože komponenty mohou být dlouhodobě dlouhodobé, výsledkem je delší kontext, než může být určena.</span><span class="sxs-lookup"><span data-stu-id="d29e6-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="d29e6-187">Přístup k databázi</span><span class="sxs-lookup"><span data-stu-id="d29e6-187">Database access</span></span>

<span data-ttu-id="d29e6-188">Následující doporučení jsou navržená tak, aby poskytovala konzistentní přístup k používání EF Core v Blazor Server aplikacích.</span><span class="sxs-lookup"><span data-stu-id="d29e6-188">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="d29e6-189">Ve výchozím nastavení zvažte použití jednoho kontextu na operaci.</span><span class="sxs-lookup"><span data-stu-id="d29e6-189">By default, consider using one context per operation.</span></span> <span data-ttu-id="d29e6-190">Kontext je navržen pro rychlou a nízkou režii vytváření instancí:</span><span class="sxs-lookup"><span data-stu-id="d29e6-190">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="d29e6-191">Použití příznaku k zabránění více souběžných operací:</span><span class="sxs-lookup"><span data-stu-id="d29e6-191">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="d29e6-192">Umístěte operace za `Loading = true;` řádek v `try` bloku.</span><span class="sxs-lookup"><span data-stu-id="d29e6-192">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="d29e6-193">U delších operací, které využijí [sledování změn](/ef/core/querying/tracking) nebo [řízení souběžnosti](/ef/core/saving/concurrency)EF Core, nastavte kontext na životní [dobu součásti](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="d29e6-193">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="d29e6-194">Vytvoření nových instancí DbContext</span><span class="sxs-lookup"><span data-stu-id="d29e6-194">Create new DbContext instances</span></span>

<span data-ttu-id="d29e6-195">Nejrychlejší způsob, jak vytvořit novou <xref:Microsoft.EntityFrameworkCore.DbContext> instanci, je použít `new` k vytvoření nové instance.</span><span class="sxs-lookup"><span data-stu-id="d29e6-195">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="d29e6-196">Existuje však několik scénářů, které mohou vyžadovat řešení dalších závislostí.</span><span class="sxs-lookup"><span data-stu-id="d29e6-196">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="d29e6-197">Například lze chtít použít [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) ke konfiguraci kontextu.</span><span class="sxs-lookup"><span data-stu-id="d29e6-197">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="d29e6-198">Doporučeným řešením pro vytvoření nového <xref:Microsoft.EntityFrameworkCore.DbContext> se závislostmi je použití továrny.</span><span class="sxs-lookup"><span data-stu-id="d29e6-198">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="d29e6-199">Ukázková aplikace implementuje svou vlastní továrnu v nástroji `Data/DbContextFactory.cs` .</span><span class="sxs-lookup"><span data-stu-id="d29e6-199">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="d29e6-200">V předchozí továrně <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> splňuje všechny závislosti prostřednictvím poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="d29e6-200">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="d29e6-201">Následující příklad konfiguruje [SQLite](https://www.sqlite.org/index.html) a povoluje protokolování dat.</span><span class="sxs-lookup"><span data-stu-id="d29e6-201">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="d29e6-202">Kód používá metodu rozšíření ke konfiguraci objektu pro vytváření databáze pro DI a poskytuje výchozí možnosti:</span><span class="sxs-lookup"><span data-stu-id="d29e6-202">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="d29e6-203">Továrna je vložena do komponent a slouží k vytváření nových instancí.</span><span class="sxs-lookup"><span data-stu-id="d29e6-203">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="d29e6-204">Například v `Pages/Index.razor` :</span><span class="sxs-lookup"><span data-stu-id="d29e6-204">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="d29e6-205">`Wrapper` je [odkaz na komponentu](xref:blazor/components/index#capture-references-to-components) `GridWrapper` komponenty.</span><span class="sxs-lookup"><span data-stu-id="d29e6-205">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="d29e6-206">Viz `Index` součást ( `Pages/Index.razor` ) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span><span class="sxs-lookup"><span data-stu-id="d29e6-206">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="d29e6-207">Rozsah pro životní dobu součásti</span><span class="sxs-lookup"><span data-stu-id="d29e6-207">Scope to the component lifetime</span></span>

<span data-ttu-id="d29e6-208">Možná budete chtít vytvořit <xref:Microsoft.EntityFrameworkCore.DbContext> , který existuje pro celou dobu platnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="d29e6-208">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="d29e6-209">To vám umožní použít ho jako [pracovní jednotku](https://martinfowler.com/eaaCatalog/unitOfWork.html) a využít vestavěné funkce, jako je sledování změn a řešení souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="d29e6-209">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="d29e6-210">Pomocí továrny můžete vytvořit kontext a sledovat ho po dobu života součásti.</span><span class="sxs-lookup"><span data-stu-id="d29e6-210">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="d29e6-211">Nejprve implementujte <xref:System.IDisposable> a založit objekt pro vytváření, jak je znázorněno v `Pages/EditContact.razor` následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d29e6-211">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="d29e6-212">Ukázková aplikace zajišťuje, že je kontext vyřazený, když je součást Vyřazená:</span><span class="sxs-lookup"><span data-stu-id="d29e6-212">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="d29e6-213">Nakonec [`OnInitializedAsync`](xref:blazor/components/lifecycle) je přepsáno, aby se vytvořil nový kontext.</span><span class="sxs-lookup"><span data-stu-id="d29e6-213">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="d29e6-214">V ukázkové aplikaci [`OnInitializedAsync`](xref:blazor/components/lifecycle) načte kontakt ve stejné metodě:</span><span class="sxs-lookup"><span data-stu-id="d29e6-214">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="d29e6-215">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d29e6-215">In the preceding example:</span></span>

* <span data-ttu-id="d29e6-216">Když `Busy` je nastaveno na `true` , mohou být zahájeny asynchronní operace.</span><span class="sxs-lookup"><span data-stu-id="d29e6-216">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="d29e6-217">Když `Busy` je nastaveno zpět na `false` , by měly být dokončeny asynchronní operace.</span><span class="sxs-lookup"><span data-stu-id="d29e6-217">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="d29e6-218">Do bloku umístěte další logiku zpracování chyb `catch` .</span><span class="sxs-lookup"><span data-stu-id="d29e6-218">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d29e6-219">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d29e6-219">Additional resources</span></span>

* [<span data-ttu-id="d29e6-220">Dokumentace k EF Core</span><span class="sxs-lookup"><span data-stu-id="d29e6-220">EF Core documentation</span></span>](/ef/)
