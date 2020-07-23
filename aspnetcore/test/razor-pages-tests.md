---
title: RazorTestování částí stránky v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet testy jednotek pro Razor aplikace stránek.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/razor-pages-tests
ms.openlocfilehash: ed048d600b629335b8267b63b3cfd57b525d608e
ms.sourcegitcommit: c86b4e2955dc1724f2eaa7c97894ad8b3bf763fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2020
ms.locfileid: "86949091"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>RazorTestování částí stránky v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core podporuje testování částí Razor aplikací Pages. Testy vrstvy přístupu k datům (DAL) a modelů stránek vám pomůžou zajistit:

* Části Razor aplikace stránky fungují nezávisle a společně jako jednotka během vytváření aplikací.
* Třídy a metody mají omezené obory zodpovědnosti.
* Další dokumentace existuje v tom, jak se má aplikace chovat.
* V průběhu automatizovaného sestavování a nasazování se nacházejí regrese, které se týkají chyb v rámci aktualizací kódu.

V tomto tématu se předpokládá, že máte základní znalosti o Razor aplikacích stránky a testování částí. Pokud nejste obeznámeni s Razor aplikacemi Pages nebo koncepty testování, přečtěte si následující témata:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testování částí C# v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Vzorový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace zprávy | *src/ Razor PagesTestSample*         | Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu). |
| Testovací aplikace    | *testuje/ Razor PagesTestSample. Tests* | Slouží k testování modelu DAL a stránky indexu aplikace zprávy. |

Testy lze spustit pomocí integrovaných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz na příkazovém řádku ve složce *Tests/ Razor PagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizace aplikace zprávy

Zpráva aplikace je Razor systém zpráv s následujícími charakteristikami:

* Stránka indexu aplikace (*pages/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (hledání průměrného počtu slov na jednu zprávu).
* Zpráva je popsána `Message` třídou (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva). `Text`Vlastnost je povinná a omezená na 200 znaků.
* Zprávy se ukládají pomocí&#8224; [databáze Entity Framework v paměti](/ef/core/providers/in-memory/) .
* Aplikace obsahuje ve své třídě kontext databáze DAL `AppDbContext` (*data/AppDbContext. cs*). Metody DAL jsou označeny `virtual` , což umožňuje napodobovat metody pro použití v testech.
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami. Tyto *osazené zprávy* se používají také v testech.

&#8224;tématu EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest. Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) . Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.

I když ukázková aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotka práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor stránky tyto vzory vývoje podporují. Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Organizace testovací aplikace

Testovací aplikace je Konzolová aplikace ve složce *Tests/ Razor PagesTestSample. Tests* .

| Složka testovací aplikace | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje testy jednotek pro dal.</li><li>*IndexPageTests.cs* obsahuje testy jednotek pro model stránky indexu.</li></ul> |
| *Nástroje*     | Obsahuje `TestDbContextOptions` metodu použitou k vytvoření nové možnosti kontextu databáze pro každý test jednotky dal, aby se databáze obnovila na svůj základní stav pro každý test. |

Testovací rozhraní je [xUnit](https://xunit.github.io/). Rozhraní objektu pro napodobení je [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testování částí vrstvy přístupu k datům (DAL)

Aplikace zprávy obsahuje DAL se čtyřmi metodami, které jsou obsaženy ve `AppDbContext` třídě (*Src/ Razor PagesTestSample/data/AppDbContext. cs*). Každá metoda má jednu nebo dvě testy jednotek v testovací aplikaci.

| DAL – metoda               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá `List<Message>` z databáze seřazené podle `Text` Vlastnosti. |
| `AddMessageAsync`        | Přidá do `Message` databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny `Message` položky z databáze.                           |
| `DeleteMessageAsync`     | Odstraní `Message` z databáze jednu z nich `Id` .                      |

Testování částí DAL vyžaduje <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test. Jedním ze způsobů, jak vytvořit `DbContextOptions` pro každý test, je použít <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu, který předchozí test opustil. To může být problematické při pokusu o zápis atomických testů, které mezi sebou nekolidují. Chcete-li vynutit `AppDbContext` použití nového kontextu databáze pro každý test, zadejte `DbContextOptions` instanci, která je založena na novém poskytovateli služeb. Testovací aplikace ukazuje, jak to provést pomocí `Utilities` metody třídy `TestDbContextOptions` (*Tests/ Razor PagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Použití funkce `DbContextOptions` v testech jednotek dal umožňuje, aby každý test běžel atomicky s novou instancí databáze:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Každá testovací metoda ve `DataAccessLayerTest` třídě (*UnitTests/DataAccessLayerTest. cs*) se řídí podobným vzorem příkazu uspořádat – Act-Assert:

1. Uspořádat: databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.
1. ACT: test je proveden.
1. Assert: kontrolní výrazy jsou určeny k určení, zda je výsledek testu úspěch.

Například `DeleteMessageAsync` Metoda zodpovídá za odebrání jedné zprávy identifikované její `Id` (*Src/ Razor PagesTestSample/data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test kontroluje, zda metoda odstraní zprávu, když se zpráva nachází v databázi. Druhá metoda Testuje, že se databáze nemění, pokud zpráva `Id` pro odstranění neexistuje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`Metoda je zobrazena níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Nejprve metoda provede krok uspořádat, kde se provádí příprava pro krok Act. Zprávy o osazení se získávají a uchovávají v `seedMessages` . Počáteční zprávy se ukládají do databáze nástroje. Zpráva s objektem `Id` `1` je nastavena pro odstranění. Při `DeleteMessageAsync` spuštění metody musí mít očekávané zprávy všechny zprávy s výjimkou jednoho s `Id` `1` . `expectedMessages`Proměnná představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda funguje: `DeleteMessageAsync` Metoda je prováděna předáním v `recId` `1` :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec Metoda získá `Messages` z kontextu a porovná ho s `expectedMessages` kontrolním výrazem, že obě jsou stejné:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat tyto dvě `List<Message>` stejné:

* Zprávy jsou seřazeny podle `Id` .
* Páry zpráv jsou porovnány s `Text` vlastností.

Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` kontroluje výsledek pokusu o odstranění neexistující zprávy. V takovém případě by měly být očekávané zprávy v databázi rovny skutečným zprávám po `DeleteMessageAsync` provedení metody. Obsah databáze by neměl být změněn:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testování částí metod modelu stránky

Další sada testů jednotek zodpovídá za testy metod modelu stránky. V aplikaci zprávy jsou modely stránek indexu nalezeny ve `IndexModel` třídě v *Src/ Razor PagesTestSample/pages/index. cshtml. cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z DAL pro uživatelské rozhraní pomocí `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) platný, volání `AddMessageAsync` pro přidání zprávy do databáze. |
| `OnPostDeleteAllMessagesAsync` | Volá `DeleteAllMessagesAsync` se, aby se odstranily všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Provede `DeleteMessageAsync` odstranění zprávy se `Id` zadaným. |
| `OnPostAnalyzeMessagesAsync` | Pokud je v databázi jedna nebo více zpráv, vypočítá průměrný počet slov na jednu zprávu. |

Metody modelu stránky jsou testovány pomocí sedmi testů ve `IndexPageTests` třídě (*Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Testy používají známý vzor uspořádání a vyhodnocení – Act. Tyto testy se zaměřují na:

* Určení, zda metody dodrží správné chování, pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatné.
* Potvrzení metod vyprodukuje správné <xref:Microsoft.AspNetCore.Mvc.IActionResult> .
* Kontroluje se, jestli jsou přiřazení hodnot vlastností správně vytvořená.

Tato skupina testů často napodobuje metody DAL, aby vytvořila očekávaná data pro krok Act, kde je spuštěna metoda modelu stránky. Například `GetMessagesAsync` Metoda `AppDbContext` je napodobná, aby vytvořila výstup. Když metoda modelu stránky spustí tuto metodu, vrátí výsledek. Data nepocházejí z databáze. Tím se vytvoří předvídatelné a spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`Test ukazuje, jak je metoda popsána `GetMessagesAsync` pro model stránky:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Při `OnGetAsync` spuštění metody v kroku Act volá metodu modelu stránky `GetMessagesAsync` .

Krok testu jednotek krok (*Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`Metoda modelu stránky `OnGetAsync` (*Src/ Razor PagesTestSample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync`Metoda v metodě dal nevrací výsledek pro toto volání metody. Napodobovaná verze metody vrátí výsledek.

V `Assert` kroku jsou skutečné zprávy ( `actualMessages` ) přiřazeny z `Messages` vlastnosti modelu stránky. Při přiřazení zpráv se také provádí ověření typu. Očekávané a skutečné zprávy jsou porovnány podle jejich `Text` vlastností. Test vyhodnotí, že dvě `List<Message>` instance obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Jiné testy v této skupině vytvoří objekty modelu stránky, které obsahují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> , a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> , <xref:Microsoft.AspNetCore.Mvc.ActionContext> k vytvoření, a `PageContext` `ViewDataDictionary` `PageContext` . Ty jsou užitečné při provádění testů. Například aplikace zprávy naváže `ModelState` chybu s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> cílem ověřit, že <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> je při spuštění vrácena platná `OnPostAddMessageAsync` :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje informací

* [Testování částí C# v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
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

ASP.NET Core podporuje testování částí Razor aplikací Pages. Testy vrstvy přístupu k datům (DAL) a modelů stránek vám pomůžou zajistit:

* Části Razor aplikace stránky fungují nezávisle a společně jako jednotka během vytváření aplikací.
* Třídy a metody mají omezené obory zodpovědnosti.
* Další dokumentace existuje v tom, jak se má aplikace chovat.
* V průběhu automatizovaného sestavování a nasazování se nacházejí regrese, které se týkají chyb v rámci aktualizací kódu.

V tomto tématu se předpokládá, že máte základní znalosti o Razor aplikacích stránky a testování částí. Pokud nejste obeznámeni s Razor aplikacemi Pages nebo koncepty testování, přečtěte si následující témata:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testování částí C# v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Vzorový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace zprávy | *src/ Razor PagesTestSample*         | Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu). |
| Testovací aplikace    | *testuje/ Razor PagesTestSample. Tests* | Slouží k testování modelu DAL a stránky indexu aplikace zprávy. |

Testy lze spustit pomocí integrovaných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz na příkazovém řádku ve složce *Tests/ Razor PagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizace aplikace zprávy

Zpráva aplikace je Razor systém zpráv s následujícími charakteristikami:

* Stránka indexu aplikace (*pages/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (hledání průměrného počtu slov na jednu zprávu).
* Zpráva je popsána `Message` třídou (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva). `Text`Vlastnost je povinná a omezená na 200 znaků.
* Zprávy se ukládají pomocí&#8224; [databáze Entity Framework v paměti](/ef/core/providers/in-memory/) .
* Aplikace obsahuje ve své třídě kontext databáze DAL `AppDbContext` (*data/AppDbContext. cs*). Metody DAL jsou označeny `virtual` , což umožňuje napodobovat metody pro použití v testech.
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami. Tyto *osazené zprávy* se používají také v testech.

&#8224;tématu EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest. Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) . Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.

I když ukázková aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotka práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor stránky tyto vzory vývoje podporují. Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Organizace testovací aplikace

Testovací aplikace je Konzolová aplikace ve složce *Tests/ Razor PagesTestSample. Tests* .

| Složka testovací aplikace | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje testy jednotek pro dal.</li><li>*IndexPageTests.cs* obsahuje testy jednotek pro model stránky indexu.</li></ul> |
| *Nástroje*     | Obsahuje `TestDbContextOptions` metodu použitou k vytvoření nové možnosti kontextu databáze pro každý test jednotky dal, aby se databáze obnovila na svůj základní stav pro každý test. |

Testovací rozhraní je [xUnit](https://xunit.github.io/). Rozhraní objektu pro napodobení je [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testování částí vrstvy přístupu k datům (DAL)

Aplikace zprávy obsahuje DAL se čtyřmi metodami, které jsou obsaženy ve `AppDbContext` třídě (*Src/ Razor PagesTestSample/data/AppDbContext. cs*). Každá metoda má jednu nebo dvě testy jednotek v testovací aplikaci.

| DAL – metoda               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá `List<Message>` z databáze seřazené podle `Text` Vlastnosti. |
| `AddMessageAsync`        | Přidá do `Message` databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny `Message` položky z databáze.                           |
| `DeleteMessageAsync`     | Odstraní `Message` z databáze jednu z nich `Id` .                      |

Testování částí DAL vyžaduje <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test. Jedním ze způsobů, jak vytvořit `DbContextOptions` pro každý test, je použít <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu, který předchozí test opustil. To může být problematické při pokusu o zápis atomických testů, které mezi sebou nekolidují. Chcete-li vynutit `AppDbContext` použití nového kontextu databáze pro každý test, zadejte `DbContextOptions` instanci, která je založena na novém poskytovateli služeb. Testovací aplikace ukazuje, jak to provést pomocí `Utilities` metody třídy `TestDbContextOptions` (*Tests/ Razor PagesTestSample. Tests/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Použití funkce `DbContextOptions` v testech jednotek dal umožňuje, aby každý test běžel atomicky s novou instancí databáze:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Každá testovací metoda ve `DataAccessLayerTest` třídě (*UnitTests/DataAccessLayerTest. cs*) se řídí podobným vzorem příkazu uspořádat – Act-Assert:

1. Uspořádat: databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.
1. ACT: test je proveden.
1. Assert: kontrolní výrazy jsou určeny k určení, zda je výsledek testu úspěch.

Například `DeleteMessageAsync` Metoda zodpovídá za odebrání jedné zprávy identifikované její `Id` (*Src/ Razor PagesTestSample/data/AppDbContext. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test kontroluje, zda metoda odstraní zprávu, když se zpráva nachází v databázi. Druhá metoda Testuje, že se databáze nemění, pokud zpráva `Id` pro odstranění neexistuje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`Metoda je zobrazena níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Nejprve metoda provede krok uspořádat, kde se provádí příprava pro krok Act. Zprávy o osazení se získávají a uchovávají v `seedMessages` . Počáteční zprávy se ukládají do databáze nástroje. Zpráva s objektem `Id` `1` je nastavena pro odstranění. Při `DeleteMessageAsync` spuštění metody musí mít očekávané zprávy všechny zprávy s výjimkou jednoho s `Id` `1` . `expectedMessages`Proměnná představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda funguje: `DeleteMessageAsync` Metoda je prováděna předáním v `recId` `1` :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec Metoda získá `Messages` z kontextu a porovná ho s `expectedMessages` kontrolním výrazem, že obě jsou stejné:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat tyto dvě `List<Message>` stejné:

* Zprávy jsou seřazeny podle `Id` .
* Páry zpráv jsou porovnány s `Text` vlastností.

Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` kontroluje výsledek pokusu o odstranění neexistující zprávy. V takovém případě by měly být očekávané zprávy v databázi rovny skutečným zprávám po `DeleteMessageAsync` provedení metody. Obsah databáze by neměl být změněn:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testování částí metod modelu stránky

Další sada testů jednotek zodpovídá za testy metod modelu stránky. V aplikaci zprávy jsou modely stránek indexu nalezeny ve `IndexModel` třídě v *Src/ Razor PagesTestSample/pages/index. cshtml. cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z DAL pro uživatelské rozhraní pomocí `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) platný, volání `AddMessageAsync` pro přidání zprávy do databáze. |
| `OnPostDeleteAllMessagesAsync` | Volá `DeleteAllMessagesAsync` se, aby se odstranily všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Provede `DeleteMessageAsync` odstranění zprávy se `Id` zadaným. |
| `OnPostAnalyzeMessagesAsync` | Pokud je v databázi jedna nebo více zpráv, vypočítá průměrný počet slov na jednu zprávu. |

Metody modelu stránky jsou testovány pomocí sedmi testů ve `IndexPageTests` třídě (*Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*). Testy používají známý vzor uspořádání a vyhodnocení – Act. Tyto testy se zaměřují na:

* Určení, zda metody dodrží správné chování, pokud je [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) neplatné.
* Potvrzení metod vyprodukuje správné <xref:Microsoft.AspNetCore.Mvc.IActionResult> .
* Kontroluje se, jestli jsou přiřazení hodnot vlastností správně vytvořená.

Tato skupina testů často napodobuje metody DAL, aby vytvořila očekávaná data pro krok Act, kde je spuštěna metoda modelu stránky. Například `GetMessagesAsync` Metoda `AppDbContext` je napodobná, aby vytvořila výstup. Když metoda modelu stránky spustí tuto metodu, vrátí výsledek. Data nepocházejí z databáze. Tím se vytvoří předvídatelné a spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`Test ukazuje, jak je metoda popsána `GetMessagesAsync` pro model stránky:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Při `OnGetAsync` spuštění metody v kroku Act volá metodu modelu stránky `GetMessagesAsync` .

Krok testu jednotek krok (*Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`Metoda modelu stránky `OnGetAsync` (*Src/ Razor PagesTestSample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync`Metoda v metodě dal nevrací výsledek pro toto volání metody. Napodobovaná verze metody vrátí výsledek.

V `Assert` kroku jsou skutečné zprávy ( `actualMessages` ) přiřazeny z `Messages` vlastnosti modelu stránky. Při přiřazení zpráv se také provádí ověření typu. Očekávané a skutečné zprávy jsou porovnány podle jejich `Text` vlastností. Test vyhodnotí, že dvě `List<Message>` instance obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Jiné testy v této skupině vytvoří objekty modelu stránky, které obsahují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> , a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> , <xref:Microsoft.AspNetCore.Mvc.ActionContext> k vytvoření, a `PageContext` `ViewDataDictionary` `PageContext` . Ty jsou užitečné při provádění testů. Například aplikace zprávy naváže `ModelState` chybu s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> cílem ověřit, že <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> je při spuštění vrácena platná `OnPostAddMessageAsync` :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje informací

* [Testování částí C# v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Začínáme s xUnit.net: použití .NET Core s příkazovým řádkem sady .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Rychlý Start MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
* [JustMockLite](https://github.com/telerik/JustMockLite): podobná architektura pro vývojáře na platformě .NET. (*Společnost Microsoft nespravuje ani nepodporuje.*)

::: moniker-end
