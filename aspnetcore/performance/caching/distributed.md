---
title: Distribuované ukládání do mezipaměti v ASP.NET Core
author: rick-anderson
description: Naučte se používat ASP.NET Core distribuovanou mezipaměť pro zlepšení výkonu a škálovatelnosti aplikace, zejména v prostředí cloudové nebo serverové farmy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 0d27206412a098f4ea749ec10189bf24d2322de1
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712477"
---
# <a name="distributed-caching-in-aspnet-core"></a>Distribuované ukládání do mezipaměti v ASP.NET Core

Od [Mohsin Nasir](https://github.com/mohsinnasir) a [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

Distribuovaná mezipaměť je mezipaměť, kterou sdílí víc aplikačních serverů, obvykle se udržuje jako externí služba pro aplikační servery, které k ní přistupují. Distribuovaná mezipaměť může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostována cloudovou službou nebo serverovou farmou.

Distribuovaná mezipaměť má několik výhod oproti jiným scénářům ukládání do mezipaměti, kdy se data uložená v mezipaměti ukládají na jednotlivé aplikační servery.

Když jsou data v mezipaměti distribuována, data:

* Je *souvislý* (konzistentní) mezi požadavky na více serverů.
* Zachová se restarty serveru a nasazení aplikací.
* Nepoužívá místní paměť.

Konfigurace distribuované mezipaměti je specifická pro implementaci. Tento článek popisuje, jak nakonfigurovat SQL Server a Redis distribuované mezipaměti. K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache)). Bez ohledu na to, která implementace je vybraná, aplikace komunikuje s mezipamětí pomocí <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Předpoklady

Pokud chcete použít SQL Server distribuovanou mezipaměť, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Chcete-li použít distribuovanou mezipaměť Redis, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .

Pokud chcete použít distribuovanou mezipaměť NCache, přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .

## <a name="idistributedcache-interface"></a>Rozhraní IDistributedCache

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Rozhraní poskytuje následující metody pro manipulaci s položkami v implementaci distribuované mezipaměti:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Přijme klíč řetězce a načte položku uloženou v mezipaměti jako `byte[]` pole, pokud je v mezipaměti nalezeno.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Přidá položku (jako `byte[]` pole) do mezipaměti pomocí klíče řetězce.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualizuje položku v mezipaměti na základě jejího klíče a resetuje časový limit klouzavého vypršení platnosti (pokud existuje).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Odebere položku mezipaměti na základě jejího řetězcového klíče.

## <a name="establish-distributed-caching-services"></a>Vytvoření služby distribuované mezipaměti

Zaregistrujte implementaci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices` . Implementace implementované v tomto tématu zahrnuje:

* [Mezipaměť distribuované paměti](#distributed-memory-cache)
* [Distribuovaná mezipaměť SQL Server](#distributed-sql-server-cache)
* [Distribuovaná mezipaměť Redis](#distributed-redis-cache)
* [Distribuovaná mezipaměť NCache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Mezipaměť distribuované paměti

Mezipaměť distribuované paměti ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) je implementací rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , které ukládá položky v paměti. Mezipaměť distribuované paměti není skutečnou distribuovanou mezipamětí. Položky uložené v mezipaměti jsou uloženy instancí aplikace na serveru, na kterém je aplikace spuštěná.

Mezipaměť distribuované paměti je užitečnou implementací:

* Ve scénářích vývoje a testování.
* Pokud se jeden server používá v produkčním prostředí a spotřeba paměti není problém. Implementace vydaných mezipamětí mezipaměti úložiště dat v mezipaměti. Umožňuje implementaci skutečného distribuovaného řešení pro ukládání do mezipaměti v budoucnu, pokud je potřeba několik uzlů nebo odolnost proti chybám.

Ukázková aplikace využívá mezipaměť distribuované paměti při spuštění aplikace ve vývojovém prostředí v nástroji `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Distribuovaná mezipaměť SQL Server

