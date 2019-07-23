---
title: Začínáme s ASP.NET Core a Entity Framework 6
author: rick-anderson
description: Tento článek ukazuje, jak pomocí Entity Framework 6 v aplikaci ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 50f51a72341cbad017872df581da214211b9920a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900615"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a>Začínáme s ASP.NET Core a Entity Framework 6

Podle [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), a [Petr Dykstra](https://github.com/tdykstra)

Tento článek ukazuje, jak pomocí Entity Framework 6 v aplikaci ASP.NET Core.

## <a name="overview"></a>Přehled

Pokud chcete používat Entity Framework 6, projekt obsahuje kompilovat proti rozhraní .NET Framework jako Entity Framework 6 nepodporuje .NET Core. Pokud potřebujete funkce napříč platformami budete muset upgradovat na [Entity Framework Core](/ef/).

Je doporučeným způsobem, jak pomocí Entity Framework 6 v aplikaci ASP.NET Core do kontextu EF6 a tříd modelu v knihovně tříd projektu, který cílí na úplné rozhraní framework. Přidejte odkaz na knihovnu tříd z projektu ASP.NET Core. Najdete v ukázce [řešení sady Visual Studio s EF6 a ASP.NET Core projekty](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).

Nelze vložit objekt context EF6 v projektu aplikace ASP.NET Core, protože projekty .NET Core nepodporují všechny funkce, která EF6 příkazů, jako *povolení migrace* vyžadují.

Bez ohledu na typ projektu, ve kterém můžete najít váš kontext EF6 pouze nástroje pro příkazový řádek EF6 pracovat EF6 kontextu. Například `Scaffold-DbContext` dostupná jenom v Entity Framework Core. Pokud potřebujete zpětná analýza databáze do modelu EF6, přečtěte si téma [Code First pro existující databázi](https://msdn.microsoft.com/jj200620).

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Odkaz na úplné rozhraní framework a EF6 v projektu ASP.NET Core

Váš projekt ASP.NET Core musí odkazovat na rozhraní .NET framework a EF6. Například *.csproj* bude vypadat podobně jako v následujícím příkladu soubor projektu ASP.NET Core (jsou zobrazeny pouze relevantní části souboru).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

Při vytváření nového projektu, použijte **webová aplikace ASP.NET Core (.NET Framework)** šablony.

## <a name="handle-connection-strings"></a>Popisovač připojovacích řetězců

EF6 nástroje příkazového řádku, které budete používat v projektu knihovny tříd EF6 vyžadují výchozí konstruktor, takže jejich instance kontextu. Ale budete pravděpodobně chtít zadat připojovací řetězec k použití v projektu ASP.NET Core, v takovém případě váš kontext konstruktor musí mít parametr, který umožňuje předat připojovací řetězec. Tady je příklad.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

Vzhledem k tomu, že váš kontext EF6 nemá konstruktor bez parametrů, musí poskytnout implementaci položky projektu EF6 [IDbContextFactory](https://msdn.microsoft.com/library/hh506876). Nástroje příkazového řádku EF6 vyhledá a použít tuto implementaci, takže se můžete vytvořit instanci kontextu. Tady je příklad.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

V tomto ukázkovém kódu `IDbContextFactory` implementace předá pevně zakódovaných propojovacích řetězců. Toto je připojovací řetězec, který bude používat nástroje příkazového řádku. Bude potřeba implementovat strategii Ujistěte se, že knihovna tříd používá stejný připojovací řetězec, který používá volající aplikace. Například může získat hodnotu z proměnné prostředí v obou projektů.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Nastavit injektáž závislostí v projektu ASP.NET Core

V projektu Core *Startup.cs* soubor nastavení EF6 kontext pro injektáž závislostí (DI) `ConfigureServices`. EF kontextové objekty by měly být omezeny na celý život jednotlivých žádostí.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

S použitím DI pak můžete získat instance kontextu ve vašich kontrolerech. Kód se podobá byste napsat pro kontext EF Core:

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>Ukázkové aplikace

Ukázkové aplikace práci, najdete v článku [ukázkové řešení sady Visual Studio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) , který doprovází tento článek.

Tato ukázka je vytvořit úplně od začátku podle následujících kroků v sadě Visual Studio:

* Vytvořte řešení.

* **Přidat** > **nový projekt** > **webové** > **webová aplikace ASP.NET Core**
  * V dialogovém okně Výběr šablony projektu vyberte v rozevíracím seznamu rozhraní API a rozhraní .NET Framework

* **Přidat** > **nový projekt** > **Windows Desktop** > **třídy knihovna (.NET Framework)**

* V **Konzola správce balíčků** (PMC) u obou projektů, spusťte příkaz `Install-Package Entityframework`.

* V projektu knihovny tříd, vytvoření tříd datových modelů a třídu kontextu a implementace `IDbContextFactory`.

* V konzole PMC pro projekt knihovny tříd, spusťte příkazy `Enable-Migrations` a `Add-Migration Initial`. Pokud nastavíte projekt ASP.NET Core jako spouštěný projekt, přidejte `-StartupProjectName EF6` těchto příkazů.

* V Core projektu přidejte odkaz na projekt knihovny tříd.

* V projektu jader v *Startup.cs*, zaregistrujte DI kontextu.

* V projektu jader v *appsettings.json*, přidejte připojovací řetězec.

* V projektu Core přidáte kontroler a zobrazení, chcete-li ověřit, že může číst a zapisovat data. (Všimněte si, že generování uživatelského rozhraní ASP.NET Core MVC nebude fungovat s EF6 kontextu odkazován z knihovny tříd.)

## <a name="summary"></a>Souhrn

Tento článek poskytuje základní pokyny pro používání Entity Framework 6 v aplikaci ASP.NET Core.

## <a name="additional-resources"></a>Další zdroje

* [Entity Framework – konfigurace založená na kódu](https://msdn.microsoft.com/data/jj680699.aspx)
