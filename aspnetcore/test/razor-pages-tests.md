---
title: Testování částí stránky Razor v ASP.NET Core
author: guardrex
description: Zjistěte, jak vytvořit testy jednotek pro aplikace stránky Razor.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f89b4fcb0065e725f70deec7859e373f9158b4bd
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622782"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Testování částí stránky Razor v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

ASP.NET Core podporuje testů jednotek aplikací pro stránky Razor. Testy dat přístup layer (DAL) a zajištění modely stránky:

* Součástí aplikace Razor Pages pracovat nezávisle a společně jako celek během vytváření aplikace.
* Třídy a metody mají omezenou obory odpovědnosti.
* Další dokumentaci k existuje v chování aplikace.
* Regrese, které jsou způsobené aktualizace s kódem chyby, byly nalezeny během automatické vytváření a nasazení.

Toto téma předpokládá, že máte základní znalosti o aplikace Razor Pages a testy jednotek. Pokud nejste obeznámeni s Razor Pages aplikace nebo koncepty testu, naleznete v následujících tématech:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testování jednotek C# v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukázkový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace zprávy | *src/RazorPagesTestSample*         | Umožňuje uživateli přidat zprávu, odstraňte jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (Najít průměrný počet slov za zprávy). |
| Test aplikace    | *tests/RazorPagesTestSample.Tests* | Používá k testování částí vrstvy DAL a model Index stránky zprávy aplikace. |

