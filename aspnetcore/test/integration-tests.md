---
title: Integrační testy v ASP.NET Core
author: guardrex
description: Zjistěte, jak zkoušky integrace Ujistěte se, že součásti vaší aplikace správně fungovat na úrovni infrastruktury, včetně databáze, systém souborů a sítě.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: test/integration-tests
ms.openlocfilehash: 053713e148df70b0be6bb567b55b2381a78d6c3e
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833576"
---
# <a name="integration-tests-in-aspnet-core"></a>Integrační testy v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex) a [Steve Smith](https://ardalis.com/)

Integrační testy Ujistěte se, že součásti vaší aplikace správně fungovat na úrovni, která zahrnuje podpůrnou infrastrukturu aplikace, jako jsou databáze, systém souborů a sítě. ASP.NET Core podporuje integrace testů pomocí rámce jednotkových testů pomocí hostitele webového testu a serverem test v paměti.

V tomto tématu se předpokládá základní znalost testů jednotek. Pokud neznáte pojmy testu, přečtěte si článek [testování jednotek v .NET Core a .NET Standard](/dotnet/core/testing/) téma a jeho odkazovaný obsah.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace je aplikace Razor Pages a předpokládá základní znalost stránky Razor. Pokud neznáte stránky Razor, naleznete v následujících tématech:

* [Úvod do Razor Pages](xref:razor-pages/index)
* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testy jednotek stránek Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pro účely testování SPA doporučujeme nástroj, jako [Selenium](https://www.seleniumhq.org/), což umožňuje automatizovat prohlížeče.

## <a name="introduction-to-integration-tests"></a>Úvod do integrace testy

Integrační testy vyhodnocení součásti vaší aplikace na úrovni širší než [testování částí](/dotnet/core/testing/). Testování částí se používají k testování izolované softwarové komponenty, jako je například metody jednotlivé třídy. Integrace testů potvrzuje, že dva nebo více komponent aplikace spolupracovat na vytváření očekávaný výsledek, případně také všechny komponenty jsou potřeba úplné zpracování požadavku.

Tyto testy širší se použily k testování aplikace infrastruktury a celé rozhraní framework, často včetně následujících součástí:

* Databáze
* Systém souborů
* Síťová zařízení
* Kanál typu žádost odpověď

Testování částí pomocí vyrobeny součásti, známé jako *napodobenin* nebo *napodobení objekty*, místo součástí infrastruktury.

Na rozdíl od jednotkové testy testy integrace:

* Použijte skutečný součásti, které aplikace používá v produkčním prostředí.
* Vyžadovat více kódu a zpracování dat.
* Trvat delší dobu.

Proto Omezte použití integrační testy pro nejdůležitější infrastrukturu scénáře. Pokud chování lze otestovat pomocí testování částí nebo o test integrace, vyberte test jednotky.

> [!TIP]
> Nezapisujte integrační testy pro každý možné permutaci dat a sdílených přístupu pomocí databáze nebo souborové systémy. Bez ohledu na to, kolik míst v aplikaci pro interakci s databází a systémy souborů, cílených sadu pro čtení, zápisu, aktualizaci a odstranění integrace testy se obvykle dokáže adekvátní testování databázi a součásti systému souborů. Použijte testy jednotky pro běžné testy logiku metody, které komunikují s komponentami. Při testech jednotek využívání infrastruktury fakes/mocks za následek rychlejší provádění testů.

> [!NOTE]
> V diskusích u testů integrace, se často nazývá testovaného projektu *zkoušený systém*, nebo "SUT" pro krátké.

## <a name="aspnet-core-integration-tests"></a>Zkoušky integrace ASP.NET Core

Integrační testy v ASP.NET Core, vyžadují následující:

* Projekt testů se používá k obsahovat a provedení testů. Projekt testů obsahuje odkaz na testovaný projekt ASP.NET Core, volá se, *testovaného systému* (SUT). _"SUT" dělali v tomto tématu slouží k odkazování na testované aplikace._
* Testovací projekt vytvoří hostitele webového testu pro SUT a zpracovávat požadavky a odpovědi SUT pomocí testovacího klienta serveru.
* Nástroj test runner se používá ke spuštění testů a sestavy výsledků testů.

Integrační testy podle posloupnost událostí, které zahrnují obvyklého *uspořádat*, *Act*, a *Assert* testovací kroky:

1. Je nakonfigurovaný SUT webového hostitele.
1. Testovací klient serveru se vytvoří odesílat žádosti do aplikace.
1. *Uspořádat* provádí se krok testu: Aplikace pro testy připraví žádost.
1. *Act* provádí se krok testu: Klient odešle žádost a obdrží odpověď.
1. *Assert* provádí se krok testu: *Skutečné* odpovědi je ověřen jako *předat* nebo *selhání* na základě *očekává* odpovědi.
1. Proces pokračuje, dokud všechny testy jsou spouštěny.
1. Výsledky testů jsou hlášeny.

Obvykle je hostitel webového testu konfigurují jinak, než hostitel běžné webové aplikace pro test běží. Například může do jiné databáze nebo jiné aplikace nastavení použít pro testy.

Součásti infrastruktury, jako je například hostitel webového testu a testu v paměti serveru ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), jsou k dispozici nebo spravuje [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) balíčku. Pomocí tohoto balíčku zjednodušuje vytváření testů a provádění.