Distributed SQL Server cache Implementation ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umožňuje distribuované mezipaměti používat jako záložní úložiště databázi SQL Server. Chcete-li vytvořit tabulku položek SQL Server v mezipaměti v instanci SQL Server, můžete použít `sql-cache` nástroj. Nástroj vytvoří tabulku se zadaným názvem a schématem.

Vytvořte tabulku v SQL Server spuštěním `sql-cache create` příkazu. Zadejte instanci SQL Server ( `Data Source` ), databázi ( `Initial Catalog` ), schéma (například `dbo` ) a název tabulky (například `TestCache` ):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Zpráva, která označuje, že nástroj byl úspěšný, je zaznamenána do protokolu:

```console
Table and index were created successfully.
```

Tabulka vytvořená `sql-cache` nástrojem má následující schéma:

![Tabulka mezipaměti SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Aplikace by měla manipulovat s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , nikoli <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Ukázková aplikace implementuje <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v prostředí bez vývoje `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (a volitelně <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> i <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se obvykle ukládají mimo správu zdrojového kódu (například uložené správcem [tajných klíčů](xref:security/app-secrets) nebo v *appsettings.js*v / *appSettings. { ENVIRONMENT}. JSON* soubory. Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být zachovány ze systémů správy zdrojového kódu.

### <a name="distributed-redis-cache"></a>Distribuované Redis Cache

[Redis](https://redis.io/) je open source úložiště dat v paměti, které se často používá jako distribuovaná mezipaměť.  Můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikaci hostovanou v Azure ASP.NET Core a použít Azure Redis Cache pro místní vývoj.

Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ).

Další informace najdete v článku, který se věnuje [službě Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

Diskuzi o alternativních přístupech k místní mezipaměti Redis najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/19542) .

### <a name="distributed-ncache-cache"></a>Distribuovaná mezipaměť NCache

[NCache](https://github.com/Alachisoft/NCache) je open source distribuovaná mezipaměť v paměti vyvinutá nativně v rozhraní .NET a .NET Core. NCache funguje místně i nakonfigurovaná jako cluster distribuovaných mezipamětí pro ASP.NET Core aplikaci běžící v Azure nebo na jiných hostujících platformách.

Pokud chcete nainstalovat a nakonfigurovat NCache na místním počítači, přečtěte si téma [průvodce Začínáme NCache pro Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Konfigurace NCache:

1. Nainstalujte [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Nakonfigurujte cluster mezipaměti v [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Do souboru `Startup.ConfigureServices` přidejte následující kód:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Použít distribuovanou mezipaměť

Chcete-li použít <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní, vyžádejte si instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z libovolného konstruktoru v aplikaci. Instance je poskytována [vložením závislosti (di)](xref:fundamentals/dependency-injection).

Po spuštění ukázkové aplikace je vložena <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `Startup.Configure` . Aktuální čas je uložen v mezipaměti pomocí <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (Další informace najdete v tématu [Generic host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Ukázková aplikace se vloží <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` pro použití stránkou indexu.

Pokaždé, když je načtena stránka indexu, je v mezipaměti kontrolována doba uložená v mezipaměti `OnGetAsync` . Pokud doba v mezipaměti nevypršela, zobrazí se čas. Pokud uplynula 20 sekund od poslední posledního přistupu k době ukládání do mezipaměti (při poslední načtení této stránky), stránka zobrazuje časový limit *v mezipaměti*.

Okamžitě aktualizovat čas uložený v mezipaměti na aktuální čas výběrem tlačítka **obnovit čas v mezipaměti** . Tlačítko aktivuje `OnPostResetCachedTime` metodu obslužné rutiny.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Pro instance není nutné používat singleton nebo vymezenou dobu života <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (alespoň pro předdefinované implementace).
>
> Můžete také vytvořit <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instanci, kdykoli budete možná potřebovat místo použití di, ale vytvoření instance v kódu může ztížit testování a porušování [explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Doporučení

Při rozhodování, která implementace nástroje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je pro vaši aplikaci nejvhodnější, vezměte v úvahu následující skutečnosti:

* Stávající infrastruktura
* Požadavky na výkon
* Cost
* Prostředí týmu

Řešení ukládání do mezipaměti obvykle spoléhá na úložiště v paměti, které poskytuje rychlé načítání dat uložených v mezipaměti, ale paměť je omezeného prostředku a je nákladná k rozšíření. Ukládat běžně používaná data do mezipaměti.

Obecně platí, že mezipaměť Redis poskytuje vyšší propustnost a nižší latenci než mezipaměť SQL Server. Srovnávací testy se ale obvykle vyžadují k určení charakteristik výkonu pro strategie ukládání do mezipaměti.

Když se SQL Server používá jako úložiště zálohování distribuované mezipaměti, použití stejné databáze pro mezipaměť a běžné ukládání a načítání dat aplikace může negativně ovlivnit výkon obou. Pro úložiště záloh distribuované mezipaměti doporučujeme použít vyhrazenou instanci SQL Server.

## <a name="additional-resources"></a>Další zdroje

* [Redis Cache v Azure](/azure/azure-cache-for-redis/)
* [Databáze SQL v Azure](/azure/sql-database/)
* [ASP.NET Core poskytovatel IDistributedCache pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Distribuovaná mezipaměť je mezipaměť, kterou sdílí víc aplikačních serverů, obvykle se udržuje jako externí služba pro aplikační servery, které k ní přistupují. Distribuovaná mezipaměť může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostována cloudovou službou nebo serverovou farmou.

Distribuovaná mezipaměť má několik výhod oproti jiným scénářům ukládání do mezipaměti, kdy se data uložená v mezipaměti ukládají na jednotlivé aplikační servery.

Když jsou data v mezipaměti distribuována, data:

* Je *souvislý* (konzistentní) mezi požadavky na více serverů.
* Zachová se restarty serveru a nasazení aplikací.
* Nepoužívá místní paměť.

Konfigurace distribuované mezipaměti je specifická pro implementaci. Tento článek popisuje, jak nakonfigurovat SQL Server a Redis distribuované mezipaměti. K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache)). Bez ohledu na to, která implementace je vybraná, aplikace komunikuje s mezipamětí pomocí <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Předpoklady

Pokud chcete použít SQL Server distribuovanou mezipaměť, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Pokud chcete použít distribuovanou mezipaměť Redis, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) . Balíček Redis není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček Redis samostatně v souboru projektu.

