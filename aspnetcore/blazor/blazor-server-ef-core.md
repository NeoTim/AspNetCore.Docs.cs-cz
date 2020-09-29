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
ms.openlocfilehash: 36c0915649d9a25cf1b6b2d9ec69c3919aa7bc2c
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424162"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server s Entity Framework Core (EFCore)

Podle: [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server je stavová architektura aplikace. Aplikace udržuje průběžné připojení k serveru a stav uživatele je uložený v paměti serveru v *okruhu*. Jedním z příkladů stavu uživatele jsou data uchovávaná v instancích služby [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , které jsou vymezeny pro okruh. Jedinečný aplikační model, který Blazor Server poskytuje, vyžaduje zvláštní přístup k použití Entity Framework Core.

> [!NOTE]
> Tento článek se zabývá EF Core v Blazor Server aplikacích. Blazor WebAssembly aplikace běží v izolovaném prostoru (sandbox) websestavení, které brání většině přímých připojení k databázi. Spuštění EF Core v systému Blazor WebAssembly je nad rámec tohoto článku.

<h2 id="sample-app-5x">Ukázková aplikace</h2>

Ukázková aplikace byla sestavena jako referenční dokumentace pro Blazor Server aplikace, které používají EF Core. Ukázková aplikace obsahuje mřížku s operacemi řazení a filtrování, odstraňování, přidávání a aktualizace. Ukázka ukazuje použití EF Core ke zpracování optimistické souběžnosti.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázka používá místní databázi [SQLite](https://www.sqlite.org/index.html) , aby ji bylo možné použít na libovolné platformě. Ukázka také nakonfiguruje protokolování databáze tak, aby zobrazovalo vygenerované dotazy SQL. Nakonfiguruje se v `appsettings.Development.json` :

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Komponenty mřížka, přidání a zobrazení používají vzor "kontext pro operaci", ve kterém je vytvořen kontext pro každou operaci. Součást pro úpravy používá vzor "kontext pro komponentu", kde je vytvořen kontext pro každou součást.

> [!NOTE]
> Některé příklady kódu v tomto tématu vyžadují obory názvů a služby, které se nezobrazují. Pokud si chcete prohlédnout plně funkční kód, včetně požadavků [`@using`](xref:mvc/views/razor#using) a [`@inject`](xref:mvc/views/razor#inject) direktiv pro Razor Příklady, přečtěte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).

<h2 id="database-access-5x">Přístup k databázi</h2>

EF Core spoléhá na a <xref:Microsoft.EntityFrameworkCore.DbContext> jako způsob [Konfigurace přístupu k databázi](/ef/core/miscellaneous/configuring-dbcontext) a fungování jako [*pracovní jednotky*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core poskytuje <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozšíření pro ASP.NET Core aplikace, které ve výchozím nastavení registrují kontext jako službu s *vymezeným oborem* . V Blazor Server aplikacích můžou být vymezené registrace služeb problematické, protože instance je sdílená napříč součástmi v rámci okruhu uživatele. <xref:Microsoft.EntityFrameworkCore.DbContext> není bezpečné pro přístup z více vláken a není navržené pro souběžné použití. Stávající životnosti jsou z těchto důvodů nevhodná:

* **Singleton** sdílí stav napříč všemi uživateli aplikace a vede k nevhodnému souběžnému použití.
* **Obor** (výchozí) představuje podobný problém mezi komponentami stejného uživatele.
* **Přechodný** výsledek nové instance na žádost; avšak protože komponenty mohou být dlouhodobě dlouhodobé, výsledkem je delší kontext, než může být určena.

Následující doporučení jsou navržená tak, aby poskytovala konzistentní přístup k používání EF Core v Blazor Server aplikacích.

* Ve výchozím nastavení zvažte použití jednoho kontextu na operaci. Kontext je navržen pro rychlou a nízkou režii vytváření instancí:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Použití příznaku k zabránění více souběžných operací:

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

  Umístěte operace za `Loading = true;` řádek v `try` bloku.

* U delších operací, které využijí [sledování změn](/ef/core/querying/tracking) nebo [řízení souběžnosti](/ef/core/saving/concurrency)EF Core, nastavte kontext na životní [dobu součásti](#scope-to-the-component-lifetime-5x).

<h3 id="new-dbcontext-instances-5x">Nové instance DbContext</h3>

Nejrychlejší způsob, jak vytvořit novou <xref:Microsoft.EntityFrameworkCore.DbContext> instanci, je použít `new` k vytvoření nové instance. Existuje však několik scénářů, které mohou vyžadovat řešení dalších závislostí. Například lze chtít použít [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) ke konfiguraci kontextu.

Doporučeným řešením pro vytvoření nového <xref:Microsoft.EntityFrameworkCore.DbContext> se závislostmi je použití továrny. EF Core 5,0 nebo novější poskytuje integrovaný objekt pro vytváření nových kontextů.

Následující příklad konfiguruje [SQLite](https://www.sqlite.org/index.html) a povoluje protokolování dat. Kód používá metodu rozšíření ke konfiguraci objektu pro vytváření databáze pro DI a poskytuje výchozí možnosti:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Továrna je vložena do komponent a slouží k vytváření nových instancí. Například v `Pages/Index.razor` :

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` je [odkaz na komponentu](xref:blazor/components/index#capture-references-to-components) `GridWrapper` komponenty. Viz `Index` součást ( `Pages/Index.razor` ) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

<h3 id="scope-to-the-component-lifetime-5x">Rozsah pro životní dobu součásti</h3>

Možná budete chtít vytvořit <xref:Microsoft.EntityFrameworkCore.DbContext> , který existuje pro celou dobu platnosti komponenty. To vám umožní použít ho jako [pracovní jednotku](https://martinfowler.com/eaaCatalog/unitOfWork.html) a využít vestavěné funkce, jako je sledování změn a řešení souběžnosti.
Pomocí továrny můžete vytvořit kontext a sledovat ho po dobu života součásti. Nejprve implementujte <xref:System.IDisposable> a založit objekt pro vytváření, jak je znázorněno v `Pages/EditContact.razor` následujícím příkladu:

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Ukázková aplikace zajišťuje, že je kontext vyřazený, když je součást Vyřazená:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Nakonec [`OnInitializedAsync`](xref:blazor/components/lifecycle) je přepsáno, aby se vytvořil nový kontext. V ukázkové aplikaci [`OnInitializedAsync`](xref:blazor/components/lifecycle) načte kontakt ve stejné metodě:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server je stavová architektura aplikace. Aplikace udržuje průběžné připojení k serveru a stav uživatele je uložený v paměti serveru v *okruhu*. Jedním z příkladů stavu uživatele jsou data uchovávaná v instancích služby [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , které jsou vymezeny pro okruh. Jedinečný aplikační model, který Blazor Server poskytuje, vyžaduje zvláštní přístup k použití Entity Framework Core.

> [!NOTE]
> Tento článek se zabývá EF Core v Blazor Server aplikacích. Blazor WebAssembly aplikace běží v izolovaném prostoru (sandbox) websestavení, které brání většině přímých připojení k databázi. Spuštění EF Core v systému Blazor WebAssembly je nad rámec tohoto článku.

<h2 id="sample-app-3x">Ukázková aplikace</h2>

Ukázková aplikace byla sestavena jako referenční dokumentace pro Blazor Server aplikace, které používají EF Core. Ukázková aplikace obsahuje mřížku s operacemi řazení a filtrování, odstraňování, přidávání a aktualizace. Ukázka ukazuje použití EF Core ke zpracování optimistické souběžnosti.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázka používá místní databázi [SQLite](https://www.sqlite.org/index.html) , aby ji bylo možné použít na libovolné platformě. Ukázka také nakonfiguruje protokolování databáze tak, aby zobrazovalo vygenerované dotazy SQL. Nakonfiguruje se v `appsettings.Development.json` :

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Komponenty mřížka, přidání a zobrazení používají vzor "kontext pro operaci", ve kterém je vytvořen kontext pro každou operaci. Součást pro úpravy používá vzor "kontext pro komponentu", kde je vytvořen kontext pro každou součást.

> [!NOTE]
> Některé příklady kódu v tomto tématu vyžadují obory názvů a služby, které se nezobrazují. Pokud si chcete prohlédnout plně funkční kód, včetně požadavků [`@using`](xref:mvc/views/razor#using) a [`@inject`](xref:mvc/views/razor#inject) direktiv pro Razor Příklady, přečtěte si [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).

<h2 id="database-access-3x">Přístup k databázi</h2>

EF Core spoléhá na a <xref:Microsoft.EntityFrameworkCore.DbContext> jako způsob [Konfigurace přístupu k databázi](/ef/core/miscellaneous/configuring-dbcontext) a fungování jako [*pracovní jednotky*](https://martinfowler.com/eaaCatalog/unitOfWork.html). EF Core poskytuje <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> rozšíření pro ASP.NET Core aplikace, které ve výchozím nastavení registrují kontext jako službu s *vymezeným oborem* . V Blazor Server aplikacích může to být problematické, protože instance je sdílená napříč součástmi v rámci okruhu uživatele. <xref:Microsoft.EntityFrameworkCore.DbContext> není bezpečné pro přístup z více vláken a není navržené pro souběžné použití. Stávající životnosti jsou z těchto důvodů nevhodná:

* **Singleton** sdílí stav napříč všemi uživateli aplikace a vede k nevhodnému souběžnému použití.
* **Obor** (výchozí) představuje podobný problém mezi komponentami stejného uživatele.
* **Přechodný** výsledek nové instance na žádost; avšak protože komponenty mohou být dlouhodobě dlouhodobé, výsledkem je delší kontext, než může být určena.

Následující doporučení jsou navržená tak, aby poskytovala konzistentní přístup k používání EF Core v Blazor Server aplikacích.

* Ve výchozím nastavení zvažte použití jednoho kontextu na operaci. Kontext je navržen pro rychlou a nízkou režii vytváření instancí:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Použití příznaku k zabránění více souběžných operací:

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

  Umístěte operace za `Loading = true;` řádek v `try` bloku.

* U delších operací, které využijí [sledování změn](/ef/core/querying/tracking) nebo [řízení souběžnosti](/ef/core/saving/concurrency)EF Core, nastavte kontext na životní [dobu součásti](#scope-to-the-component-lifetime-3x).

<h3 id="new-dbcontext-instances-3x">Nové instance DbContext</h3>

Nejrychlejší způsob, jak vytvořit novou <xref:Microsoft.EntityFrameworkCore.DbContext> instanci, je použít `new` k vytvoření nové instance. Existuje však několik scénářů, které mohou vyžadovat řešení dalších závislostí. Například lze chtít použít [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) ke konfiguraci kontextu.

Doporučeným řešením pro vytvoření nového <xref:Microsoft.EntityFrameworkCore.DbContext> se závislostmi je použití továrny. Ukázková aplikace implementuje svou vlastní továrnu v nástroji `Data/DbContextFactory.cs` .

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

V předchozím objektu pro vytváření:

* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> splňuje všechny závislosti prostřednictvím poskytovatele služeb.
* `IDbContextFactory` je k dispozici v EF Core ASP.NET Core 5,0 nebo novějším, takže rozhraní je [implementováno v ukázkové aplikaci pro ASP.NET Core 3. x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).

Následující příklad konfiguruje [SQLite](https://www.sqlite.org/index.html) a povoluje protokolování dat. Kód používá metodu rozšíření ke konfiguraci objektu pro vytváření databáze pro DI a poskytuje výchozí možnosti:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Továrna je vložena do komponent a slouží k vytváření nových instancí. Například v `Pages/Index.razor` :

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` je [odkaz na komponentu](xref:blazor/components/index#capture-references-to-components) `GridWrapper` komponenty. Viz `Index` součást ( `Pages/Index.razor` ) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).

<h3 id="scope-to-the-component-lifetime-3x">Rozsah pro životní dobu součásti</h3>

Možná budete chtít vytvořit <xref:Microsoft.EntityFrameworkCore.DbContext> , který existuje pro celou dobu platnosti komponenty. To vám umožní použít ho jako [pracovní jednotku](https://martinfowler.com/eaaCatalog/unitOfWork.html) a využít vestavěné funkce, jako je sledování změn a řešení souběžnosti.
Pomocí továrny můžete vytvořit kontext a sledovat ho po dobu života součásti. Nejprve implementujte <xref:System.IDisposable> a založit objekt pro vytváření, jak je znázorněno v `Pages/EditContact.razor` následujícím příkladu:

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Ukázková aplikace zajišťuje, že je kontext vyřazený, když je součást Vyřazená:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Nakonec [`OnInitializedAsync`](xref:blazor/components/lifecycle) je přepsáno, aby se vytvořil nový kontext. V ukázkové aplikaci [`OnInitializedAsync`](xref:blazor/components/lifecycle) načte kontakt ve stejné metodě:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

V předchozím příkladu:

* Když `Busy` je nastaveno na `true` , mohou být zahájeny asynchronní operace. Když `Busy` je nastaveno zpět na `false` , by měly být dokončeny asynchronní operace.
* Do bloku umístěte další logiku zpracování chyb `catch` .

:::moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Dokumentace k EF Core](/ef/)