`Microsoft.AspNetCore.Mvc.Testing` Balíček provádí následující úlohy:

* Zkopíruje soubor závislosti (*\*.deps*) z SUT do testovacího projektu *bin* složky.
* Nastaví kořenové obsahu do kořenového adresáře projektu SUT aby statické soubory a stránky a zobrazení se našly při spouštění testů.
* Poskytuje [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) tříd zjednodušení spuštění SUT s `TestServer`.

[Testování částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) dokumentace popisuje, jak nastavit službu projektu a testovacímu spuštění testu, spolu s podrobné pokyny o tom, jak spouštět testy a doporučení, jak pro název testů a testovací třídy.

> [!NOTE]
> Při vytváření testovacího projektu pro aplikaci, oddělte z testů integrace do různých projektů testů jednotek. To pomáhá zajistit, že testování komponent infrastruktury nejsou neúmyslně zahrnuty při testech jednotek. Oddělení testů jednotek a integrace také umožňuje řídit, přes které sadu testů pro spuštění.

Není k dispozici téměř žádný rozdíl mezi konfigurace pro testy aplikace Razor Pages a aplikace MVC. Jediný rozdíl je v tom, jak jsou testy s názvem. V aplikaci pro stránky Razor, testy stránky koncových bodů obvykle pojmenován podle třídy modelu stránky (například `IndexPageTests` testování součástí integrace pro indexovou stránku). V aplikaci MVC, testy se obvykle uspořádané podle třídy kontroleru a pojmenované po řadiče testují (například `HomeControllerTests` testování integrace komponenty pro kontroler Home).

## <a name="test-app-prerequisites"></a>Požadavky na testovací aplikace

Musí se projekt testů:

* Odkazovat na následujících balíčků:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Zadejte Web SDK v souboru projektu (`<Project Sdk="Microsoft.NET.Sdk.Web">`). Při odkazování na sadu SDK webové vyžádáním [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).