Pokud chcete použít distribuovanou mezipaměť NCache, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . Balíček NCache není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček NCache samostatně v souboru projektu.

## <a name="idistributedcache-interface"></a>Rozhraní IDistributedCache

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Rozhraní poskytuje následující metody pro manipulaci s položkami v implementaci distribuované mezipaměti:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Přijme klíč řetězce a načte položku uloženou v mezipaměti jako `byte[]` pole, pokud je v mezipaměti nalezeno.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Přidá položku (jako `byte[]` pole) do mezipaměti pomocí klíče řetězce.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualizuje položku v mezipaměti na základě jejího klíče a resetuje časový limit klouzavého vypršení platnosti (pokud existuje).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Odebere položku mezipaměti na základě jejího řetězcového klíče.

## <a name="establish-distributed-caching-services"></a>Vytvoření služby distribuované mezipaměti

Zaregistrujte implementaci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices` . Implementace implementované v tomto tématu zahrnuje:

* [Mezipaměť distribuované paměti](#distributed-memory-cache)
* [Distribuovaná mezipaměť SQL Server](#distributed-sql-server-cache)
* [Distribuovaná mezipaměť Redis](#distributed-redis-cache)
* [Distribuovaná mezipaměť NCache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Mezipaměť distribuované paměti

Mezipaměť distribuované paměti ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) je implementací rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , které ukládá položky v paměti. Mezipaměť distribuované paměti není skutečnou distribuovanou mezipamětí. Položky uložené v mezipaměti jsou uloženy instancí aplikace na serveru, na kterém je aplikace spuštěná.

Mezipaměť distribuované paměti je užitečnou implementací:

* Ve scénářích vývoje a testování.
* Pokud se jeden server používá v produkčním prostředí a spotřeba paměti není problém. Implementace vydaných mezipamětí mezipaměti úložiště dat v mezipaměti. Umožňuje implementaci skutečného distribuovaného řešení pro ukládání do mezipaměti v budoucnu, pokud je potřeba několik uzlů nebo odolnost proti chybám.

Ukázková aplikace využívá mezipaměť distribuované paměti při spuštění aplikace ve vývojovém prostředí v nástroji `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Distribuovaná mezipaměť SQL Server