Testy můžete spustit pomocí integrované testovací funkce integrované vývojové prostředí, například [sady Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazového řádku, spusťte následující příkaz na příkazovém řádku v *tests/RazorPagesTestSample.Tests* složky:

```console
dotnet test
```

## <a name="message-app-organization"></a>Zpráva aplikace organizace

Aplikace zprávy je zpráva systém stránky Razor s následujícími charakteristikami:

* Index stránky aplikace (*Pages/Index.cshtml* a *Pages/Index.cshtml.cs*) poskytuje uživatelské rozhraní a stránky model metody řídit přidání, odstranění a analýzy (Najít průměrný počet zpráv slov za zprávy).
* Zprávu je popsán `Message` třídy (*Data/Message.cs*) s dvě vlastnosti: `Id` (klíč) a `Text` (zprávy). `Text` Vlastnost je požadováno a omezené na 200 znaků.
* Zprávy jsou bezpečně uložené pomocí [databáze v paměti rozhraní Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* Aplikace obsahuje DAL ve své třídě kontext databáze `AppDbContext` (*Data/AppDbContext.cs*). DAL metody jsou označeny `virtual`, což umožňuje napodobování metody pro použití v testech.
* Pokud je při spuštění aplikace prázdné databáze, úložiště zpráv je inicializován pomocí tří zpráv. Tyto *nasadí zprávy* se také používají v testech.

&#8224;Téma EF [Test s InMemory](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy s použitím MSTest. Toto téma používá [xUnit](https://xunit.github.io/) rozhraní pro testování. Koncepty testu a testovací implementace napříč různými testovacími architektury jsou podobné, ale nejsou identické.

I když se nepoužívá model úložiště ukázkové aplikace a není efektivní příklad [pracovní jednotka (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), stránky Razor podporuje tyto způsoby vývoje. Další informace najdete v tématu [návrh vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Testování aplikace organizace

Aplikace testů je konzolová aplikace uvnitř *tests/RazorPagesTestSample.Tests* složky.

| Složky aplikace testu | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje testy jednotek pro vrstvy DAL.</li><li>*IndexPageTests.cs* obsahuje testy jednotek pro model Index stránky.</li></ul> |
| *Nástroje*     | Obsahuje `TestDbContextOptions` metodu použitou k vytvoření nové databáze možnosti kontextu pro každý Jednotkový test DAL tak, aby se obnovení databáze do stavu směrný plán pro každý test. |

Je rozhraní pro testování [xUnit](https://xunit.github.io/). Objekt napodobování framework [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testování částí dat přístup layer (DAL)

Zpráva k němu má aplikace DAL se čtyři metody obsažené v `AppDbContext` třídy (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Každá metoda má jeden nebo dva testování částí v aplikaci test.

| DAL – metoda               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá `List<Message>` z databáze, seřazené podle `Text` vlastnost. |
| `AddMessageAsync`        | Přidá `Message` do databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny `Message` položky z databáze.                           |
| `DeleteMessageAsync`     | Odstraní jeden `Message` z databáze pomocí `Id`.                      |

Testy jednotek DAL vyžadují <xref:Microsoft.EntityFrameworkCore.DbContextOptions> při vytváření nového `AppDbContext` pro každý test. Jedním z přístupů k vytváření `DbContextOptions` pro každý test, je použít <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test přijme databáze v libovolné stavu předchozí test nacházela. To může být problematické, při pokusu o zápis atomickou jednotku testy, které není konfliktu mezi sebou. K vynucení `AppDbContext` pro každý test použít nový kontext databáze, zadejte `DbContextOptions` instanci, která je založena na nového poskytovatele služby. Test aplikace ukazuje, jak to udělat pomocí jeho `Utilities` metoda třídy `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Použití `DbContextOptions` v jednotce DAL testů umožňuje každého testu ke spuštění atomicky s novou databází instancí:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Jednotlivých testovacích metod v `DataAccessLayerTest` třídy (*UnitTests/DataAccessLayerTest.cs*) používá podobně jako kontrolní výraz uspořádat Act vzor:

1. Uspořádání: Databáze je nakonfigurovaná pro test a/nebo si očekávaný výsledek je definován.
1. Akce: Spuštění testu.
1. Vyhodnocení: Kontrolní výrazy byly k určení, zda výsledek testu je úspěšné.

Například `DeleteMessageAsync` metoda je zodpovědná za odebrání jedné zprávy identifikován jeho `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test ověří, že metoda odstraní zprávu při zprávy se nachází v databázi. Další metoda testy, které databázi se nemění, pokud zpráva `Id` pro odstranění neexistuje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` Metody jsou uvedené níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Nejprve provádí metoda uspořádat krok, kde probíhá příprava na krok Act. Získat a uchovávat v datovém typu osazení zpráv `seedMessages`. Osazení zprávy se uloží do databáze. Zpráva s `Id` z `1` nastavený pro odstranění. Když `DeleteMessageAsync` provedení metody, očekávané zprávy musí mít všechny zprávy s kategorií s výjimkou `Id` z `1`. `expectedMessages` Proměnná představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda funguje: `DeleteMessageAsync` Provedení metody předáním `recId` z `1`:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec získá metodu `Messages` z kontextu a porovná ji `expectedMessages` potvrzující, že jsou dva stejné:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat, který dva `List<Message>` jsou stejné:

* Zprávy jsou řazeny podle `Id`.
* Dvojice zprávy jsou porovnány na `Text` vlastnost.

Podobně jako testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` zkontroluje výsledek pokusu o odstranění zprávy, která neexistuje. V takovém případě musí být roven skutečné zprávy po očekávané zprávy v databázi `DeleteMessageAsync` provedení metody. Měla by existovat bez nutnosti změn obsahu databáze:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testy jednotek metody modelu stránky

Další sady testů jednotek je zodpovědná za testy model metody stránky. V aplikaci zprávy jsou součástí modely Index stránky `IndexModel` třídy v *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z vrstvy DAL pomocí uživatelského rozhraní `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Pokud [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) je platná, volá `AddMessageAsync` k přidání zprávy do databáze. |
| `OnPostDeleteAllMessagesAsync` | Volání `DeleteAllMessagesAsync` odstranit všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Spustí `DeleteMessageAsync` se má odstranit zpráva s `Id` zadané. |
| `OnPostAnalyzeMessagesAsync` | Pokud jeden nebo více zpráv v databázi, vypočítá průměrný počet slov za zprávy. |

Model metody stránky jsou testovány pomocí sedm testy v `IndexPageTests` třídy (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Testy pomocí známých uspořádat vyhodnocení Act vzor. Zaměřte se na tyto testy:

* Určení, pokud podle metody správné chování při [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) je neplatný.
* Potvrzují se metody vytvoření správné <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Kontroluje se, že jsou správně provedené přiřazení hodnoty vlastnosti.

Tato skupina testy často napodobení metody vrstvy DAL k vytvoření očekávaná data Act krok, kde provedení metody modelu stránky. Například `GetMessagesAsync` metodu `AppDbContext` imitace výstup. Jakmile model metoda stránky spustí tuto metodu, model vrátí výsledek. Data nepochází od databáze. Tím se vytvoří předvídatelný a spolehlivý testovací podmínky pro použití vrstvy DAL v testech modelu stránky.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` Testování ukazuje jak `GetMessagesAsync` metoda je imitace modelu stránky:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Když `OnGetAsync` provedení metody v kroku Act, volá model stránky `GetMessagesAsync` metody.

Act krok testu jednotek (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` model stránky `OnGetAsync` – metoda (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync` Metoda ve DAL nevrací výsledek pro toto volání metody. Imitaci verzi metody vrátí výsledek.

V `Assert` krok, skutečné zprávy (`actualMessages`) se přidělují `Messages` vlastnost modelu stránky. Kontrola typu se také provádí při zprávy jsou přiřazeny. Očekávaných a aktuálních zpráv jsou porovnány pomocí jejich `Text` vlastnosti. Test, který vyhodnotí dva `List<Message>` instance obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Ostatní testy v této skupině vytvořit stránku objekty modelu, které zahrnují <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext> navázat `PageContext`, `ViewDataDictionary`a `PageContext`. Toto jsou užitečné při provádění testů. Například vytváří aplikace zprávy `ModelState` chyba s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> zkontroluje, jestli platný <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> je vrácena, pokud `OnPostAddMessageAsync` spuštění:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje

* [Testování jednotek C# v .NET Core pomocí příkazu dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Začínáme s xUnit.net: Použití .NET Core z příkazového řádku sady .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Rychlý start Moq](https://github.com/Moq/moq4/wiki/Quickstart)