Tyto požadavky si můžete prohlédnout ve [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples/). Zkontrolujte *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* souboru. Tato ukázková aplikace používá [xUnit](https://xunit.github.io/) rozhraní pro testování a [AngleSharp](https://anglesharp.github.io/) analyzátor knihovny, tak také odkazuje na ukázkovou aplikaci:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT prostředí

Pokud SUT [prostředí](xref:fundamentals/environments) není nastavena, výchozí nastavení prostředí pro vývoj.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Základní testy s výchozím WebApplicationFactory

[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) slouží k vytvoření [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pro testy integrace. `TEntryPoint` vstupní bod třídu SUT, je obvykle `Startup` třídy.

Implementace třídy testu *třídy testovací přípravek* rozhraní ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) k označení třídy obsahuje testy a poskytuje instance objektů sdílené napříč testů ve třídě.

### <a name="basic-test-of-app-endpoints"></a>Základní test přenosu koncových bodů aplikace

Následující testovací třídy, `BasicTests`, používá `WebApplicationFactory` bootstrap SUT a poskytovat [HttpClient](/dotnet/api/system.net.http.httpclient) s testovací metodou `Get_EndpointsReturnSuccessAndCorrectContentType`. Metoda ověří, zda stavový kód odpovědi úspěšné (stavové kódy v rozsahu 200 299) a `Content-Type` záhlaví je `text/html; charset=utf-8` pro několik stránek aplikace.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) vytvoří instanci `HttpClient` , který automaticky sleduje přesměrování a zpracovává soubory cookie.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

### <a name="test-a-secure-endpoint"></a>Testování zabezpečené koncového bodu

V jiném testu `BasicTests` třída zkontroluje, že koncový bod zabezpečené přesměruje neověřený uživatel na přihlašovací stránku aplikace.

