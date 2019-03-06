---
title: Distribuované ukládání do mezipaměti v ASP.NET Core
author: guardrex
description: Další informace o použití ASP.NET Core distribuované mezipaměti ke zlepšení výkonu aplikací a škálovatelnost, obzvláště v prostředí cloudu nebo serveru farmy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2019
uid: performance/caching/distributed
ms.openlocfilehash: a7850e317dfa3b54f1980902b3dcd6b096effa15
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346116"
---
# <a name="distributed-caching-in-aspnet-core"></a>Distribuované ukládání do mezipaměti v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex) a [Steve Smith](https://ardalis.com/)

Distribuované mezipaměti je mezipaměť sdílených více serverů aplikace, obvykle nakládat jako s externí služby na serverech aplikace, které k němu přístup. Distribuované mezipaměti může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostovaná v cloudové službě nebo v serverové farmě.

Distribuované mezipaměti má několik výhod oproti jiné scénáře ukládání do mezipaměti ukládat data uložená v mezipaměti na serverech jednotlivých aplikací.

Když se distribuuje data uložená v mezipaměti, data:

* Je *přeměnit* (konzistentní) v rámci celého žádosti více servery.
* Odolává server se restartuje a nasazení aplikací.
* Nepoužívá místní paměti.

Konfigurace distribuované mezipaměti závisí na konkrétní implementaci. Tento článek popisuje, jak nakonfigurovat SQL Server a distribuované mezipaměti Redis. Implementace třetích stran jsou také k dispozici, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na Githubu](https://github.com/Alachisoft/NCache)). Bez ohledu na to, kterou implementaci je vybrána, aplikace komunikuje s potřebnou mezipaměť <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

::: moniker range=">= aspnetcore-2.2"

Použití SQL serveru distribuovaná mezipaměť, odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček pro [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) balíčku.

Použití Redis distribuovaná mezipaměť, odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) balíčku. Není součástí balíčku Redis `Microsoft.AspNetCore.App` balíček, takže musí odkazujte na balíček Redis samostatně v souboru projektu.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Použití SQL serveru distribuovaná mezipaměť, odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček pro [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) balíčku.

Použití Redis distribuovaná mezipaměť, odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) balíčku. Není součástí balíčku Redis `Microsoft.AspNetCore.App` balíček, takže musí odkazujte na balíček Redis samostatně v souboru projektu.

::: moniker-end

## <a name="idistributedcache-interface"></a>IDistributedCache rozhraní

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Rozhraní poskytuje následující metody k manipulaci s položkami v distribuované mezipaměti implementace:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Přijímá s klíčem řetězce a načte položku v mezipaměti jako `byte[]` pole, pokud v mezipaměti nalezen.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Přidá položku (jako `byte[]` pole) do mezipaměti pomocí řetězce klíče.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Aktualizuje položku v mezipaměti na základě jeho klíče resetuje jeho časový limit klouzavé vypršení platnosti (pokud existuje).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Odebere položku mezipaměti na základě jeho řetězec klíče.

## <a name="establish-distributed-caching-services"></a>Vytvoření distribuované ukládání do mezipaměti služby

Zaregistrujte implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices`. Mezi poskytované rozhraním implementace, které jsou popsané v tomto tématu patří:

* [Distribuované mezipaměti](#distributed-memory-cache)
* [Distribuované mezipaměti serveru SQL Server](#distributed-sql-server-cache)
* [Distribuované mezipaměti Redis](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a>Distribuované mezipaměti

Distribuované mezipaměti (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) je implementace poskytované rozhraním <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , který ukládá položky v paměti. Distribuované mezipaměti není skutečný distribuované mezipaměti. Položek v mezipaměti jsou uloženy v instanci aplikace na serveru, kde je aplikace spuštěna.

Distribuované mezipaměti je užitečné implementace:

* V vývojové a testovací scénáře.
* Pokud používáte jeden server v produkční a paměti spotřeby není problém. Implementace distribuované mezipaměti přehledů v mezipaměti datové úložiště. To umožňuje pro implementaci že hodnotu true distribuované řešení ukládání do mezipaměti v budoucích Pokud více uzly nebo nezbytné odolnost proti chybám.

Ukázková aplikace využívá distribuované mezipaměti při spuštění aplikace ve vývojovém prostředí v `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Mezipaměť distribuovaná SQL serveru

Implementace distribuované mezipaměti serveru SQL (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) umožňuje distribuované mezipaměti pro použití jiné databáze systému SQL Server jako svůj záložní úložiště. Chcete-li vytvořit tabulku položky uložené v mezipaměti serveru SQL Server v instanci systému SQL Server, můžete použít `sql-cache` nástroj. Nástroj vytvoří tabulku s názvem a schéma, které zadáte.

