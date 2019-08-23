---
title: Začínáme s ASP.NET Core a Entity Framework 6
author: rick-anderson
description: Tento článek ukazuje, jak použít Entity Framework 6 v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: ace937e72efa2343e50b11d52ebc0a2530505758
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975599"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a>Začínáme s ASP.NET Core a Entity Framework 6

[Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)a [Dykstra](https://github.com/tdykstra)

Tento článek ukazuje, jak použít Entity Framework 6 v aplikaci ASP.NET Core.

## <a name="overview"></a>Přehled

Chcete-li použít Entity Framework 6, projekt musí být zkompilován proti .NET Framework, protože Entity Framework 6 nepodporuje .NET Core. Pokud potřebujete funkce pro více platforem, budete muset upgradovat na [Entity Framework Core](/ef/).

Doporučený způsob použití Entity Framework 6 v aplikaci ASP.NET Core je umístit kontext EF6 a třídy modelu do projektu knihovny tříd, který cílí na úplné rozhraní. Do knihovny tříd přidejte odkaz z projektu ASP.NET Core. Podívejte se na ukázkové [řešení sady Visual Studio s projekty EF6 a ASP.NET Core](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).

EF6 kontext nemůžete vložit do projektu ASP.NET Core, protože projekty .NET Core nepodporují všechny funkce, které EF6 příkazy, jako je třeba *Enable – migrace* .

Bez ohledu na typ projektu, ve kterém najdete kontext EF6, fungují pouze nástroje příkazového řádku EF6 s kontextem EF6. Například `Scaffold-DbContext` je k dispozici pouze v Entity Framework Core. Pokud potřebujete provést zpětnou přípravu databáze na model EF6, přečtěte si téma [Code First do existující databáze](https://msdn.microsoft.com/jj200620).

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Odkaz na úplné rozhraní a EF6 v projektu ASP.NET Core

Váš projekt ASP.NET Core musí odkazovat na rozhraní .NET Framework a EF6. Například soubor *. csproj* vašeho projektu ASP.NET Core bude vypadat podobně jako v následujícím příkladu (zobrazují se pouze relevantní části souboru).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

Při vytváření nového projektu použijte šablonu **ASP.NET Core webové aplikace (.NET Framework)** .

## <a name="handle-connection-strings"></a>Zpracování připojovacích řetězců

Nástroje příkazového řádku EF6, které použijete v projektu knihovny tříd EF6, vyžadují výchozí konstruktor, aby mohli vytvářet instance kontextu. Ale pravděpodobně budete chtít zadat připojovací řetězec pro použití v ASP.NET Core projektu. v takovém případě musí mít konstruktor kontextu parametr, který vám umožní předat připojovací řetězec. Tady je příklad.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

Vzhledem k tomu, že váš kontext EF6 nemá konstruktor bez parametrů, musí váš projekt EF6 poskytovat implementaci [IDbContextFactory](https://msdn.microsoft.com/library/hh506876). Nástroje příkazového řádku EF6 vyhledají a použijí tuto implementaci, aby mohli vytvářet instance kontextu. Tady je příklad.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

V tomto ukázkovém kódu `IDbContextFactory` implementace projde pevně zakódovaným připojovacím řetězcem. Toto je připojovací řetězec, který budou používat nástroje příkazového řádku. Chcete-li zajistit, aby knihovna tříd používala stejný připojovací řetězec, jaký používá volající aplikace, budete chtít implementovat strategii. Můžete například získat hodnotu z proměnné prostředí v obou projektech.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Nastavení injektáže závislosti v projektu ASP.NET Core

V souboru *Startup.cs* základního projektu nastavte kontext EF6 pro vkládání závislostí (di) v `ConfigureServices`. Objekty kontextu EF by měly být v oboru pro dobu života žádosti.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

Pak můžete získat instanci kontextu v řadičích do řadičů pomocí DI. Kód je podobný tomu, co píšete pro EF Coreho kontextu:

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>Ukázková aplikace

Pracovní ukázkovou aplikaci najdete v ukázkovém [řešení sady Visual Studio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) , které doprovází tento článek.

Tuto ukázku můžete vytvořit úplně od začátku pomocí následujících kroků v aplikaci Visual Studio:

* Vytvořte řešení.

* **Přidat** > **novou webovou** **aplikaci** Project**Web**ASP.NET Core >  > 
  * V dialogovém okně Výběr šablony projektu vyberte v rozevíracím seznamu položku rozhraní API a .NET Framework.

* **Přidat** >  >  novou knihovnu tříd pro Windows Project Desktop (.NET Framework) > 

* V **konzole správce balíčků** (PMC) pro oba projekty spusťte příkaz `Install-Package Entityframework`.

* V projektu knihovny tříd vytvořte třídy datového modelu a třídu kontextu a implementaci `IDbContextFactory`.

* V PMC pro projekt knihovny tříd spusťte příkazy `Enable-Migrations` a. `Add-Migration Initial` Pokud jste nastavili ASP.NET Core projekt jako spouštěný projekt, přidejte `-StartupProjectName EF6` k těmto příkazům.

* V projektu Core přidejte odkaz na projekt do projektu knihovny tříd.

* V projektu Core Zaregistrujte v *Startup.cs*kontext pro di.

* V projektu Core v souboru *appSettings. JSON*přidejte připojovací řetězec.

* V projektu Core přidejte kontroler a zobrazení, abyste ověřili, že můžete číst a zapisovat data. (Všimněte si, že ASP.NET Core generování uživatelského rozhraní MVC nebude fungovat s kontextem EF6, na který se odkazuje z knihovny tříd.)

## <a name="summary"></a>Souhrn

V tomto článku najdete základní pokyny k používání Entity Framework 6 v ASP.NET Core aplikaci.

## <a name="additional-resources"></a>Další zdroje

* [Konfigurace na základě kódu Entity Framework](https://msdn.microsoft.com/data/jj680699.aspx)