V SUT `/SecurePage` stránce používá [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) konvenci, kterou chcete použít [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na stránku. Další informace najdete v tématu [konvence autorizace stránek Razor](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

V `Get_SecurePageRequiresAnAuthenticatedUser` otestovat, [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) nastavená tak, aby nepovoloval přesměrování nastavením [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) k `false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

Zakázáním klienta dodržovat přesměrování, můžete provést následující kontroly:

* Stavový kód vrácený SUT můžete kontrolovat proti očekávané [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) výsledek, není kód konečný stav po přesměrování na přihlašovací stránku, kterou by [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* `Location` Hodnota hlavičky v hlavičkách odpovědi je zaškrtnuté políčko, potvrďte, že začíná `http://localhost/Identity/Account/Login`, není konečný přihlašovací stránky odpovědi, kde `Location` záhlaví nemusí být k dispozici.

Další informace o `WebApplicationFactoryClientOptions`, najdete v článku [možnosti klienta](#client-options) oddílu.

## <a name="customize-webapplicationfactory"></a>Přizpůsobení WebApplicationFactory

Konfigurace webového hostitele je možné vytvořit nezávisle na testovacích tříd děděním z `WebApplicationFactory` vytvořit jeden nebo více vlastních továren:

1. Dědit z `WebApplicationFactory` a přepsat [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umožňuje konfiguraci kolekce služby s [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Synchronizace replik indexů v databázi [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) provádí `InitializeDbForTests` metoda. Metoda je popsána v [integrační testy vzorku: Testování aplikace organizace](#test-app-organization) oddílu.

2. Použít vlastní `CustomWebApplicationFactory` v testovacích třídách. Následující příklad používá objekt pro vytváření v `IndexPageTests` třídy:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Zabránit své konfigurace klient ukázkovou aplikaci `HttpClient` z následující přesměrování. Jak je vysvětleno v [otestovat zabezpečený koncový bod](#test-a-secure-endpoint) části to umožňuje testy podívat se na výsledek první odpovědi aplikace. První reakce spočívá ve přesměrování v mnoha těchto testů se `Location` záhlaví.

3. Používá typickém testu `HttpClient` a pomocné metody pro zpracování požadavku a odpovědi:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Žádné požadavkům POST odeslaných SUT musí splňovat antiforgery zkontrolujte, jestli se automaticky stane aplikace [antiforgery systém ochrany dat](xref:security/data-protection/introduction). Aby bylo možné zajistit požadavek POST test, musí aplikace pro testy:

1. Vytvoříte žádost pro stránku.
1. Parsovat antiforgery souboru cookie a žádost o ověřovací token z odpovědi.
1. Vytvořte požadavek POST s antiforgery ověření souboru cookie a žádosti o token na místě.

`SendAsync` Pomocné metody rozšíření (*Helpers/HttpClientExtensions.cs*) a `GetDocumentAsync` pomocnou metodu (*Helpers/HtmlHelpers.cs*) v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples/) použít [AngleSharp](https://anglesharp.github.io/) analyzátor, který má zpracovávat antiforgery kontroly pomocí následujících metod:

* `GetDocumentAsync` &ndash; Přijímá [objekt HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) a vrátí `IHtmlDocument`. `GetDocumentAsync` používá objekt factory, který připraví *virtuální odpovědi* založena na původní `HttpResponseMessage`. Další informace najdete v tématu [AngleSharp dokumentaci](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` rozšiřující metody pro `HttpClient` compose [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) a volat [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pro odesílání požadavků SUT. Přetížení pro `SendAsync` přijmout formuláře HTML (`IHtmlFormElement`) a následující:
  * Tlačítko formuláře odeslat (`IHtmlElement`)
  * Kolekce hodnot formuláře (`IEnumerable<KeyValuePair<string, string>>`)
  * Tlačítko Odeslat (`IHtmlElement`) a hodnoty (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) je třetí strany parsování knihovna používaná pro demonstrační účely v tomto tématu a ukázkovou aplikaci. AngleSharp není podporován nebo potřebné pro testování integrace aplikací ASP.NET Core. Další analyzátory je možné, například [Html flexibilitu Pack (HAP)](http://html-agility-pack.net/). Další možností je napsat kód pro zpracování žádosti o token pro ověření a antiforgery soubor cookie antiforgery systému přímo.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Vlastní nastavení klienta se WithWebHostBuilder

Při další konfigurace je nutná v rámci metody testu, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) vytvoří novou `WebApplicationFactory` s [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) dál přizpůsobit podle konfigurace.

`Post_DeleteMessageHandler_ReturnsRedirectToRoot` Testovací metodu [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstruje použití `WithWebHostBuilder`. Tento test provádí aktivací odeslání formuláře v SUT odstranit záznam v databázi.

Protože jiného testu v `IndexPageTests` třída provádí operaci, která odstraňuje všechny záznamy v databázi a může být spuštěn `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metoda, databázi je nasazený v této testovací metody k zajištění, že je k dispozici pro SUT odstranit záznam. Výběr `deleteBtn1` tlačítko `messages` formulář v nástrojích pro SUT simulujeme do požadavku SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Možnosti klienta

V následující tabulce jsou uvedeny výchozí [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) dostupné při vytváření `HttpClient` instancí.

| Možnost | Popis | Výchozí |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Získá nebo nastaví, zda je či není `HttpClient` instance by měly dodržovat automaticky přesměrování odpovědi. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Získá nebo nastaví základní adresu `HttpClient` instancí. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Získá nebo nastaví, zda `HttpClient` instance by měl zpracovat soubory cookie. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Získá nebo nastaví maximální počet odpovědí na přesměrování, který `HttpClient` postupujte podle instance. | 7 |

Vytvořte `WebApplicationFactoryClientOptions` třídy a předat ho metodě [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) – metoda (výchozí hodnoty jsou zobrazeny v příkladu kódu):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Vložit mock služby

Služby se dá přepsat v testu pomocí volání [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) na tvůrce hostitele. **Vložit mock služby, musí mít SUT `Startup` třídy s `Startup.ConfigureServices` metoda.**

Ukázka SUT zahrnuje vymezenou službu, která vrátí do nabídky. Nabídku se vloží do skryté pole na indexovou stránku, pokud se požaduje indexovou stránku.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Následující kód je generován při spuštění aplikace SUT:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

K otestování služby a nabídky vkládání v o test integrace, služby mock vloženy do SUT testu. Aplikace nahrazuje mock službu `QuoteService` pomocí služby poskytované aplikace pro testy, se označuje `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` je volána, a je zaregistrován s vymezeným oborem služby:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Značky vytvářené při provádění testu odráží text nabídky poskytnutých `TestQuoteService`, tedy předá kontrolního výrazu:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Jak odvodí testovací infrastrukturu obsahu kořenová cesta aplikace

`WebApplicationFactory` Konstruktor odvodí obsahu kořenové cestě aplikace tak, že [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) na sestavení obsahující testy integrace s klíčem rovná `TEntryPoint` sestavení `System.Reflection.Assembly.FullName`. V případě, že nebyl nalezen atribut se správným klíčem, `WebApplicationFactory` spadne zpět na hledání souboru řešení (*\*.sln*) a připojí `TEntryPoint` název sestavení k adresáři řešení. Kořenový adresář aplikace (obsahu kořenová cesta) se používá ke zjišťování, zobrazení a soubory obsahu.

Ve většině případů není nutné explicitně nastavit kořen obsahu aplikace, protože logiky hledání obvykle najde správnou obsahu kořenové za běhu. Ve speciální scénářích, kde nebyl nalezen kořen obsahu pomocí algoritmu integrované hledání obsahu kořenové dá se zadat explicitně nebo pomocí vlastní logiky aplikace. Chcete-li nastavit kořen obsahu aplikace v těchto scénářích, zavolejte `UseSolutionRelativeContentRoot` rozšiřující metoda z [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost) balíčku. Zadejte relativní cestu řešení a název nebo glob souboru vzor nepovinné řešení (výchozí = `*.sln`).

Volání [UseSolutionRelativeContentRoot](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.usesolutionrelativecontentroot) pomocí metody rozšíření *jeden* z následujících postupů:

* Při konfiguraci testovacích tříd s `WebApplicationFactory`, zadejte vlastní konfigurace s [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder):

   ```csharp
   public IndexPageTests(
       WebApplicationFactory<RazorPagesProject.Startup> factory)
   {
       var _factory = factory.WithWebHostBuilder(builder =>
       {
           builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

           ...
       });
   }
   ```

* Při konfiguraci testovacích tříd s vlastní `WebApplicationFactory`, dědí `WebApplicationFactory` a přepsat [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost):

   ```csharp
   public class CustomWebApplicationFactory<TStartup>
       : WebApplicationFactory<RazorPagesProject.Startup>
   {
       protected override void ConfigureWebHost(IWebHostBuilder builder)
       {
           builder.ConfigureServices(services =>
           {
               builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

               ...
           });
       }
   }
   ```

## <a name="disable-shadow-copying"></a>Zakázat stínové kopírování sestavení

Stínové kopírování sestavení způsobí, že testy ke spuštění v jiné složce než výstupní složka. Pro testy fungovalo správně musí se zakázat stínové kopírování sestavení. [Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) používá xUnit a zakáže stínové kopírování sestavení pro xUnit, včetně *xunit.runner.json* souboru s nastavením správnou konfiguraci. Další informace najdete v tématu [xUnit.net nakonfigurování JSON](https://xunit.github.io/docs/configuring-with-json.html).

Přidat *xunit.runner.json* souboru do kořenového adresáře projektu testování s následujícím obsahem:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Odstraňování objektů

Po testech `IClassFixture` implementace jsou spouštěny, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) a [HttpClient](/dotnet/api/system.net.http.httpclient) jsou odstraněny při xUnit uvolní [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) . Pokud objekty vytvořena instance vývojář potřebuje k vyřazení, neprovede v `IClassFixture` implementace. Další informace najdete v tématu [implementace metody Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Ukázka testů integrace

[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) se skládá ze dvou aplikací:

| Aplikace | Složka projektu | Popis |
| --- | -------------- | ----------- |
| Zpráva aplikace (SUT) | *src/RazorPagesProject* | Umožňuje uživateli přidat, toku nějaký tok odstranit, odstraňte všechny a analyzovat zprávy. |
| Test aplikace | *tests/RazorPagesProject.Tests* | Slouží jako test integrace SUT. |

Testy můžete spustit pomocí integrované testovací funkce integrované vývojové prostředí, například [sady Visual Studio](https://www.visualstudio.com/vs/). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazového řádku, spusťte následující příkaz na příkazovém řádku v *tests/RazorPagesProject.Tests* složky:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Zpráva organizace aplikace (SUT)

SUT je systém zpráv pro stránky Razor s následujícími charakteristikami:

* Index stránky aplikace (*Pages/Index.cshtml* a *Pages/Index.cshtml.cs*) poskytuje uživatelské rozhraní a stránky model metody řídit přidání, odstranění a analýzy zprávy (průměrná slov za zprávy) .
* Zprávu je popsán `Message` třídy (*Data/Message.cs*) s dvě vlastnosti: `Id` (klíč) a `Text` (zprávy). `Text` Vlastnost je požadováno a omezené na 200 znaků.
* Zprávy jsou bezpečně uložené pomocí [databáze v paměti rozhraní Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* Aplikace obsahuje vrstvy přístupu k datům (DAL) ve své třídě kontext databáze `AppDbContext` (*Data/AppDbContext.cs*).
* Pokud je při spuštění aplikace prázdné databáze, úložiště zpráv je inicializován pomocí tří zpráv.
* Tato aplikace obsahuje `/SecurePage` , který je přístupný pouze od ověřeného uživatele.

&#8224;Téma EF [Test s InMemory](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy s použitím MSTest. Toto téma používá [xUnit](https://xunit.github.io/) rozhraní pro testování. Koncepty testu a testovací implementace napříč různými testovacími architektury jsou podobné, ale nejsou identické.

I když se aplikace nepoužívá model úložiště a není efektivní příklad [pracovní jednotka (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), stránky Razor podporuje tyto způsoby vývoje. Další informace najdete v tématu [návrh vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a [testovací kontroler logiku](/aspnet/core/mvc/controllers/testing) (ukázka implementuje vzor úložiště).

### <a name="test-app-organization"></a>Testování aplikace organizace

Aplikace testů je konzolová aplikace uvnitř *tests/RazorPagesProject.Tests* složky.

| Složky aplikace testu | Popis |
| --------------- | ----------- |
| *BasicTests* | *BasicTests.cs* obsahuje testovací metody pro směrování, přístup k zabezpečené stránce neověřené uživatelem a získání uživatelského profilu Githubu a kontrolu přihlášení profilu uživatele. |
| *IntegrationTests* | *IndexPageTests.cs* obsahuje testy integrace pro indexovou stránku pomocí vlastní `WebApplicationFactory` třídy. |
| *Pomocné rutiny a nástroje* | <ul><li>*Utilities.cs* obsahuje `InitializeDbForTests` metodu použitou k přidání dat do databáze s testovací data.</li><li>*HtmlHelpers.cs* představuje způsob, jak vrátit AngleSharp `IHtmlDocument` používají testovacích metod.</li><li>*HttpClientExtensions.cs* poskytují přetížení pro `SendAsync` pro odesílání požadavků SUT.</li></ul> |

Je rozhraní pro testování [xUnit](https://xunit.github.io/). Integrační testy jsou prováděny pomocí [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), což zahrnuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Protože [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) balíčku se používá ke konfiguraci testovací server hostitele a testování `TestHost` a `TestServer` balíčky nevyžadují odkazy na balíček s přímým přístupem v souboru projektu test aplikace nebo konfigurace pro vývojáře v aplikaci test.

**Synchronizace replik indexů databáze pro testování**

Integrační testy obvykle vyžadují malé datové sady v databázi před spuštěním testu. Například odstranění otestovat volání pro odstranění záznamů databáze, takže databáze, musí mít alespoň jeden záznam pro žádosti o odstranění úspěšná.

Nasazení ukázkové aplikace nasazuje databázi pomocí tří zpráv *Utilities.cs* , že testy můžete použít při jejich spuštění:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>Další zdroje

* [Testy jednotek](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Testy jednotek stránek Razor](xref:test/razor-pages-tests)
* [Middleware](xref:fundamentals/middleware/index)
* [Testovací kontrolery](xref:mvc/controllers/testing)
