---
uid: web-api/overview/testing-and-debugging/mocking-entity-framework-when-unit-testing-aspnet-web-api-2
title: Vytvoření modelu Entity Framework při testování rozhraní ASP.NET Web API 2 | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento průvodce a aplikace ukazují, jak vytvořit testy jednotek pro aplikace webového rozhraní API 2, která používá Entity Framework. Ukazuje, jak změnit...
ms.author: riande
ms.date: 12/13/2013
ms.assetid: cd844025-ccad-41ce-8694-595f1022a49f
msc.legacyurl: /web-api/overview/testing-and-debugging/mocking-entity-framework-when-unit-testing-aspnet-web-api-2
msc.type: authoredcontent
ms.openlocfilehash: f1799b3f9d698053c397e57da3f33ff900ec4013
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021024"
---
<a name="mocking-entity-framework-when-unit-testing-aspnet-web-api-2"></a>Vytvoření modelu Entity Framework při testování rozhraní ASP.NET Web API 2
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

[Stáhnout dokončený projekt](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11)

> Tento průvodce a aplikace ukazují, jak vytvořit testy jednotek pro aplikace webového rozhraní API 2, která používá Entity Framework. Ukazuje, jak změnit automaticky generovaný kontroleru k povolení předávání objekt kontextu pro testování a jak vytvořit testovací objekty, které pracují s Entity Framework.
>
> Úvod do testování jednotek v rozhraní ASP.NET Web API najdete v tématu [jednotkové testování v ASP.NET Web API 2](unit-testing-with-aspnet-web-api.md).
>
> Tento kurz předpokládá, že jste obeznámeni se základními koncepcemi rozhraní ASP.NET Web API. Úvodní tutoriál naleznete v tématu [Začínáme s ASP.NET Web API 2](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - Webové rozhraní API 2

## <a name="in-this-topic"></a>V tomto tématu

Toto téma obsahuje následující oddíly:

- [Požadované součásti](#prereqs)
- [Stáhnout kód](#download)
- [Vytvoření aplikace pomocí projektu testů jednotek](#appwithunittest)
- [Vytvoření tříd modelu](#modelclass)
- [Přidání kontroleru](#controller)
- [Přidat injektáž závislostí](#dependency)
- [Instalace balíčků NuGet v projektu testů](#testpackages)
- [Vytvoření kontextu testu](#testcontext)
- [Vytváření testů](#tests)
- [Spuštění testů](#runtests)

Pokud jste již dokončili kroky v [jednotkové testování v ASP.NET Web API 2](unit-testing-with-aspnet-web-api.md), můžete přeskočit k části [přidat kontroler](#controller).

<a id="prereqs"></a>
## <a name="prerequisites"></a>Požadavky

Visual Studio 2017 Community, Professional nebo Enterprise edition

<a id="download"></a>
## <a name="download-code"></a>Stáhnout kód

Stáhněte si [dokončený projekt](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11). Ke stažení projekt zahrnuje kód testu jednotek pro toto téma a [jednotky testování ASP.NET Web API 2](unit-testing-with-aspnet-web-api.md) tématu.

<a id="appwithunittest"></a>
## <a name="create-application-with-unit-test-project"></a>Vytvoření aplikace pomocí projektu testů jednotek

Můžete buď při vytváření aplikace vytvořit projekt testování částí nebo přidat projekt testování částí do stávající aplikace. Tento kurz ukazuje vytvoření projektu testů jednotek při vytváření aplikace.

Vytvořit novou webovou aplikaci ASP.NET s názvem **StoreApp**.

V oknech Nový projekt ASP.NET, vyberte **prázdný** šablony a přidat složky a základní odkazy pro webové rozhraní API. Vyberte **přidání jednotkových testů** možnost. Automaticky s názvem projektu testování částí **StoreApp.Tests**. Abyste mohli tento název.

![Vytvořte projekt testu jednotek](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image1.png)

Po vytvoření aplikace, zobrazí se obsahuje dva projekty – **StoreApp** a **StoreApp.Tests**.

<a id="modelclass"></a>
## <a name="create-the-model-class"></a>Vytvoření tříd modelu

V projektu StoreApp, přidejte soubor třídy do **modely** složku s názvem **Product.cs**. Obsah souboru nahraďte následujícím kódem.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample1.cs)]

Sestavte řešení.

<a id="controller"></a>
## <a name="add-the-controller"></a>Přidání kontroleru

Klikněte pravým tlačítkem na složku řadiče a vyberte **přidat** a **novou vygenerovanou položku**. Vyberte kontroler rozhraní Web API 2 s akcemi používající nástroj Entity Framework.

![Přidat nový kontroler](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image2.png)

Nastavte následující hodnoty:

- Název kontroleru: **ProductController**
- Třída modelu: **produktu**
- Třída kontextu dat: [vyberte **nový kontext dat** tlačítko, které vyplní hodnoty vidět níže]

![Zadejte kontroler](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image3.png)

Klikněte na tlačítko **přidat** vytvoření kontroleru s automaticky generovaným kódem. Tento kód obsahuje metody pro vytváření, načítání, aktualizaci a odstranění instancí třídy produktu. Následující kód ukazuje metody pro přidání produktu. Všimněte si, že metoda vrací instanci **IHttpActionResult**.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample2.cs)]

IHttpActionResult je jedním z nových funkcí ve webovém rozhraní API 2 a zjednodušuje vývoj jednotkových testů.

V další části, bude přizpůsobení generovaný kód pro usnadnění předávání objektů testu pro kontroler.

<a id="dependency"></a>
## <a name="add-dependency-injection"></a>Přidat injektáž závislostí

V současné době je třída ProductController pevně určený instance třídy StoreAppContext. Ke změně aplikace a odebrat dané pevně zakódované závislosti použijete vzor volá vkládání závislostí. Porušením této závislosti, můžete předat objekt mock při testování.

Klikněte pravým tlačítkem myši **modely** složky a přidejte nové rozhraní s názvem **IStoreAppContext**.

Nahraďte kód následujícím kódem.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample3.cs)]

Otevřete soubor StoreAppContext.cs a měnit následující zvýrazněný. Všimněte si důležité změny jsou:

- StoreAppContext třída implementuje rozhraní IStoreAppContext
- Metoda MarkAsModified je implementována.


[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample4.cs?highlight=6,14-17)]

Otevřete soubor ProductController.cs. Změna existujícího kódu tak, aby odpovídaly zvýrazněný kód. Tyto změny na StoreAppContext přerušit závislosti a jiné třídy a zajistěte tak předání jiný objekt třídy kontextu povolit. Tato změna vám umožní předat během testování částí v kontextu testu.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample5.cs?highlight=4,7-12)]

Existuje jeden další změny, které musíte udělat v ProductController. V **PutProduct** metody, nahraďte řádek, který nastaví stav entity upravit pomocí volání metody MarkAsModified.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample6.cs?highlight=14-15)]

Sestavte řešení.

Nyní jste připraveni nastavit testovacího projektu.

<a id="testpackages"></a>
## <a name="install-nuget-packages-in-test-project"></a>Instalace balíčků NuGet v projektu testů

Když použijete prázdnou šablonu k vytvoření aplikace, projekt testu jednotek (StoreApp.Tests) neobsahuje žádné nainstalované balíčky NuGet. Další šablony, jako je například šabloně webového rozhraní API patří některé balíčky NuGet v projektu testování částí. Pro účely tohoto kurzu musíte zahrnout balíčku Entity Framework a balíček Microsoft ASP.NET Web API 2 Core do testovacího projektu.

Klikněte pravým tlačítkem na projekt StoreApp.Tests a vyberte **spravovat balíčky NuGet**. Je nutné vybrat projekt StoreApp.Tests přidat balíčky pro tento projekt.

![Správa balíčků](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image4.png)

Z Online balíčků najít a nainstalovat balíček EntityFramework (verze 6.0 nebo novější). Pokud se zdá, že EntityFramework balíček je už nainstalovaný, možná jste vybrali StoreApp projektu namísto StoreApp.Tests projektu.

![Přidání Entity Framework](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image5.png)

Najít a nainstalovat balíček Microsoft ASP.NET Web API 2 jádra.

![Nainstalujte balíček základního webového rozhraní api](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image6.png)

Zavřete okno Spravovat balíčky NuGet.

<a id="testcontext"></a>
## <a name="create-test-context"></a>Vytvoření kontextu testu

Přidejte třídu pojmenovanou **TestDbSet** do testovacího projektu. Tato třída slouží jako základní třída pro vaši datovou sadu testů. Nahraďte kód následujícím kódem.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample7.cs)]

