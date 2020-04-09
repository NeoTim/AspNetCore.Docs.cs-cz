---
title: Razor Pages jednotkové testy v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak vytvořit testy částí pro aplikace Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664407"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Razor Pages jednotkové testy v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core podporuje testování částí aplikací Razor Pages. Testy vrstvy přístupu k datům (DAL) a modelů stránek pomáhají zajistit:

* Části aplikace Razor Pages fungují nezávisle a společně jako celek během výstavby aplikace.
* Třídy a metody mají omezený rozsah odpovědnosti.
* Další dokumentace existuje o tom, jak by se měla aplikace chovat.
* Regrese, což jsou chyby způsobené aktualizacemi kódu, se nacházejí během automatizovaného vytváření a nasazování.

Toto téma předpokládá, že máte základní znalosti o aplikacích Razor Pages a testování částí. Pokud nejste obeznámeni s aplikacemi Razor Pages nebo testovacími koncepty, podívejte se na následující témata:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testování částí C# v .NET Core pomocí dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázkový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace Zprávy | *src/RazorPagesTestVzorek*         | Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu). |
| Testovací aplikace    | *testy/RazorPagesTestSample.Testy* | Slouží k testování částí dal a index modelu stránky zprávy aplikace. |

Testy lze spustit pomocí předdefinovaných testovacích funkcí rozhraní IDE, jako je [například Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [kód sady Visual Studio](https://code.visualstudio.com/) nebo příkazový řádek, proveďte následující příkaz na příkazovém řádku ve složce *Tests/RazorPagesTestSample.Tests:*

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizace aplikace Zprávy

Aplikace pro zprávy je systém zpráv Razor Pages s následujícími charakteristikami:

* Stránka Index aplikace (*Pages/Index.cshtml* a *Pages/Index.cshtml.cs*) poskytuje metody modelu ui a stránky pro řízení sčítání, mazání a analýzy zpráv (najděte průměrný počet slov na zprávu).
* Zpráva je popsána `Message` třídou (*Data/Message.cs*) `Id` se dvěma vlastnostmi: (klíč) a `Text` (zpráva). Ubytovací `Text` zařízení je povinné a je omezeno na 200 znaků.
* Zprávy jsou uloženy pomocí [entity framework u databáze v paměti](/ef/core/providers/in-memory/)&#8224;.
* Aplikace obsahuje DAL ve své databázi kontextu třídy `AppDbContext` (*Data/AppDbContext.cs*). Dal metody jsou `virtual`označeny , což umožňuje zesměšňování metody pro použití v testech.
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami. Tyto *osývané zprávy* se také používají v testech.

&#8224;Téma EF [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy s MSTest. Toto téma používá rozhraní [xUnit](https://xunit.github.io/) test. Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou identické.

I když ukázková aplikace nepoužívá vzor úložiště a není účinným příkladem [vzoru jednotky práce (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages podporuje tyto vzory vývoje. Další informace naleznete [v tématu Návrh vrstvy trvalost infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Testování organizace aplikace

Testovací aplikace je konzolová aplikace uvnitř *složky tests/RazorPagesTestSample.Tests.*

| Testování složky aplikace | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje jednotkové testy pro DAL.</li><li>*IndexPageTests.cs* obsahuje testy částí pro model stránky Index.</li></ul> |
| *Nástroje*     | Obsahuje `TestDbContextOptions` metodu použitou k vytvoření nové možnosti kontextu databáze pro každý test jednotky DAL tak, aby databáze je obnovena na základní podmínku pro každý test. |

Testovací rámec je [xUnit](https://xunit.github.io/). Objekt zesměšňovat rámec je [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Jednotkové testy vrstvy přístupu k datům (DAL)

Aplikace pro zprávy má DAL se čtyřmi `AppDbContext` metodami obsaženými ve třídě *(src/RazorPagesTestSample/Data/AppDbContext.cs).* Každá metoda má jeden nebo dva testy částí v testovací aplikaci.

| Metoda DAL               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá `List<Message>` z databáze seřazené podle vlastnosti. `Text` |
| `AddMessageAsync`        | Přidá `Message` a do databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny `Message` položky z databáze.                           |
| `DeleteMessageAsync`     | Odstraní jeden `Message` z databáze `Id`podle .                      |

Jednotkové testy DAL <xref:Microsoft.EntityFrameworkCore.DbContextOptions> vyžadují při `AppDbContext` vytváření nového pro každý test. Jeden přístup k `DbContextOptions` vytvoření pro každý <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>test je použít :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu předchozí test opustil. To může být problematické při pokusu o zápis testů atomických částí, které vzájemně nekolidčují. Chcete-li `AppDbContext` vynutit použití nového kontextu `DbContextOptions` databáze pro každý test, zajistěte instanci, která je založena na novém poskytovateli služeb. Testovací aplikace ukazuje, jak to `Utilities` udělat `TestDbContextOptions` pomocí metody třídy *(tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Pomocí `DbContextOptions` v jednotkových testů DAL umožňuje každý test spustit atomicky s instancí nové databáze:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Každá testovací metoda `DataAccessLayerTest` ve třídě *(UnitTests/DataAccessLayerTest.cs*) následuje podobný vzor Arrange-Act-Assert:

1. Uspořádat: Databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.
1. Akt: Test je proveden.
1. Assert: Kontrolní výrazy jsou provedeny k určení, pokud výsledek testu je úspěch.

Metoda je `DeleteMessageAsync` například zodpovědná za odebrání jediné `Id` zprávy identifikované jejími (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test zkontroluje, že metoda odstraní zprávu, když je zpráva přítomna v databázi. Druhá metoda testuje, že databáze nezmění, `Id` pokud zpráva k odstranění neexistuje. Metoda `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` je uvedena níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Za prvé, metoda provádí Uspořádat krok, kde příprava na krok zákona probíhá. Výsevní zprávy jsou získány a uchovávány v `seedMessages`. Výsev zprávy jsou uloženy do databáze. Zpráva s `Id` of `1` je nastavena k odstranění. Při `DeleteMessageAsync` spuštění metody očekávané zprávy by měly mít všechny zprávy s `Id` `1`výjimkou jedné s . Proměnná `expectedMessages` představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda působí: `DeleteMessageAsync` Metoda je provedena předávání `recId` `1`v :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec metoda získá `Messages` z kontextu a porovná ji `expectedMessages` s tvrzením, že dva jsou stejné:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat, že dva `List<Message>` jsou stejné:

* Zprávy jsou seřazeny podle . `Id`
* Dvojice zpráv jsou porovnány `Text` na vlastnost.

Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` zkontroluje výsledek pokusu o odstranění zprávy, která neexistuje. V tomto případě očekávané zprávy v databázi by měla `DeleteMessageAsync` být rovna skutečné zprávy po spuštění metody. Obsah databáze by se neměl měnit:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Jednotkové testy metod modelu stránky

Další sada testů částí je zodpovědná za testy metod modelu stránky. V aplikaci zprávy se modely stránek `IndexModel` Index nacházejí ve třídě v *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z DAL pro ui `GetMessagesAsync` pomocí metody. |
| `OnPostAddMessageAsync` | Pokud [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) je platný, volá `AddMessageAsync` přidat zprávu do databáze. |
| `OnPostDeleteAllMessagesAsync` | Volání `DeleteAllMessagesAsync` odstranit všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Spustí `DeleteMessageAsync` odstranění zprávy se `Id` zadaným. |
| `OnPostAnalyzeMessagesAsync` | Pokud jedna nebo více zpráv jsou v databázi, vypočítá průměrný počet slov na zprávu. |

Metody modelu stránky jsou testovány pomocí `IndexPageTests` sedmi testů ve třídě *(tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs).* Testy používají známý Arrange-Assert-Act vzor. Tyto testy se zaměřují na:

* Určení, pokud metody postupujte podle správného chování, když [modelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) je neplatný.
* Potvrzení metody vyrábět správné <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Kontrola, zda jsou přiřazení hodnoty vlastnosti provedena správně.

Tato skupina testů často zesměšňovat metody DAL k vytvoření očekávaných dat pro krok Act, kde je spuštěna metoda modelu stránky. Například `GetMessagesAsync` metoda `AppDbContext` je zesměšňován k výrobě výstupu. Když metoda modelu stránky spustí tuto metodu, mock vrátí výsledek. Data nepocházejí z databáze. To vytvoří předvídatelné, spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.

Test `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` ukazuje, `GetMessagesAsync` jak je metoda zesměšňována pro model stránky:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Když `OnGetAsync` je metoda spuštěna v kroku Act, volá `GetMessagesAsync` metodu modelu stránky.

Krok zákona o zkoušce částí *(testy/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage``OnGetAsync` metoda modelu stránky (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Metoda `GetMessagesAsync` v DAL nevrátí výsledek pro volání této metody. Zesměšňovaná verze metody vrátí výsledek.

V `Assert` kroku jsou skutečné`actualMessages`zprávy ( ) `Messages` přiřazeny z vlastnosti modelu stránky. Kontrola typu se provádí také při přiřazení zpráv. Očekávané a skutečné zprávy jsou `Text` porovnány podle jejich vlastností. Test tvrdí, že `List<Message>` dvě instance obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Další testy v této skupině vytvořit <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>objekty <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>modelu <xref:Microsoft.AspNetCore.Mvc.ActionContext> stránky, `PageContext`které `ViewDataDictionary`zahrnují `PageContext`, , a vytvořit , a , a . Ty jsou užitečné při provádění testů. Například aplikace zprávy vytvoří `ModelState` chybu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> s zkontrolovat, <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> že platný `OnPostAddMessageAsync` je vrácena při spuštění:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje

* [Testování částí C# v .NET Core pomocí dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Začínáme s xUnit.net: Použití rozhraní .NET Core s příkazovým řádkem .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq řekl:](https://github.com/moq/moq4)
* [Rychlý start společnosti Moq](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core podporuje testování částí aplikací Razor Pages. Testy vrstvy přístupu k datům (DAL) a modelů stránek pomáhají zajistit:

* Části aplikace Razor Pages fungují nezávisle a společně jako celek během výstavby aplikace.
* Třídy a metody mají omezený rozsah odpovědnosti.
* Další dokumentace existuje o tom, jak by se měla aplikace chovat.
* Regrese, což jsou chyby způsobené aktualizacemi kódu, se nacházejí během automatizovaného vytváření a nasazování.

Toto téma předpokládá, že máte základní znalosti o aplikacích Razor Pages a testování částí. Pokud nejste obeznámeni s aplikacemi Razor Pages nebo testovacími koncepty, podívejte se na následující témata:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testování částí C# v .NET Core pomocí dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázkový projekt se skládá ze dvou aplikací:

| Aplikace         | Složka projektu                     | Popis |
| ----------- | ---------------------------------- | ----------- |
| Aplikace Zprávy | *src/RazorPagesTestVzorek*         | Umožňuje uživateli přidat zprávu, odstranit jednu zprávu, odstranit všechny zprávy a analyzovat zprávy (najít průměrný počet slov na zprávu). |
| Testovací aplikace    | *testy/RazorPagesTestSample.Testy* | Slouží k testování částí dal a index modelu stránky zprávy aplikace. |

Testy lze spustit pomocí předdefinovaných testovacích funkcí rozhraní IDE, jako je [například Visual Studio](/visualstudio/test/unit-test-your-code) nebo [Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Pokud používáte [kód sady Visual Studio](https://code.visualstudio.com/) nebo příkazový řádek, proveďte následující příkaz na příkazovém řádku ve složce *Tests/RazorPagesTestSample.Tests:*

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizace aplikace Zprávy

Aplikace pro zprávy je systém zpráv Razor Pages s následujícími charakteristikami:

* Stránka Index aplikace (*Pages/Index.cshtml* a *Pages/Index.cshtml.cs*) poskytuje metody modelu ui a stránky pro řízení sčítání, mazání a analýzy zpráv (najděte průměrný počet slov na zprávu).
* Zpráva je popsána `Message` třídou (*Data/Message.cs*) `Id` se dvěma vlastnostmi: (klíč) a `Text` (zpráva). Ubytovací `Text` zařízení je povinné a je omezeno na 200 znaků.
* Zprávy jsou uloženy pomocí [entity framework u databáze v paměti](/ef/core/providers/in-memory/)&#8224;.
* Aplikace obsahuje DAL ve své databázi kontextu třídy `AppDbContext` (*Data/AppDbContext.cs*). Dal metody jsou `virtual`označeny , což umožňuje zesměšňování metody pro použití v testech.
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami. Tyto *osývané zprávy* se také používají v testech.

&#8224;Téma EF [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy s MSTest. Toto téma používá rozhraní [xUnit](https://xunit.github.io/) test. Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou identické.

I když ukázková aplikace nepoužívá vzor úložiště a není účinným příkladem [vzoru jednotky práce (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages podporuje tyto vzory vývoje. Další informace naleznete [v tématu Návrh vrstvy trvalost infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a <xref:mvc/controllers/testing> (ukázka implementuje vzor úložiště).

## <a name="test-app-organization"></a>Testování organizace aplikace

Testovací aplikace je konzolová aplikace uvnitř *složky tests/RazorPagesTestSample.Tests.*

| Testování složky aplikace | Popis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* obsahuje jednotkové testy pro DAL.</li><li>*IndexPageTests.cs* obsahuje testy částí pro model stránky Index.</li></ul> |
| *Nástroje*     | Obsahuje `TestDbContextOptions` metodu použitou k vytvoření nové možnosti kontextu databáze pro každý test jednotky DAL tak, aby databáze je obnovena na základní podmínku pro každý test. |

Testovací rámec je [xUnit](https://xunit.github.io/). Objekt zesměšňovat rámec je [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Jednotkové testy vrstvy přístupu k datům (DAL)

Aplikace pro zprávy má DAL se čtyřmi `AppDbContext` metodami obsaženými ve třídě *(src/RazorPagesTestSample/Data/AppDbContext.cs).* Každá metoda má jeden nebo dva testy částí v testovací aplikaci.

| Metoda DAL               | Funkce                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Získá `List<Message>` z databáze seřazené podle vlastnosti. `Text` |
| `AddMessageAsync`        | Přidá `Message` a do databáze.                                          |
| `DeleteAllMessagesAsync` | Odstraní všechny `Message` položky z databáze.                           |
| `DeleteMessageAsync`     | Odstraní jeden `Message` z databáze `Id`podle .                      |

Jednotkové testy DAL <xref:Microsoft.EntityFrameworkCore.DbContextOptions> vyžadují při `AppDbContext` vytváření nového pro každý test. Jeden přístup k `DbContextOptions` vytvoření pro každý <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>test je použít :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problém s tímto přístupem je, že každý test obdrží databázi v jakémkoli stavu předchozí test opustil. To může být problematické při pokusu o zápis testů atomických částí, které vzájemně nekolidčují. Chcete-li `AppDbContext` vynutit použití nového kontextu `DbContextOptions` databáze pro každý test, zajistěte instanci, která je založena na novém poskytovateli služeb. Testovací aplikace ukazuje, jak to `Utilities` udělat `TestDbContextOptions` pomocí metody třídy *(tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Pomocí `DbContextOptions` v jednotkových testů DAL umožňuje každý test spustit atomicky s instancí nové databáze:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Každá testovací metoda `DataAccessLayerTest` ve třídě *(UnitTests/DataAccessLayerTest.cs*) následuje podobný vzor Arrange-Act-Assert:

1. Uspořádat: Databáze je nakonfigurována pro test a/nebo je definován očekávaný výsledek.
1. Akt: Test je proveden.
1. Assert: Kontrolní výrazy jsou provedeny k určení, pokud výsledek testu je úspěch.

Metoda je `DeleteMessageAsync` například zodpovědná za odebrání jediné `Id` zprávy identifikované jejími (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Existují dva testy pro tuto metodu. Jeden test zkontroluje, že metoda odstraní zprávu, když je zpráva přítomna v databázi. Druhá metoda testuje, že databáze nezmění, `Id` pokud zpráva k odstranění neexistuje. Metoda `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` je uvedena níže:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Za prvé, metoda provádí Uspořádat krok, kde příprava na krok zákona probíhá. Výsevní zprávy jsou získány a uchovávány v `seedMessages`. Výsev zprávy jsou uloženy do databáze. Zpráva s `Id` of `1` je nastavena k odstranění. Při `DeleteMessageAsync` spuštění metody očekávané zprávy by měly mít všechny zprávy s `Id` `1`výjimkou jedné s . Proměnná `expectedMessages` představuje tento očekávaný výsledek.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda působí: `DeleteMessageAsync` Metoda je provedena předávání `recId` `1`v :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Nakonec metoda získá `Messages` z kontextu a porovná ji `expectedMessages` s tvrzením, že dva jsou stejné:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Aby bylo možné porovnat, že dva `List<Message>` jsou stejné:

* Zprávy jsou seřazeny podle . `Id`
* Dvojice zpráv jsou porovnány `Text` na vlastnost.

Podobná testovací metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` zkontroluje výsledek pokusu o odstranění zprávy, která neexistuje. V tomto případě očekávané zprávy v databázi by měla `DeleteMessageAsync` být rovna skutečné zprávy po spuštění metody. Obsah databáze by se neměl měnit:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Jednotkové testy metod modelu stránky

Další sada testů částí je zodpovědná za testy metod modelu stránky. V aplikaci zprávy se modely stránek `IndexModel` Index nacházejí ve třídě v *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Metoda modelu stránky | Funkce |
| ----------------- | -------- |
| `OnGetAsync` | Získá zprávy z DAL pro ui `GetMessagesAsync` pomocí metody. |
| `OnPostAddMessageAsync` | Pokud [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) je platný, volá `AddMessageAsync` přidat zprávu do databáze. |
| `OnPostDeleteAllMessagesAsync` | Volání `DeleteAllMessagesAsync` odstranit všechny zprávy v databázi. |
| `OnPostDeleteMessageAsync` | Spustí `DeleteMessageAsync` odstranění zprávy se `Id` zadaným. |
| `OnPostAnalyzeMessagesAsync` | Pokud jedna nebo více zpráv jsou v databázi, vypočítá průměrný počet slov na zprávu. |

Metody modelu stránky jsou testovány pomocí `IndexPageTests` sedmi testů ve třídě *(tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs).* Testy používají známý Arrange-Assert-Act vzor. Tyto testy se zaměřují na:

* Určení, pokud metody postupujte podle správného chování, když [modelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) je neplatný.
* Potvrzení metody vyrábět správné <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Kontrola, zda jsou přiřazení hodnoty vlastnosti provedena správně.

Tato skupina testů často zesměšňovat metody DAL k vytvoření očekávaných dat pro krok Act, kde je spuštěna metoda modelu stránky. Například `GetMessagesAsync` metoda `AppDbContext` je zesměšňován k výrobě výstupu. Když metoda modelu stránky spustí tuto metodu, mock vrátí výsledek. Data nepocházejí z databáze. To vytvoří předvídatelné, spolehlivé testovací podmínky pro použití DAL v testech modelu stránky.

Test `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` ukazuje, `GetMessagesAsync` jak je metoda zesměšňována pro model stránky:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Když `OnGetAsync` je metoda spuštěna v kroku Act, volá `GetMessagesAsync` metodu modelu stránky.

Krok zákona o zkoušce částí *(testy/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage``OnGetAsync` metoda modelu stránky (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Metoda `GetMessagesAsync` v DAL nevrátí výsledek pro volání této metody. Zesměšňovaná verze metody vrátí výsledek.

V `Assert` kroku jsou skutečné`actualMessages`zprávy ( ) `Messages` přiřazeny z vlastnosti modelu stránky. Kontrola typu se provádí také při přiřazení zpráv. Očekávané a skutečné zprávy jsou `Text` porovnány podle jejich vlastností. Test tvrdí, že `List<Message>` dvě instance obsahují stejné zprávy.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Další testy v této skupině vytvořit <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>objekty <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>modelu <xref:Microsoft.AspNetCore.Mvc.ActionContext> stránky, `PageContext`které `ViewDataDictionary`zahrnují `PageContext`, , a vytvořit , a , a . Ty jsou užitečné při provádění testů. Například aplikace zprávy vytvoří `ModelState` chybu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> s zkontrolovat, <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> že platný `OnPostAddMessageAsync` je vrácena při spuštění:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Další zdroje

* [Testování částí C# v .NET Core pomocí dotnet test a xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testování částí kódu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Vytvoření kompletního řešení .NET Core v systému macOS pomocí sady Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Začínáme s xUnit.net: Použití rozhraní .NET Core s příkazovým řádkem .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq řekl:](https://github.com/moq/moq4)
* [Rychlý start společnosti Moq](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
