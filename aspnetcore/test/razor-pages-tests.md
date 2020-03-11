---
title: Razor Pages testování částí v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet testy jednotek pro aplikace Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664407"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Razor Pages testování částí v ASP.NET Core

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

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Vzorový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace zprávy | *src/RazorPagesTestSample*         | Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu). |
| Testovací aplikace    | *testuje/RazorPagesTestSample. Tests* | Slouží k testování modelu DAL a stránky indexu aplikace zprávy. |

Testy lze spustit pomocí integrovaných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz na příkazovém řádku ve složce *Tests/RazorPagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizace aplikace zprávy

Aplikace zprávy je Razor Pages systém zpráv s následujícími charakteristikami:

* Stránka indexu aplikace (*pages/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (hledání průměrného počtu slov na jednu zprávu).
* Zpráva je popsána ve třídě `Message` (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva). Vlastnost `Text` je povinná a omezená na 200 znaků.
* Zprávy se ukládají&#8224;pomocí [databáze Entity Framework v paměti](/ef/core/providers/in-memory/).
* Aplikace obsahuje DAL ve své třídě kontext databáze `AppDbContext` (*data/AppDbContext. cs*). Metody DAL jsou označené jako `virtual`, což umožňuje napodobovat metody pro použití v testech.
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami. Tyto *osazené zprávy* se používají také v testech.

&#8224;Téma EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest. Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) . Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.

I když ukázková aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotky práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages podporuje tyto vzory vývoje. Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Organizace testovací aplikace

Testovací aplikace je Konzolová aplikace ve složce *Tests/RazorPagesTestSample. Tests* .

| Složka testovací aplikace | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje testy jednotek pro dal.</li><li>*IndexPageTests.cs* obsahuje testy jednotek pro model stránky indexu.</li></ul> |
| *Nástroje*     | Obsahuje metodu `TestDbContextOptions`, která se používá k vytvoření nové možnosti kontextu databáze pro každý test jednotky DAL, aby se databáze obnovila na svůj základní stav pro každý test. |

Testovací rozhraní je [xUnit](https://xunit.github.io/). Rozhraní objektu pro napodobení je [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testování částí vrstvy přístupu k datům (DAL)

Aplikace zprávy obsahuje DAL se čtyřmi metodami, které jsou obsaženy ve třídě `AppDbContext` (*Src/RazorPagesTestSample/data/AppDbContext. cs*). Každá metoda má jednu nebo dvě testy jednotek v testovací aplikaci.

| DAL – metoda               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá `List<Message>` z databáze seřazené podle vlastnosti `Text`. |
| `AddMessageAsync`        | Přidá `Message` do databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny položky `Message` z databáze.                           |
| `DeleteMessageAsync`     | Odstraní jeden `Message` z databáze pomocí `Id`.                      |

Testy jednotek DAL vyžadují <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test. Jedním z přístupů k vytváření `DbContextOptions` pro každý test je použití <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu, který předchozí test opustil. To může být problematické při pokusu o zápis atomických testů, které mezi sebou nekolidují. Chcete-li vynutit, aby `AppDbContext` používala nový kontext databáze pro každý test, zadejte instanci `DbContextOptions`, která je založena na novém poskytovateli služeb. Testovací aplikace ukazuje, jak to provést pomocí své `Utilities` metody třídy `TestDbContextOptions` (*Tests/RazorPagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Použití `DbContextOptions` v testováních jednotek DAL umožňuje, aby každý test běžel atomicky s novou instancí databáze:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Každá testovací metoda ve třídě `DataAccessLayerTest` (*UnitTests/DataAccessLayerTest. cs*) se řídí podobným vzorem příkazu uspořádat – Act-Assert:

1. Uspořádat: databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.
1. ACT: test je proveden.
1. Assert: kontrolní výrazy jsou určeny k určení, zda je výsledek testu úspěch.

Například metoda `DeleteMessageAsync` zodpovídá za odebrání jedné zprávy identifikované její `Id` (*Src/RazorPagesTestSample/data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test kontroluje, zda metoda odstraní zprávu, když se zpráva nachází v databázi. Druhá metoda Testuje, že se databáze nemění, pokud `Id` zpráva pro odstranění neexistuje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` metoda je zobrazena níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Nejprve metoda provede krok uspořádat, kde se provádí příprava pro krok Act. Zprávy o osazení se získávají a uchovávají v `seedMessages`. Počáteční zprávy se ukládají do databáze nástroje. Zpráva s `Id` `1` je nastavena k odstranění. Při spuštění metody `DeleteMessageAsync` musí mít očekávané zprávy všechny zprávy s výjimkou toho, že se jedná o `Id` `1`. Proměnná `expectedMessages` představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda funguje: metoda `DeleteMessageAsync` je prováděna předání `recId` `1`:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec Metoda získá `Messages` z kontextu a porovná ji s `expectedMessages` vyhodnotit, že obě jsou stejné:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat, zda jsou tyto dva `List<Message>` stejné:

* Zprávy jsou seřazené podle `Id`.
* Páry zpráv se porovnávají s vlastností `Text`.

Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` kontroluje výsledek pokusu o odstranění neexistující zprávy. V takovém případě by měly být očekávané zprávy v databázi rovny skutečným zprávám po spuštění metody `DeleteMessageAsync`. Obsah databáze by neměl být změněn:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testování částí metod modelu stránky

Další sada testů jednotek zodpovídá za testy metod modelu stránky. V aplikaci zprávy se modely stránek indexu nacházejí ve třídě `IndexModel` v *Src/RazorPagesTestSample/pages/index. cshtml. cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z DAL pro uživatelské rozhraní pomocí metody `GetMessagesAsync`. |
| `OnPostAddMessageAsync` | Pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) platný, volá `AddMessageAsync`, aby se do databáze přidala zpráva. |
| `OnPostDeleteAllMessagesAsync` | Zavolá `DeleteAllMessagesAsync`, aby se odstranily všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Spustí `DeleteMessageAsync` k odstranění zprávy se zadaným `Id`. |
| `OnPostAnalyzeMessagesAsync` | Pokud je v databázi jedna nebo více zpráv, vypočítá průměrný počet slov na jednu zprávu. |

Metody modelu stránky jsou testovány pomocí sedmi testů ve třídě `IndexPageTests` (*Tests/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Testy používají známý vzor uspořádání a vyhodnocení – Act. Tyto testy se zaměřují na:

* Určení, zda metody dodrží správné chování, pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatné.
* Potvrzení metod vyprodukuje správné <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Kontroluje se, jestli jsou přiřazení hodnot vlastností správně vytvořená.

Tato skupina testů často napodobuje metody DAL, aby vytvořila očekávaná data pro krok Act, kde je spuštěna metoda modelu stránky. Například metoda `GetMessagesAsync` `AppDbContext` je napsána na výstup. Když metoda modelu stránky spustí tuto metodu, vrátí výsledek. Data nepocházejí z databáze. Tím se vytvoří předvídatelné a spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.

Test `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` ukazuje, jak je metoda `GetMessagesAsync` pro model stránky popsána:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Při spuštění metody `OnGetAsync` v kroku Act volá metodu `GetMessagesAsync` modelu stránky.

Krok testu jednotek krok (*Tests/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

Metoda `OnGetAsync` `IndexPage` modelu stránky (*Src/RazorPagesTestSample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Metoda `GetMessagesAsync` v metodě DAL nevrací výsledek pro toto volání metody. Napodobovaná verze metody vrátí výsledek.

V kroku `Assert` jsou skutečné zprávy (`actualMessages`) přiřazeny z vlastnosti `Messages` modelu stránky. Při přiřazení zpráv se také provádí ověření typu. Očekávané a skutečné zprávy jsou porovnány podle jejich `Text` vlastností. Test vyhodnotí, že dvě instance `List<Message>` obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Jiné testy v této skupině vytvoří objekty modelu stránky, které zahrnují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext> k navázání `PageContext`, `ViewDataDictionary`a `PageContext`. Ty jsou užitečné při provádění testů. Například aplikace zprávy naváže `ModelState` chybu s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, aby zkontrolovala, zda je při spuštění `OnPostAddMessageAsync` vrácen platný <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje

* [Testování C# částí v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Začínáme s xUnit.net: použití .NET Core s příkazovým řádkem sady .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
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

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Vzorový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace zprávy | *src/RazorPagesTestSample*         | Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu). |
| Testovací aplikace    | *testuje/RazorPagesTestSample. Tests* | Slouží k testování modelu DAL a stránky indexu aplikace zprávy. |

Testy lze spustit pomocí integrovaných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz na příkazovém řádku ve složce *Tests/RazorPagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizace aplikace zprávy

Aplikace zprávy je Razor Pages systém zpráv s následujícími charakteristikami:

* Stránka indexu aplikace (*pages/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (hledání průměrného počtu slov na jednu zprávu).
* Zpráva je popsána ve třídě `Message` (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva). Vlastnost `Text` je povinná a omezená na 200 znaků.
* Zprávy se ukládají&#8224;pomocí [databáze Entity Framework v paměti](/ef/core/providers/in-memory/).
* Aplikace obsahuje DAL ve své třídě kontext databáze `AppDbContext` (*data/AppDbContext. cs*). Metody DAL jsou označené jako `virtual`, což umožňuje napodobovat metody pro použití v testech.
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami. Tyto *osazené zprávy* se používají také v testech.

&#8224;Téma EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest. Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) . Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.

I když ukázková aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotky práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages podporuje tyto vzory vývoje. Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Organizace testovací aplikace

Testovací aplikace je Konzolová aplikace ve složce *Tests/RazorPagesTestSample. Tests* .

| Složka testovací aplikace | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje testy jednotek pro dal.</li><li>*IndexPageTests.cs* obsahuje testy jednotek pro model stránky indexu.</li></ul> |
| *Nástroje*     | Obsahuje metodu `TestDbContextOptions`, která se používá k vytvoření nové možnosti kontextu databáze pro každý test jednotky DAL, aby se databáze obnovila na svůj základní stav pro každý test. |

Testovací rozhraní je [xUnit](https://xunit.github.io/). Rozhraní objektu pro napodobení je [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testování částí vrstvy přístupu k datům (DAL)

Aplikace zprávy obsahuje DAL se čtyřmi metodami, které jsou obsaženy ve třídě `AppDbContext` (*Src/RazorPagesTestSample/data/AppDbContext. cs*). Každá metoda má jednu nebo dvě testy jednotek v testovací aplikaci.

| DAL – metoda               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá `List<Message>` z databáze seřazené podle vlastnosti `Text`. |
| `AddMessageAsync`        | Přidá `Message` do databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny položky `Message` z databáze.                           |
| `DeleteMessageAsync`     | Odstraní jeden `Message` z databáze pomocí `Id`.                      |

Testy jednotek DAL vyžadují <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test. Jedním z přístupů k vytváření `DbContextOptions` pro každý test je použití <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu, který předchozí test opustil. To může být problematické při pokusu o zápis atomických testů, které mezi sebou nekolidují. Chcete-li vynutit, aby `AppDbContext` používala nový kontext databáze pro každý test, zadejte instanci `DbContextOptions`, která je založena na novém poskytovateli služeb. Testovací aplikace ukazuje, jak to provést pomocí své `Utilities` metody třídy `TestDbContextOptions` (*Tests/RazorPagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Použití `DbContextOptions` v testováních jednotek DAL umožňuje, aby každý test běžel atomicky s novou instancí databáze:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Každá testovací metoda ve třídě `DataAccessLayerTest` (*UnitTests/DataAccessLayerTest. cs*) se řídí podobným vzorem příkazu uspořádat – Act-Assert:

1. Uspořádat: databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.
1. ACT: test je proveden.
1. Assert: kontrolní výrazy jsou určeny k určení, zda je výsledek testu úspěch.

Například metoda `DeleteMessageAsync` zodpovídá za odebrání jedné zprávy identifikované její `Id` (*Src/RazorPagesTestSample/data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test kontroluje, zda metoda odstraní zprávu, když se zpráva nachází v databázi. Druhá metoda Testuje, že se databáze nemění, pokud `Id` zpráva pro odstranění neexistuje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` metoda je zobrazena níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Nejprve metoda provede krok uspořádat, kde se provádí příprava pro krok Act. Zprávy o osazení se získávají a uchovávají v `seedMessages`. Počáteční zprávy se ukládají do databáze nástroje. Zpráva s `Id` `1` je nastavena k odstranění. Při spuštění metody `DeleteMessageAsync` musí mít očekávané zprávy všechny zprávy s výjimkou toho, že se jedná o `Id` `1`. Proměnná `expectedMessages` představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda funguje: metoda `DeleteMessageAsync` je prováděna předání `recId` `1`:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec Metoda získá `Messages` z kontextu a porovná ji s `expectedMessages` vyhodnotit, že obě jsou stejné:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat, zda jsou tyto dva `List<Message>` stejné:

* Zprávy jsou seřazené podle `Id`.
* Páry zpráv se porovnávají s vlastností `Text`.

Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` kontroluje výsledek pokusu o odstranění neexistující zprávy. V takovém případě by měly být očekávané zprávy v databázi rovny skutečným zprávám po spuštění metody `DeleteMessageAsync`. Obsah databáze by neměl být změněn:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testování částí metod modelu stránky

Další sada testů jednotek zodpovídá za testy metod modelu stránky. V aplikaci zprávy se modely stránek indexu nacházejí ve třídě `IndexModel` v *Src/RazorPagesTestSample/pages/index. cshtml. cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z DAL pro uživatelské rozhraní pomocí metody `GetMessagesAsync`. |
| `OnPostAddMessageAsync` | Pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) platný, volá `AddMessageAsync`, aby se do databáze přidala zpráva. |
| `OnPostDeleteAllMessagesAsync` | Zavolá `DeleteAllMessagesAsync`, aby se odstranily všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Spustí `DeleteMessageAsync` k odstranění zprávy se zadaným `Id`. |
| `OnPostAnalyzeMessagesAsync` | Pokud je v databázi jedna nebo více zpráv, vypočítá průměrný počet slov na jednu zprávu. |

Metody modelu stránky jsou testovány pomocí sedmi testů ve třídě `IndexPageTests` (*Tests/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Testy používají známý vzor uspořádání a vyhodnocení – Act. Tyto testy se zaměřují na:

* Určení, zda metody dodrží správné chování, pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatné.
* Potvrzení metod vyprodukuje správné <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Kontroluje se, jestli jsou přiřazení hodnot vlastností správně vytvořená.

Tato skupina testů často napodobuje metody DAL, aby vytvořila očekávaná data pro krok Act, kde je spuštěna metoda modelu stránky. Například metoda `GetMessagesAsync` `AppDbContext` je napsána na výstup. Když metoda modelu stránky spustí tuto metodu, vrátí výsledek. Data nepocházejí z databáze. Tím se vytvoří předvídatelné a spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.

Test `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` ukazuje, jak je metoda `GetMessagesAsync` pro model stránky popsána:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Při spuštění metody `OnGetAsync` v kroku Act volá metodu `GetMessagesAsync` modelu stránky.

Krok testu jednotek krok (*Tests/RazorPagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

Metoda `OnGetAsync` `IndexPage` modelu stránky (*Src/RazorPagesTestSample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Metoda `GetMessagesAsync` v metodě DAL nevrací výsledek pro toto volání metody. Napodobovaná verze metody vrátí výsledek.

V kroku `Assert` jsou skutečné zprávy (`actualMessages`) přiřazeny z vlastnosti `Messages` modelu stránky. Při přiřazení zpráv se také provádí ověření typu. Očekávané a skutečné zprávy jsou porovnány podle jejich `Text` vlastností. Test vyhodnotí, že dvě instance `List<Message>` obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Jiné testy v této skupině vytvoří objekty modelu stránky, které zahrnují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext> k navázání `PageContext`, `ViewDataDictionary`a `PageContext`. Ty jsou užitečné při provádění testů. Například aplikace zprávy naváže `ModelState` chybu s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, aby zkontrolovala, zda je při spuštění `OnPostAddMessageAsync` vrácen platný <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje

* [Testování C# částí v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Začínáme s xUnit.net: použití .NET Core s příkazovým řádkem sady .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Rychlý Start MOQ](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
