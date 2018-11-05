---
uid: web-api/overview/testing-and-debugging/unit-testing-with-aspnet-web-api
title: Testování rozhraní ASP.NET Web API 2 | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento průvodce a aplikace ukazují, jak vytvořit jednoduchý částí pro vaši aplikaci s webovým rozhraním API 2. Tento kurz ukazuje, jak zahrnout proj test jednotek...
ms.author: riande
ms.date: 06/05/2014
ms.assetid: bf20f78d-ff91-48be-abd1-88e23dcc70ba
msc.legacyurl: /web-api/overview/testing-and-debugging/unit-testing-with-aspnet-web-api
msc.type: authoredcontent
ms.openlocfilehash: 915610e6646ebe86dd8f16f290ecabd36bf7f48d
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020829"
---
<a name="unit-testing-aspnet-web-api-2"></a>Testování rozhraní ASP.NET Web API 2
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

[Stáhnout dokončený projekt](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11)

> Tento průvodce a aplikace ukazují, jak vytvořit jednoduchý částí pro vaši aplikaci s webovým rozhraním API 2. Tento kurz ukazuje, jak zahrnout projekt testu jednotek ve vašem řešení a zapisovat testovací metody, které zkontrolujte hodnoty vrácené z metody kontroleru.
>
> Tento kurz předpokládá, že jste obeznámeni se základními koncepcemi rozhraní ASP.NET Web API. Úvodní tutoriál naleznete v tématu [Začínáme s ASP.NET Web API 2](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).
>
> Testování částí v tomto tématu jsou záměrně omezené na jednoduché datové scénáře. Testování pokročilejší scénáře datových částí, naleznete v tématu [vytvoření modelu Entity Framework při jednotky testování ASP.NET Web API 2](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md).
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
    - [Přidejte projekt testu jednotek při vytváření aplikace](#whencreate)
    - [Přidat projekt testování částí do existující aplikace](#addtoexisting)
- [Nastavení aplikace webového rozhraní API 2](#setupproject)
- [Instalace balíčků NuGet v projektu testů](#testpackages)
- [Vytváření testů](#tests)
- [Spuštění testů](#runtests)

<a id="prereqs"></a>
## <a name="prerequisites"></a>Požadavky

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) Community, Professional nebo Enterprise edition

<a id="download"></a>
## <a name="download-code"></a>Stáhnout kód

Stáhněte si [dokončený projekt](https://code.msdn.microsoft.com/Unit-Testing-with-ASPNET-1374bc11). Ke stažení projekt zahrnuje kód testu jednotek pro toto téma a [vytvoření modelu Entity Framework při jednotky testování webového rozhraní API ASP.NET](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md) tématu.

<a id="appwithunittest"></a>
## <a name="create-application-with-unit-test-project"></a>Vytvoření aplikace pomocí projektu testů jednotek

Můžete buď při vytváření aplikace vytvořit projekt testování částí nebo přidat projekt testování částí do stávající aplikace. Tento kurz ukazuje obě metody pro vytvoření projektu testů jednotek. Chcete-li v tomto kurzu můžete použít kterýkoliv přístup.

<a id="whencreate"></a>
### <a name="add-unit-test-project-when-creating-the-application"></a>Přidejte projekt testu jednotek při vytváření aplikace

Vytvořit novou webovou aplikaci ASP.NET s názvem **StoreApp**.

![Vytvoření projektu](unit-testing-with-aspnet-web-api/_static/image1.png)

V oknech Nový projekt ASP.NET, vyberte **prázdný** šablony a přidat složky a základní odkazy pro webové rozhraní API. Vyberte **přidání jednotkových testů** možnost. Automaticky s názvem projektu testování částí **StoreApp.Tests**. Abyste mohli tento název.

![Vytvořte projekt testu jednotek](unit-testing-with-aspnet-web-api/_static/image2.png)

Po vytvoření aplikace, uvidíte, že obsahuje dva projekty.

![dva projekty](unit-testing-with-aspnet-web-api/_static/image3.png)

<a id="addtoexisting"></a>
### <a name="add-unit-test-project-to-an-existing-application"></a>Přidat projekt testování částí do existující aplikace

Pokud jste nevytvořili projekt testu jednotek při vytváření vaší aplikace, můžete přidat jednu kdykoli. Předpokládejme například, že již máte aplikaci s názvem StoreApp a chcete přidat testy jednotek. Chcete-li přidat projekt testování částí, klikněte pravým tlačítkem na řešení a vyberte **přidat** a **nový projekt**.

![Přidat nový projekt do řešení](unit-testing-with-aspnet-web-api/_static/image4.png)

Vyberte **testovací** v levém podokně a vyberte **projekt testu jednotek** pro typ projektu. Pojmenujte projekt **StoreApp.Tests**.

![přidejte projekt testu jednotek](unit-testing-with-aspnet-web-api/_static/image5.png)

Projekt testu jednotek se zobrazí ve vašem řešení.

V projektu testování částí přidejte odkaz na projekt do původního projektu.

<a id="setupproject"></a>
## <a name="set-up-the-web-api-2-application"></a>Nastavení aplikace webového rozhraní API 2

V projektu StoreApp, přidejte soubor třídy do **modely** složku s názvem **Product.cs**. Obsah souboru nahraďte následujícím kódem.

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample1.cs)]

Sestavte řešení.

Klikněte pravým tlačítkem na složku řadiče a vyberte **přidat** a **novou vygenerovanou položku**. Vyberte **rozhraní Web API 2 kontroler – prázdný**.

![Přidat nový kontroler](unit-testing-with-aspnet-web-api/_static/image6.png)

Nastavte název kontroleru **SimpleProductController**a klikněte na tlačítko **přidat**.

![Zadejte kontroler](unit-testing-with-aspnet-web-api/_static/image7.png)

Nahraďte stávající kód následujícím kódem. Pro zjednodušení tento příklad, jsou data uložená v seznamu, nikoli databázi. Seznam definované v této třídě představuje produkční data. Všimněte si, že kontroler obsahuje konstruktor, který jako parametr používá seznam objektů produktu. Tento konstruktor umožňuje předat testovacích dat při testování částí. Kontroler také zahrnuje dva **asynchronní** metody pro ilustraci asynchronních metod testování částí. Asynchronní metody byly implementované voláním **Task.FromResult** minimalizovat cizí kód, ale obvykle metody bude zahrnovat náročná operace.

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample2.cs)]

Metoda GetProduct vrátí instanci **IHttpActionResult** rozhraní. IHttpActionResult je jedním z nových funkcí ve webovém rozhraní API 2 a zjednodušuje vývoj jednotkových testů. Třídy, které implementují rozhraní IHttpActionResult se nacházejí v [System.Web.Http.Results](https://msdn.microsoft.com/library/system.web.http.results.aspx) oboru názvů. Tyto třídy představují možné odpovědi z požadavku akce a odpovídají stavové kódy HTTP.

Sestavte řešení.

Nyní jste připraveni nastavit testovacího projektu.

<a id="testpackages"></a>
## <a name="install-nuget-packages-in-test-project"></a>Instalace balíčků NuGet v projektu testů

Když použijete prázdnou šablonu k vytvoření aplikace, projekt testu jednotek (StoreApp.Tests) neobsahuje žádné nainstalované balíčky NuGet. Další šablony, jako je například šabloně webového rozhraní API patří některé balíčky NuGet v projektu testování částí. Pro účely tohoto kurzu musí zahrnovat balíček Microsoft ASP.NET Web API 2 Core do testovacího projektu.

Klikněte pravým tlačítkem na projekt StoreApp.Tests a vyberte **spravovat balíčky NuGet**. Je nutné vybrat projekt StoreApp.Tests přidat balíčky pro tento projekt.

![Správa balíčků](unit-testing-with-aspnet-web-api/_static/image8.png)

Najít a nainstalovat balíček Microsoft ASP.NET Web API 2 jádra.

![Nainstalujte balíček základního webového rozhraní api](unit-testing-with-aspnet-web-api/_static/image9.png)

Zavřete okno Spravovat balíčky NuGet.

<a id="tests"></a>
## <a name="create-tests"></a>Vytváření testů

Ve výchozím nastavení obsahuje soubor prázdný test s názvem UnitTest1.cs testovacího projektu. Tento soubor obsahuje atributy, že pomocí kterých lze vytvářet testovací metody. Pro testy jednotky můžete použít tento soubor nebo vytvořte svůj vlastní soubor.

![UnitTest1](unit-testing-with-aspnet-web-api/_static/image10.png)

V tomto kurzu vytvoříte testovací třídy. Můžete odstranit soubor UnitTest1.cs. Přidejte třídu pojmenovanou **TestSimpleProductController.cs**a nahraďte kód následujícím kódem.

[!code-csharp[Main](unit-testing-with-aspnet-web-api/samples/sample3.cs)]

<a id="runtests"></a>
## <a name="run-tests"></a>Spouštění testů

Nyní jste připraveni ke spuštění testů. Všechny metody, která jsou označena **TestMethod** atribut bude ověřovat. Z **Test** položku nabídky, spusťte testy.

![Provádění testů](unit-testing-with-aspnet-web-api/_static/image11.png)

Otevřít **Průzkumník testů** okně a Všimněte si, že výsledky testů.

![výsledky testu](unit-testing-with-aspnet-web-api/_static/image12.png)

## <a name="summary"></a>Souhrn

Dokončili jste tento kurz. Data v tomto kurzu se záměrně zjednodušená zaměřit se na testování podmínek. Testování pokročilejší scénáře datových částí, naleznete v tématu [vytvoření modelu Entity Framework při jednotky testování ASP.NET Web API 2](mocking-entity-framework-when-unit-testing-aspnet-web-api-2.md).
