---
title: Razor Pages testování částí v ASP.NET Core
author: guardrex
description: Naučte se vytvářet testy jednotek pro aplikace Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 35feb5dd95fa79ceca7ff03523cef30d29ccbdd3
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022565"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Razor Pages testování částí v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core podporuje testy jednotek aplikací Razor Pages. Testy vrstvy přístupu k datům (DAL) a modelů stránek vám pomůžou zajistit:

* Části aplikace Razor Pages fungují nezávisle a společně jako jednotka během vytváření aplikací.
* Třídy a metody mají omezené obory zodpovědnosti.
* Další dokumentace existuje v tom, jak se má aplikace chovat.
* V průběhu automatizovaného sestavování a nasazování se nacházejí regrese, které se týkají chyb v rámci aktualizací kódu.

V tomto tématu se předpokládá, že máte základní znalosti Razor Pages aplikací a testování částí. Pokud nejste obeznámeni s Razor Pages aplikacemi nebo koncepty testování, přečtěte si následující témata:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testování C# částí v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([stažení](xref:index#how-to-download-a-sample))

Vzorový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace zprávy | *src/RazorPagesTestSample*         | Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu). |
| Testovací aplikace    | *testuje/RazorPagesTestSample. Tests* | Slouží k testování modelu DAL a stránky indexu aplikace zprávy. |

Testy lze spustit pomocí integrovaných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz na příkazovém řádku ve složce *Tests/RazorPagesTestSample. Tests* :

```console
dotnet test
```

## <a name="message-app-organization"></a>Organizace aplikace zprávy

Aplikace zprávy je Razor Pages systém zpráv s následujícími charakteristikami:

* Stránka indexu aplikace (*pages/index. cshtml* a Pages */index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (hledání průměrného počtu slov na jednu zprávu).
* Zpráva je popsána `Message` třídou (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva). `Text` Vlastnost je povinná a omezená na 200 znaků.
* Zprávy se ukládají&#8224;pomocí [databáze Entity Framework v paměti](/ef/core/providers/in-memory/).
* Aplikace obsahuje ve své třídě `AppDbContext` kontext databáze dal (*data/AppDbContext. cs*). Metody dal jsou označeny `virtual`, což umožňuje napodobovat metody pro použití v testech.
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami. Tyto *osazené zprávy* se používají také v testech.

&#8224;Téma EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest. Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) . Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.

I když ukázková aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotky práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages podporuje tyto vzory vývoje. Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Organizace testovací aplikace

Testovací aplikace je Konzolová aplikace ve složce *Tests/RazorPagesTestSample. Tests* .

| Složka testovací aplikace | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje testy jednotek pro dal.</li><li>*IndexPageTests.cs* obsahuje testy jednotek pro model stránky indexu.</li></ul> |
| *Nástroje*     | `TestDbContextOptions` Obsahuje metodu použitou k vytvoření nové možnosti kontextu databáze pro každý test jednotky dal, aby se databáze obnovila na svůj základní stav pro každý test. |

Testovací rozhraní je [xUnit](https://xunit.github.io/). Rozhraní objektu pro napodobení je [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testování částí vrstvy přístupu k datům (DAL)

Aplikace zprávy obsahuje dal se čtyřmi metodami, které jsou `AppDbContext` obsaženy ve třídě (*Src/RazorPagesTestSample/data/AppDbContext. cs*). Každá metoda má jednu nebo dvě testy jednotek v testovací aplikaci.

| DAL – metoda               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá z databáze seřazené `Text` podle vlastnosti. `List<Message>` |
| `AddMessageAsync`        | `Message` Přidá do databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny `Message` položky z databáze.                           |
| `DeleteMessageAsync`     | Odstraní z `Message` `Id`databáze jednu z nich.                      |

Testování částí dal vyžaduje <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test. Jedním ze způsobů, jak `DbContextOptions` vytvořit pro každý test, je <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>použít:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu, který předchozí test opustil. To může být problematické při pokusu o zápis atomických testů, které mezi sebou nekolidují. Chcete-li `AppDbContext` vynutit použití nového kontextu databáze pro každý test, `DbContextOptions` zadejte instanci, která je založena na novém poskytovateli služeb. Testovací aplikace ukazuje, jak to provést pomocí `Utilities` metody `TestDbContextOptions` třídy (Tests */RazorPagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Použití funkce `DbContextOptions` v testech jednotek dal umožňuje, aby každý test běžel atomicky s novou instancí databáze:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Každá testovací metoda ve `DataAccessLayerTest` třídě (*UnitTests/DataAccessLayerTest. cs*) se řídí podobným vzorem příkazu uspořádat – Act-Assert:

1. Organizuje Databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.
1. Usnáší Test je proveden.
1. Uplatňuje Kontrolní výrazy jsou provedeny za účelem určení, zda je výsledek testu úspěch.

Například `DeleteMessageAsync` metoda zodpovídá za odebrání jedné zprávy identifikované její `Id` (*Src/RazorPagesTestSample/data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test kontroluje, zda metoda odstraní zprávu, když se zpráva nachází v databázi. Druhá metoda Testuje, že se databáze nemění, pokud zpráva `Id` pro odstranění neexistuje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` Metoda je zobrazena níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Nejprve metoda provede krok uspořádat, kde se provádí příprava pro krok Act. Zprávy o osazení se získávají a uchovávají v `seedMessages`. Počáteční zprávy se ukládají do databáze nástroje. Zpráva s objektem `Id` `1` je nastavena pro odstranění. Při spuštění `Id` `1`metody musí mít očekávané zprávy všechny zprávy s výjimkou jednoho s. `DeleteMessageAsync` `expectedMessages` Proměnná představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda funguje: Metoda je prováděna předáním `recId` v `1`: `DeleteMessageAsync`

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec Metoda získá `Messages` z kontextu a porovná ho `expectedMessages` s kontrolním výrazem, že obě jsou stejné:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat tyto dvě `List<Message>` stejné:

* Zprávy jsou seřazeny podle `Id`.
* Páry zpráv jsou porovnány `Text` s vlastností.

Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` kontroluje výsledek pokusu o odstranění neexistující zprávy. V takovém případě by měly být očekávané zprávy v databázi rovny skutečným zprávám po `DeleteMessageAsync` provedení metody. Obsah databáze by neměl být změněn:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testování částí metod modelu stránky

Další sada testů jednotek zodpovídá za testy metod modelu stránky. V aplikaci zprávy jsou modely stránek indexu nalezeny ve `IndexModel` třídě v *Src/RazorPagesTestSample/pages/index. cshtml. cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z dal pro uživatelské rozhraní pomocí `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) platný, volání `AddMessageAsync` pro přidání zprávy do databáze. |
| `OnPostDeleteAllMessagesAsync` | Volá `DeleteAllMessagesAsync` se, aby se odstranily všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Provede `DeleteMessageAsync` odstranění zprávy `Id` se zadaným. |
| `OnPostAnalyzeMessagesAsync` | Pokud je v databázi jedna nebo více zpráv, vypočítá průměrný počet slov na jednu zprávu. |

Metody modelu stránky jsou testovány pomocí sedmi testů ve `IndexPageTests` třídě (Tests */RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Testy používají známý vzor uspořádání a vyhodnocení – Act. Tyto testy se zaměřují na:

* Určení, zda metody dodrží správné chování, pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatné.
* Potvrzení metod vyprodukuje správné <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Kontroluje se, jestli jsou přiřazení hodnot vlastností správně vytvořená.

Tato skupina testů často napodobuje metody DAL, aby vytvořila očekávaná data pro krok Act, kde je spuštěna metoda modelu stránky. Například `GetMessagesAsync` Metoda`AppDbContext` je napodobná, aby vytvořila výstup. Když metoda modelu stránky spustí tuto metodu, vrátí výsledek. Data nepocházejí z databáze. Tím se vytvoří předvídatelné a spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.

Test ukazuje, `GetMessagesAsync` jak je metoda popsána pro model stránky: `OnGetAsync_PopulatesThePageModel_WithAListOfMessages`

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Při spuštění `GetMessagesAsync` metody v kroku Act volá metodu modelu stránky. `OnGetAsync`

Krok testu jednotek krok (*Tests/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage``OnGetAsync` metoda modelu stránky (*Src/RazorPagesTestSample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync` Metoda v metodě dal nevrací výsledek pro toto volání metody. Napodobovaná verze metody vrátí výsledek.

V kroku jsou skutečné zprávy (`actualMessages` `Messages` ) přiřazeny z vlastnosti modelu stránky. `Assert` Při přiřazení zpráv se také provádí ověření typu. Očekávané a skutečné zprávy jsou porovnány podle jejich `Text` vlastností. Test vyhodnotí, že dvě `List<Message>` instance obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Jiné testy v této skupině vytvoří objekty modelu stránky, které obsahují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, a `PageContext`, <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext`k vytvoření, `ViewDataDictionary`a. Ty jsou užitečné při provádění testů. `ModelState` Například aplikace zprávy naváže chybu s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> cílem ověřit, že je při `OnPostAddMessageAsync` spuštění vrácena <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> platná:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje

* [Testování C# částí v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Začínáme s xUnit.net: Použití .NET Core s příkazovým řádkem sady .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Rychlý Start MOQ](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core podporuje testy jednotek aplikací Razor Pages. Testy vrstvy přístupu k datům (DAL) a modelů stránek vám pomůžou zajistit:

* Části aplikace Razor Pages fungují nezávisle a společně jako jednotka během vytváření aplikací.
* Třídy a metody mají omezené obory zodpovědnosti.
* Další dokumentace existuje v tom, jak se má aplikace chovat.
* V průběhu automatizovaného sestavování a nasazování se nacházejí regrese, které se týkají chyb v rámci aktualizací kódu.

V tomto tématu se předpokládá, že máte základní znalosti Razor Pages aplikací a testování částí. Pokud nejste obeznámeni s Razor Pages aplikacemi nebo koncepty testování, přečtěte si následující témata:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testování C# částí v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([stažení](xref:index#how-to-download-a-sample))

Vzorový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace zprávy | *src/RazorPagesTestSample*         | Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu). |
| Testovací aplikace    | *testuje/RazorPagesTestSample. Tests* | Slouží k testování modelu DAL a stránky indexu aplikace zprávy. |

Testy lze spustit pomocí integrovaných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz na příkazovém řádku ve složce *Tests/RazorPagesTestSample. Tests* :

```console
dotnet test
```

## <a name="message-app-organization"></a>Organizace aplikace zprávy

Aplikace zprávy je Razor Pages systém zpráv s následujícími charakteristikami:

* Stránka indexu aplikace (*pages/index. cshtml* a Pages */index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (hledání průměrného počtu slov na jednu zprávu).
* Zpráva je popsána `Message` třídou (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva). `Text` Vlastnost je povinná a omezená na 200 znaků.
* Zprávy se ukládají&#8224;pomocí [databáze Entity Framework v paměti](/ef/core/providers/in-memory/).
* Aplikace obsahuje ve své třídě `AppDbContext` kontext databáze dal (*data/AppDbContext. cs*). Metody dal jsou označeny `virtual`, což umožňuje napodobovat metody pro použití v testech.
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami. Tyto *osazené zprávy* se používají také v testech.

&#8224;Téma EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest. Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) . Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.

I když ukázková aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotky práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages podporuje tyto vzory vývoje. Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Organizace testovací aplikace

Testovací aplikace je Konzolová aplikace ve složce *Tests/RazorPagesTestSample. Tests* .

| Složka testovací aplikace | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje testy jednotek pro dal.</li><li>*IndexPageTests.cs* obsahuje testy jednotek pro model stránky indexu.</li></ul> |
| *Nástroje*     | `TestDbContextOptions` Obsahuje metodu použitou k vytvoření nové možnosti kontextu databáze pro každý test jednotky dal, aby se databáze obnovila na svůj základní stav pro každý test. |

Testovací rozhraní je [xUnit](https://xunit.github.io/). Rozhraní objektu pro napodobení je [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testování částí vrstvy přístupu k datům (DAL)

Aplikace zprávy obsahuje dal se čtyřmi metodami, které jsou `AppDbContext` obsaženy ve třídě (*Src/RazorPagesTestSample/data/AppDbContext. cs*). Každá metoda má jednu nebo dvě testy jednotek v testovací aplikaci.

| DAL – metoda               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá z databáze seřazené `Text` podle vlastnosti. `List<Message>` |
| `AddMessageAsync`        | `Message` Přidá do databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny `Message` položky z databáze.                           |
| `DeleteMessageAsync`     | Odstraní z `Message` `Id`databáze jednu z nich.                      |

Testování částí dal vyžaduje <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test. Jedním ze způsobů, jak `DbContextOptions` vytvořit pro každý test, je <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>použít:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu, který předchozí test opustil. To může být problematické při pokusu o zápis atomických testů, které mezi sebou nekolidují. Chcete-li `AppDbContext` vynutit použití nového kontextu databáze pro každý test, `DbContextOptions` zadejte instanci, která je založena na novém poskytovateli služeb. Testovací aplikace ukazuje, jak to provést pomocí `Utilities` metody `TestDbContextOptions` třídy (Tests */RazorPagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Použití funkce `DbContextOptions` v testech jednotek dal umožňuje, aby každý test běžel atomicky s novou instancí databáze:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Každá testovací metoda ve `DataAccessLayerTest` třídě (*UnitTests/DataAccessLayerTest. cs*) se řídí podobným vzorem příkazu uspořádat – Act-Assert:

1. Organizuje Databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.
1. Usnáší Test je proveden.
1. Uplatňuje Kontrolní výrazy jsou provedeny za účelem určení, zda je výsledek testu úspěch.

Například `DeleteMessageAsync` metoda zodpovídá za odebrání jedné zprávy identifikované její `Id` (*Src/RazorPagesTestSample/data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test kontroluje, zda metoda odstraní zprávu, když se zpráva nachází v databázi. Druhá metoda Testuje, že se databáze nemění, pokud zpráva `Id` pro odstranění neexistuje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` Metoda je zobrazena níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Nejprve metoda provede krok uspořádat, kde se provádí příprava pro krok Act. Zprávy o osazení se získávají a uchovávají v `seedMessages`. Počáteční zprávy se ukládají do databáze nástroje. Zpráva s objektem `Id` `1` je nastavena pro odstranění. Při spuštění `Id` `1`metody musí mít očekávané zprávy všechny zprávy s výjimkou jednoho s. `DeleteMessageAsync` `expectedMessages` Proměnná představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda funguje: Metoda je prováděna předáním `recId` v `1`: `DeleteMessageAsync`

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec Metoda získá `Messages` z kontextu a porovná ho `expectedMessages` s kontrolním výrazem, že obě jsou stejné:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat tyto dvě `List<Message>` stejné:

* Zprávy jsou seřazeny podle `Id`.
* Páry zpráv jsou porovnány `Text` s vlastností.

Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` kontroluje výsledek pokusu o odstranění neexistující zprávy. V takovém případě by měly být očekávané zprávy v databázi rovny skutečným zprávám po `DeleteMessageAsync` provedení metody. Obsah databáze by neměl být změněn:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testování částí metod modelu stránky

Další sada testů jednotek zodpovídá za testy metod modelu stránky. V aplikaci zprávy jsou modely stránek indexu nalezeny ve `IndexModel` třídě v *Src/RazorPagesTestSample/pages/index. cshtml. cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z dal pro uživatelské rozhraní pomocí `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) platný, volání `AddMessageAsync` pro přidání zprávy do databáze. |
| `OnPostDeleteAllMessagesAsync` | Volá `DeleteAllMessagesAsync` se, aby se odstranily všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Provede `DeleteMessageAsync` odstranění zprávy `Id` se zadaným. |
| `OnPostAnalyzeMessagesAsync` | Pokud je v databázi jedna nebo více zpráv, vypočítá průměrný počet slov na jednu zprávu. |

Metody modelu stránky jsou testovány pomocí sedmi testů ve `IndexPageTests` třídě (Tests */RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Testy používají známý vzor uspořádání a vyhodnocení – Act. Tyto testy se zaměřují na:

* Určení, zda metody dodrží správné chování, pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatné.
* Potvrzení metod vyprodukuje správné <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Kontroluje se, jestli jsou přiřazení hodnot vlastností správně vytvořená.

Tato skupina testů často napodobuje metody DAL, aby vytvořila očekávaná data pro krok Act, kde je spuštěna metoda modelu stránky. Například `GetMessagesAsync` Metoda`AppDbContext` je napodobná, aby vytvořila výstup. Když metoda modelu stránky spustí tuto metodu, vrátí výsledek. Data nepocházejí z databáze. Tím se vytvoří předvídatelné a spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.

Test ukazuje, `GetMessagesAsync` jak je metoda popsána pro model stránky: `OnGetAsync_PopulatesThePageModel_WithAListOfMessages`

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Při spuštění `GetMessagesAsync` metody v kroku Act volá metodu modelu stránky. `OnGetAsync`

Krok testu jednotek krok (*Tests/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage``OnGetAsync` metoda modelu stránky (*Src/RazorPagesTestSample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync` Metoda v metodě dal nevrací výsledek pro toto volání metody. Napodobovaná verze metody vrátí výsledek.

V kroku jsou skutečné zprávy (`actualMessages` `Messages` ) přiřazeny z vlastnosti modelu stránky. `Assert` Při přiřazení zpráv se také provádí ověření typu. Očekávané a skutečné zprávy jsou porovnány podle jejich `Text` vlastností. Test vyhodnotí, že dvě `List<Message>` instance obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Jiné testy v této skupině vytvoří objekty modelu stránky, které obsahují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, a `PageContext`, <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext`k vytvoření, `ViewDataDictionary`a. Ty jsou užitečné při provádění testů. `ModelState` Například aplikace zprávy naváže chybu s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> cílem ověřit, že je při `OnPostAddMessageAsync` spuštění vrácena <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> platná:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje

* [Testování C# částí v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Začínáme s xUnit.net: Použití .NET Core s příkazovým řádkem sady .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Rychlý Start MOQ](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