Přidejte třídu pojmenovanou **TestProductDbSet** do testovacího projektu, který obsahuje následující kód.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample8.cs)]

Přidejte třídu pojmenovanou **TestStoreAppContext** a nahraďte existující kód následujícím kódem.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample9.cs)]

<a id="tests"></a>
## <a name="create-tests"></a>Vytváření testů

Ve výchozím nastavení, testovací projekt obsahuje soubor prázdný test s názvem **UnitTest1.cs**. Tento soubor obsahuje atributy, že pomocí kterých lze vytvářet testovací metody. Pro účely tohoto kurzu můžete odstranit tento soubor, protože přidáte novou třídu testu.

Přidejte třídu pojmenovanou **TestProductController** do testovacího projektu. Nahraďte kód následujícím kódem.

[!code-csharp[Main](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/samples/sample10.cs)]

<a id="runtests"></a>
## <a name="run-tests"></a>Spouštění testů

Nyní jste připraveni ke spuštění testů. Všechny metody, která jsou označena **TestMethod** atribut bude ověřovat. Z **Test** položku nabídky, spusťte testy.

![Provádění testů](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image7.png)

Otevřít **Průzkumník testů** okně a Všimněte si, že výsledky testů.

![výsledky testu](mocking-entity-framework-when-unit-testing-aspnet-web-api-2/_static/image8.png)