Distributed SQL Server cache Implementation ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umožňuje distribuované mezipaměti používat jako záložní úložiště databázi SQL Server. Chcete-li vytvořit tabulku položek SQL Server v mezipaměti v instanci SQL Server, můžete použít `sql-cache` nástroj. Nástroj vytvoří tabulku se zadaným názvem a schématem.

Vytvořte tabulku v SQL Server spuštěním `sql-cache create` příkazu. Zadejte instanci SQL Server ( `Data Source` ), databázi ( `Initial Catalog` ), schéma (například `dbo` ) a název tabulky (například `TestCache` ):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Zpráva, která označuje, že nástroj byl úspěšný, je zaznamenána do protokolu:

```console
Table and index were created successfully.
```

Tabulka vytvořená `sql-cache` nástrojem má následující schéma:

![Tabulka mezipaměti SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Aplikace by měla manipulovat s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , nikoli <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Ukázková aplikace implementuje <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v prostředí bez vývoje `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (a volitelně <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> i <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se obvykle ukládají mimo správu zdrojového kódu (například uložené správcem [tajných klíčů](xref:security/app-secrets) nebo v *appsettings.js*v / *appSettings. { ENVIRONMENT}. JSON* soubory. Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být zachovány ze systémů správy zdrojového kódu.

### <a name="distributed-redis-cache"></a>Distribuované Redis Cache

[Redis](https://redis.io/) je open source úložiště dat v paměti, které se často používá jako distribuovaná mezipaměť. Redis můžete použít místně a můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikace ASP.NET Core hostované pro Azure.

Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) v nevývojovém prostředí v `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Instalace Redis na místní počítač:

1. Nainstalujte [balíček Redis pro čokolády](https://chocolatey.org/packages/redis-64/).
1. Spusťte `redis-server` z příkazového řádku.

### <a name="distributed-ncache-cache"></a>Distribuovaná mezipaměť NCache

[NCache](https://github.com/Alachisoft/NCache) je open source distribuovaná mezipaměť v paměti vyvinutá nativně v rozhraní .NET a .NET Core. NCache funguje místně i nakonfigurovaná jako cluster distribuovaných mezipamětí pro ASP.NET Core aplikaci běžící v Azure nebo na jiných hostujících platformách.

Pokud chcete nainstalovat a nakonfigurovat NCache na místním počítači, přečtěte si téma [průvodce Začínáme NCache pro Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Konfigurace NCache:

1. Nainstalujte [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Nakonfigurujte cluster mezipaměti v [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Do souboru `Startup.ConfigureServices` přidejte následující kód:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Použít distribuovanou mezipaměť

Chcete-li použít <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní, vyžádejte si instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z libovolného konstruktoru v aplikaci. Instance je poskytována [vložením závislosti (di)](xref:fundamentals/dependency-injection).

Po spuštění ukázkové aplikace je vložena <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `Startup.Configure` . Aktuální čas je uložen v mezipaměti s použitím <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Další informace najdete v tématu [webový hostitel: rozhraní IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Ukázková aplikace se vloží <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` pro použití stránkou indexu.

Pokaždé, když je načtena stránka indexu, je v mezipaměti kontrolována doba uložená v mezipaměti `OnGetAsync` . Pokud doba v mezipaměti nevypršela, zobrazí se čas. Pokud uplynula 20 sekund od poslední posledního přistupu k době ukládání do mezipaměti (při poslední načtení této stránky), stránka zobrazuje časový limit *v mezipaměti*.

Okamžitě aktualizovat čas uložený v mezipaměti na aktuální čas výběrem tlačítka **obnovit čas v mezipaměti** . Tlačítko aktivuje `OnPostResetCachedTime` metodu obslužné rutiny.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Pro instance není nutné používat singleton nebo vymezenou dobu života <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (alespoň pro předdefinované implementace).
>
> Můžete také vytvořit <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instanci, kdykoli budete možná potřebovat místo použití di, ale vytvoření instance v kódu může ztížit testování a porušování [explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Doporučení

Při rozhodování, která implementace nástroje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je pro vaši aplikaci nejvhodnější, vezměte v úvahu následující skutečnosti:

* Stávající infrastruktura
* Požadavky na výkon
* Cost
* Prostředí týmu

Řešení ukládání do mezipaměti obvykle spoléhá na úložiště v paměti, které poskytuje rychlé načítání dat uložených v mezipaměti, ale paměť je omezeného prostředku a je nákladná k rozšíření. Ukládat běžně používaná data do mezipaměti.

Obecně platí, že mezipaměť Redis poskytuje vyšší propustnost a nižší latenci než mezipaměť SQL Server. Srovnávací testy se ale obvykle vyžadují k určení charakteristik výkonu pro strategie ukládání do mezipaměti.

Když se SQL Server používá jako úložiště zálohování distribuované mezipaměti, použití stejné databáze pro mezipaměť a běžné ukládání a načítání dat aplikace může negativně ovlivnit výkon obou. Pro úložiště záloh distribuované mezipaměti doporučujeme použít vyhrazenou instanci SQL Server.

## <a name="additional-resources"></a>Další zdroje

* [Redis Cache v Azure](/azure/azure-cache-for-redis/)
* [Databáze SQL v Azure](/azure/sql-database/)
* [ASP.NET Core poskytovatel IDistributedCache pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Distribuovaná mezipaměť je mezipaměť, kterou sdílí víc aplikačních serverů, obvykle se udržuje jako externí služba pro aplikační servery, které k ní přistupují. Distribuovaná mezipaměť může zlepšit výkon a škálovatelnost aplikace ASP.NET Core, zejména v případě, že je aplikace hostována cloudovou službou nebo serverovou farmou.

Distribuovaná mezipaměť má několik výhod oproti jiným scénářům ukládání do mezipaměti, kdy se data uložená v mezipaměti ukládají na jednotlivé aplikační servery.

Když jsou data v mezipaměti distribuována, data:

* Je *souvislý* (konzistentní) mezi požadavky na více serverů.
* Zachová se restarty serveru a nasazení aplikací.
* Nepoužívá místní paměť.

Konfigurace distribuované mezipaměti je specifická pro implementaci. Tento článek popisuje, jak nakonfigurovat SQL Server a Redis distribuované mezipaměti. K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache)). Bez ohledu na to, která implementace je vybraná, aplikace komunikuje s mezipamětí pomocí <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Předpoklady

Pokud chcete použít SQL Server distribuovanou mezipaměť, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Pokud chcete použít distribuovanou mezipaměť Redis, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) . Balíček Redis není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček Redis samostatně v souboru projektu.

Pokud chcete použít distribuovanou mezipaměť NCache, odkazujte na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a přidejte odkaz na balíček do balíčku [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) . Balíček NCache není součástí `Microsoft.AspNetCore.App` balíčku, takže musíte odkazovat na balíček NCache samostatně v souboru projektu.

## <a name="idistributedcache-interface"></a>Rozhraní IDistributedCache

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Rozhraní poskytuje následující metody pro manipulaci s položkami v implementaci distribuované mezipaměti:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Přijme klíč řetězce a načte položku uloženou v mezipaměti jako `byte[]` pole, pokud je v mezipaměti nalezeno.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Přidá položku (jako `byte[]` pole) do mezipaměti pomocí klíče řetězce.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualizuje položku v mezipaměti na základě jejího klíče a resetuje časový limit klouzavého vypršení platnosti (pokud existuje).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Odebere položku mezipaměti na základě jejího řetězcového klíče.

## <a name="establish-distributed-caching-services"></a>Vytvoření služby distribuované mezipaměti

Zaregistrujte implementaci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> v `Startup.ConfigureServices` . Implementace implementované v tomto tématu zahrnuje:

* [Mezipaměť distribuované paměti](#distributed-memory-cache)
* [Distribuovaná mezipaměť SQL Server](#distributed-sql-server-cache)
* [Distribuovaná mezipaměť Redis](#distributed-redis-cache)
* [Distribuovaná mezipaměť NCache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Mezipaměť distribuované paměti

Mezipaměť distribuované paměti ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) je implementací rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , které ukládá položky v paměti. Mezipaměť distribuované paměti není skutečnou distribuovanou mezipamětí. Položky uložené v mezipaměti jsou uloženy instancí aplikace na serveru, na kterém je aplikace spuštěná.

Mezipaměť distribuované paměti je užitečnou implementací:

* Ve scénářích vývoje a testování.
* Pokud se jeden server používá v produkčním prostředí a spotřeba paměti není problém. Implementace vydaných mezipamětí mezipaměti úložiště dat v mezipaměti. Umožňuje implementaci skutečného distribuovaného řešení pro ukládání do mezipaměti v budoucnu, pokud je potřeba několik uzlů nebo odolnost proti chybám.

Ukázková aplikace využívá mezipaměť distribuované paměti při spuštění aplikace ve vývojovém prostředí v nástroji `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Distribuovaná mezipaměť SQL Server

Distributed SQL Server cache Implementation ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) umožňuje distribuované mezipaměti používat jako záložní úložiště databázi SQL Server. Chcete-li vytvořit tabulku položek SQL Server v mezipaměti v instanci SQL Server, můžete použít `sql-cache` nástroj. Nástroj vytvoří tabulku se zadaným názvem a schématem.

Vytvořte tabulku v SQL Server spuštěním `sql-cache create` příkazu. Zadejte instanci SQL Server ( `Data Source` ), databázi ( `Initial Catalog` ), schéma (například `dbo` ) a název tabulky (například `TestCache` ):

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Zpráva, která označuje, že nástroj byl úspěšný, je zaznamenána do protokolu:

```console
Table and index were created successfully.
```

Tabulka vytvořená `sql-cache` nástrojem má následující schéma:

![Tabulka mezipaměti SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Aplikace by měla manipulovat s hodnotami mezipaměti pomocí instance <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , nikoli <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Ukázková aplikace implementuje <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> v prostředí bez vývoje `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (a volitelně <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> i <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) se obvykle ukládají mimo správu zdrojového kódu (například uložené správcem [tajných klíčů](xref:security/app-secrets) nebo v *appsettings.js*v / *appSettings. { ENVIRONMENT}. JSON* soubory. Připojovací řetězec může obsahovat přihlašovací údaje, které by měly být zachovány ze systémů správy zdrojového kódu.

### <a name="distributed-redis-cache"></a>Distribuované Redis Cache

[Redis](https://redis.io/) je open source úložiště dat v paměti, které se často používá jako distribuovaná mezipaměť. Redis můžete použít místně a můžete nakonfigurovat [Azure Redis Cache](https://azure.microsoft.com/services/cache/) pro aplikace ASP.NET Core hostované pro Azure.

Aplikace konfiguruje implementaci mezipaměti pomocí <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

Instalace Redis na místní počítač:

1. Nainstalujte [balíček Redis pro čokolády](https://chocolatey.org/packages/redis-64/).
1. Spusťte `redis-server` z příkazového řádku.

### <a name="distributed-ncache-cache"></a>Distribuovaná mezipaměť NCache

[NCache](https://github.com/Alachisoft/NCache) je open source distribuovaná mezipaměť v paměti vyvinutá nativně v rozhraní .NET a .NET Core. NCache funguje místně i nakonfigurovaná jako cluster distribuovaných mezipamětí pro ASP.NET Core aplikaci běžící v Azure nebo na jiných hostujících platformách.

Pokud chcete nainstalovat a nakonfigurovat NCache na místním počítači, přečtěte si téma [průvodce Začínáme NCache pro Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

Konfigurace NCache:

1. Nainstalujte [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Nakonfigurujte cluster mezipaměti v [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).
1. Do souboru `Startup.ConfigureServices` přidejte následující kód:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Použít distribuovanou mezipaměť

Chcete-li použít <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> rozhraní, vyžádejte si instanci <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> z libovolného konstruktoru v aplikaci. Instance je poskytována [vložením závislosti (di)](xref:fundamentals/dependency-injection).

Po spuštění ukázkové aplikace je vložena <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `Startup.Configure` . Aktuální čas je uložen v mezipaměti s použitím <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Další informace najdete v tématu [webový hostitel: rozhraní IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Ukázková aplikace se vloží <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> do `IndexModel` pro použití stránkou indexu.

Pokaždé, když je načtena stránka indexu, je v mezipaměti kontrolována doba uložená v mezipaměti `OnGetAsync` . Pokud doba v mezipaměti nevypršela, zobrazí se čas. Pokud uplynula 20 sekund od poslední posledního přistupu k době ukládání do mezipaměti (při poslední načtení této stránky), stránka zobrazuje časový limit *v mezipaměti*.

Okamžitě aktualizovat čas uložený v mezipaměti na aktuální čas výběrem tlačítka **obnovit čas v mezipaměti** . Tlačítko aktivuje `OnPostResetCachedTime` metodu obslužné rutiny.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Pro instance není nutné používat singleton nebo vymezenou dobu života <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (alespoň pro předdefinované implementace).
>
> Můžete také vytvořit <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instanci, kdykoli budete možná potřebovat místo použití di, ale vytvoření instance v kódu může ztížit testování a porušování [explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Doporučení

Při rozhodování, která implementace nástroje <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je pro vaši aplikaci nejvhodnější, vezměte v úvahu následující skutečnosti:

* Stávající infrastruktura
* Požadavky na výkon
* Cost
* Prostředí týmu

Řešení ukládání do mezipaměti obvykle spoléhá na úložiště v paměti, které poskytuje rychlé načítání dat uložených v mezipaměti, ale paměť je omezeného prostředku a je nákladná k rozšíření. Ukládat běžně používaná data do mezipaměti.

Obecně platí, že mezipaměť Redis poskytuje vyšší propustnost a nižší latenci než mezipaměť SQL Server. Srovnávací testy se ale obvykle vyžadují k určení charakteristik výkonu pro strategie ukládání do mezipaměti.

Když se SQL Server používá jako úložiště zálohování distribuované mezipaměti, použití stejné databáze pro mezipaměť a běžné ukládání a načítání dat aplikace může negativně ovlivnit výkon obou. Pro úložiště záloh distribuované mezipaměti doporučujeme použít vyhrazenou instanci SQL Server.

## <a name="additional-resources"></a>Další zdroje

* [Redis Cache v Azure](/azure/azure-cache-for-redis/)
* [Databáze SQL v Azure](/azure/sql-database/)
* [ASP.NET Core poskytovatel IDistributedCache pro NCache ve webových farmách](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache na GitHubu](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