Vytvoření tabulky v SQL serveru spuštěním `sql-cache create` příkazu. Zadejte instanci systému SQL Server (`Data Source`), database (`Initial Catalog`), schéma (například `dbo`) a název tabulky (například `TestCache`):

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Bude zaznamenána zpráva označující, že nástroj byla úspěšná:

```console
Table and index were created successfully.
```

Tabulky vytvořené `sql-cache` nástroj má následující schéma:

![Tabulka mezipaměti systému SQL Server](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Aplikace by měla manipulaci s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, nikoli <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.

Implementuje ukázkové aplikace <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v mimo vývojové prostředí v `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (a volitelně také <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) jsou obvykle uložená mimo správy zdrojového kódu (například uložené [manažera tajných](xref:security/app-secrets) nebo v *appsettings.json* / *appsettings. {PROSTŘEDÍ} .json* soubory). Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být neustále mimo systémy správy zdrojového kódu.

### <a name="distributed-redis-cache"></a>Distribuované Redis Cache

[Redis](https://redis.io/) je úložiště otevřít zdroj dat v paměti, což se často používá jako distribuované mezipaměti. Redis může používat místně, a můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikace ASP.NET Core hostovaných v Azure.

::: moniker range=">= aspnetcore-2.2"

Aplikace nastaví implementaci mezipaměti pomocí `RedisCache` instance (`AddStackExchangeRedisCache`) v prostředí pro vývoj v `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aplikace nastaví implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

Chcete-li nainstalovat Redis na místním počítači:

* Nainstalujte [Chocolatey Redis balíčku](https://chocolatey.org/packages/redis-64/).
* Spustit `redis-server` z příkazového řádku.

## <a name="use-the-distributed-cache"></a>Pomocí distribuované mezipaměti

Použít <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní, požádat o instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z jakéhokoli konstruktoru v aplikaci. Instance je poskytován [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).

Při spuštění aplikace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> se vloží do `Startup.Configure`. Aktuální čas se uloží do mezipaměti pomocí <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Další informace najdete v tématu [webového hostitele: Rozhraní IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Vloží ukázková aplikace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` používají indexovou stránku.

Pokaždé, když je načten indexovou stránku, do mezipaměti se kontroluje u mezipaměti čas v `OnGetAsync`. Pokud v mezipaměti Doba nevypršela, zobrazí se čas. Pokud 20 sekund uplynuly od posledního času v mezipaměti se použila (poslední čas na této stránce byl načten), na stránce se zobrazí *uložené v mezipaměti časový limit vypršel*.

Okamžitě aktualizovat uložené v mezipaměti Doba na aktuální čas tak, že vyberete **obnovit uložený v mezipaměti Doba** tlačítko. Aktivacemi tlačítek `OnPostResetCachedTime` metodu obslužné rutiny.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Není nutné používat jednotlivý prvek nebo rozsah doba života pro <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance (nejméně pro předdefinované implementace).
>
> Můžete také vytvořit <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance bez ohledu na to může být nutné jednu namísto použití DI, ale vytvoření instance v kódu můžou znesnadnit kódu pro testování a je v rozporu [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Doporučení

Při rozhodování o tom, která implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je nejvhodnější pro vaši aplikaci, vezměte v úvahu následující:

* Stávající infrastruktury
* Požadavky na výkon
* Náklady
* Týmových zkušeností

Řešení ukládání do mezipaměti obvykle využívají úložiště v paměti zajistit rychlé načítání dat uložených v mezipaměti paměti je však omezená prostředků a nákladná rozbalte. Pouze úložiště používaných dat v mezipaměti.

Obecně platí Redis cache poskytuje vyšší propustnost a nižší latenci než mezipaměti serveru SQL Server. Srovnávací testy je však obvykle vyžaduje k určení výkonové charakteristiky strategií ukládání do mezipaměti.

Pokud SQL Server slouží jako záložní úložiště distribuované mezipaměti, použijte stejné databáze pro mezipaměť a aplikace běžné datové úložiště a načtení může mít negativní vliv na výkon obou. Doporučujeme použít vyhrazenou instanci systému SQL Server pro distribuované mezipaměti záložní úložiště.

## <a name="additional-resources"></a>Další zdroje

* [Redis Cache v Azure](/azure/azure-cache-for-redis/)
* [SQL Database v Azure](/azure/sql-database/)
* [ASP.NET Core IDistributedCache zprostředkovatele pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na Githubu](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
