---
title: Začínáme s ASP.NET core a entity Framework 6
author: rick-anderson
description: Tento článek ukazuje, jak používat entity Framework 6 v ASP.NET základní aplikace.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 85cf86dcb22ef94cfc87975abaab176e4f1227d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656385"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a>Začínáme s ASP.NET core a entity Framework 6

[Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex)a [Tom Dykstra](https://github.com/tdykstra)

Tento článek ukazuje, jak používat entity Framework 6 v ASP.NET základní aplikace.

## <a name="overview"></a>Přehled

Chcete-li použít entity Framework 6, váš projekt musí zkompilovat proti rozhraní .NET Framework, protože entity Framework 6 nepodporuje .NET Core. Pokud potřebujete funkce pro různé platformy, budete muset upgradovat na [core entity frameworku](/ef/).

Doporučený způsob, jak použít entity Framework 6 v ASP.NET základní aplikace je umístit EF6 kontextu a třídy modelu v projektu knihovny tříd, který cíle .NET Framework. Přidejte odkaz na knihovnu tříd z projektu ASP.NET Core. Podívejte se na ukázkové [řešení Visual Studio s EF6 a ASP.NET základní projekty](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).

Nelze umístit kontextu EF6 v ASP.NET základní projekt, protože projekty .NET Core nepodporují všechny funkce, které ef6 příkazy, jako je *například Enable-Migrations* vyžadují.

Bez ohledu na typ projektu, ve kterém najdete kontext EF6, pouze EF6 nástroje příkazového řádku pracovat s kontextem EF6. Například `Scaffold-DbContext` je k dispozici pouze v jádru entity frameworku. Pokud potřebujete provést zpětnou analýzu databáze do modelu EF6, naleznete [v tématu Code First to a Existing Database](https://msdn.microsoft.com/jj200620).

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Odkaz na úplný rámec a EF6 v projektu ASP.NET Core

Váš ASP.NET základní projekt musí cílit na rozhraní .NET Framework a odkazovat na EF6. Například soubor *.csproj* projektu ASP.NET Core bude vypadat podobně jako v následujícím příkladu (zobrazí se pouze relevantní části souboru).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

Při vytváření nového projektu použijte šablonu **ASP.NET Core Web Application (.NET Framework).**

## <a name="handle-connection-strings"></a>Zpracování připojovacích řetězců

Nástroje příkazového řádku EF6, které budete používat v projektu knihovny tříd EF6, vyžadují výchozí konstruktor, aby mohly vytvořit konkretizovat kontext. Ale budete pravděpodobně chtít zadat připojovací řetězec pro použití v projektu ASP.NET Core, v takovém případě váš konstruktor kontextu musí mít parametr, který umožňuje předat v připojovacím řetězci. Tady je příklad.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

Vzhledem k tomu, že váš kontext EF6 nemá konstruktor bez parametrů, váš projekt EF6 musí poskytnout implementaci [IDbContextFactory](https://msdn.microsoft.com/library/hh506876). Ef6 nástroje příkazového řádku najde a použít tuto implementaci tak, aby mohly vytvořit konkretizovat kontext. Tady je příklad.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

V tomto ukázkovém `IDbContextFactory` kódu implementace předá v pevně zakódovaném připojovacím řetězci. Toto je připojovací řetězec, který budou používat nástroje příkazového řádku. Budete chtít implementovat strategii k zajištění, že knihovna tříd používá stejný připojovací řetězec, který používá volající aplikace. Můžete například získat hodnotu z proměnné prostředí v obou projektech.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Nastavení vkládání závislostí v projektu ASP.NET Core

V souboru *Startup.cs* základního projektu nastavte kontext EF6 pro vkládání závislostí (DI) v `ConfigureServices`. Objekty kontextu EF by měly být vymezeny pro životnost požadavku.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

Potom můžete získat instanci kontextu v řadičích pomocí DI. Kód je podobný tomu, co byste napsali pro kontext EF Core:

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>Ukázková aplikace

Pracovní ukázkové aplikace naleznete v ukázkové řešení [sady Visual Studio,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) které doprovází tento článek.

Tuto ukázku lze vytvořit od začátku pomocí následujících kroků v sadě Visual Studio:

* Vytvořte řešení.

* **Přidat** > novou**webovou** > **ASP.NET základní webovou aplikaci** **projectu** > 
  * V dialogovém okně výběru šablony projektu vyberte v rozevíracím seznami rozhraní API a rozhraní .NET Framework.

* **Přidat** > **novou knihovnu** > tříd**desktopového prostředí** > **windows (.NET Framework)**

* V **konzole Správce balíčků** (PMC) pro `Install-Package Entityframework`oba projekty spusťte příkaz .

* V projektu knihovny tříd vytvořte třídy datového modelu `IDbContextFactory`a třídu kontextu a implementaci aplikace .

* V PMC pro projekt knihovny tříd `Enable-Migrations` `Add-Migration Initial`spusťte příkazy a . Pokud jste nastavili ASP.NET základní projekt jako `-StartupProjectName EF6` projekt při spuštění, přidejte do těchto příkazů.

* V projektu Core přidejte odkaz na projekt projektu knihovny tříd.

* V projektu Core v *Startup.cs*zaregistrujte kontext pro DI.

* V projektu Core přidejte v *souboru appsettings.json*připojovací řetězec.

* V projektu Core přidejte řadič a zobrazení, abyste ověřili, že můžete číst a zapisovat data. (Všimněte si, že ASP.NET základní hosti cítění MVC nebude fungovat s kontextem EF6 odkazovaném z knihovny tříd.)

## <a name="summary"></a>Souhrn

Tento článek obsahuje základní pokyny pro použití entity Framework 6 v ASP.NET základní aplikace.

## <a name="additional-resources"></a>Další zdroje

* [Entity Framework – konfigurace založená na kódu](https://msdn.microsoft.com/data/jj680699.aspx)
