---
title: Integrační testy v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak integrační testy zajišťují, že součásti aplikace fungují správně na úrovni infrastruktury, včetně databáze, systému souborů a sítě.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: 414e47b9c5a1c843755bd79d313f503b554945db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664694"
---
# <a name="integration-tests-in-aspnet-core"></a>Integrační testy v ASP.NET Core

[Javier Calvarro Nelson](https://github.com/javiercn), Steve [Smith](https://ardalis.com/)a [Jos van der Til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Testy integrace zajišťují, že součásti aplikace fungují správně na úrovni, která zahrnuje podpůrnou infrastrukturu aplikace, jako je databáze, systém souborů a síť. ASP.NET Core podporuje integrační testy pomocí rozhraní testování částí s testovacím webovým hostitelem a testovacím serverem v paměti.

Toto téma předpokládá základní znalosti o testování částí. Pokud neznáte koncepty testování, podívejte se na [testování částí v tématu .NET Core a .NET Standard](/dotnet/core/testing/) a jeho propojeném obsahu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázková aplikace je aplikace Razor Pages a předpokládá základní znalosti o stránkách Razor Pages. Pokud stránky Razor Pages neznají, podívejte se na následující témata:

* [Úvod do Razor Pages](xref:razor-pages/index)
* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testy jednotek stránek Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pro testování srabů doporučujeme nástroj, jako je [Selen](https://www.seleniumhq.org/), který dokáže automatizovat prohlížeč.

## <a name="introduction-to-integration-tests"></a>Úvod do integračních testů

Testy integrace vyhodnocují součásti aplikace na širší úrovni než [testy částí](/dotnet/core/testing/). Jednotkové testy se používají k testování izolovaných softwarových součástí, jako jsou například metody jednotlivých tříd. Testy integrace potvrzují, že dvě nebo více součástí aplikace spolupracují na dosažení očekávaného výsledku, případně včetně všech součástí potřebných k úplnému zpracování požadavku.

Tyto širší testy se používají k testování infrastruktury aplikace a celého rámce, často včetně následujících součástí:

* databáze
* Systém souborů
* Síťová zařízení
* Kanál požadavku a odpovědi

Jednotkové testy používají vyrobené součásti, známé jako *falešné* nebo *falešné objekty*, místo součástí infrastruktury.

Na rozdíl od testů částí, integrační testy:

* Použijte skutečné součásti, které aplikace používá v produkčním prostředí.
* Vyžadovat více kódu a zpracování dat.
* Běh trvá déle.

Proto omezit použití integračních testů na scénáře nejdůležitější infrastruktury. Pokud chování lze testovat pomocí testování částí nebo test integrace, zvolte test jednotky.

> [!TIP]
> Nepište integrační testy pro všechny možné permutace dat a přístup k souborům s databázemi a souborovými systémy. Bez ohledu na to, kolik míst v aplikaci interaguje s databázemi a systémy souborů, cílená sada testů pro čtení, zápis, aktualizaci a odstranění jsou obvykle schopna odpovídajícím způsobem testovat součásti databáze a systému souborů. Použijte testy částí pro rutinní testy logiky metody, které interagují s těmito součástmi. V jednotkových testech má použití falešných a mocků infrastruktury za následek rychlejší spuštění testu.

> [!NOTE]
> V diskusích o integračních testech se testovaný projekt často nazývá *Testovaný systém*, zkráceně "SUT".
>
> *"SUT" se používá v celém tomto tématu odkazovat na testované ASP.NET aplikace Core.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET základní integrační testy

Integrační testy v ASP.NET Core vyžadují následující:

* Testovací projekt se používá k omezení a provedení testů. Testovací projekt má odkaz na SUT.
* Testovací projekt vytvoří testovací webový hostitel pro SUT a používá klienta testovacího serveru pro zpracování požadavků a odpovědí s SUT.
* Testovací běžec se používá k provedení testů a hlášení výsledků testů.

Testy integrace následovat posloupnost událostí, které zahrnují obvyklé *Uspořádat*, *Zákon*a *Assert* testovací kroky:

1. Webový hostitel SUT je nakonfigurován.
1. Pro odesílání požadavků do aplikace je vytvořen klient testovacího serveru.
1. Krok *testu Uspořádat* se provede: Testovací aplikace připraví požadavek.
1. Krok testu *zákona* je proveden: Klient odešle požadavek a obdrží odpověď.
1. Krok testu *assert* je proveden: *Skutečná* odpověď je ověřena jako *průchod* nebo *selhání* na základě *očekávané* odpovědi.
1. Proces pokračuje, dokud nejsou provedeny všechny testy.
1. Výsledky testů jsou hlášeny.

Testovací webový hostitel je obvykle konfigurován jinak než běžný webový hostitel aplikace pro testovací běhy. Pro testy může být například použita jiná databáze nebo jiné nastavení aplikace.

Součásti infrastruktury, jako je například testovací webový hostitel a testovací server v paměti ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), jsou poskytovány nebo spravovány balíčkem [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) Použití tohoto balíčku zjednodušuje vytváření a provádění testu.

Balíček `Microsoft.AspNetCore.Mvc.Testing` zpracovává následující úkoly:

* Zkopíruje soubor závislostí (*.deps*) z SUT do adresáře *bin* testovacího projektu.
* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na kořen projektu SUT tak, aby statické soubory a stránky/ zobrazení byly nalezeny při provádění testů.
* Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění `TestServer`sut s .

Dokumentace [k testům částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) popisuje, jak nastavit testovací projekt a testovací běh, spolu s podrobnými pokyny, jak spustit testy a doporučení, jak pojmenovat testy a testovací třídy.

> [!NOTE]
> Při vytváření testovacího projektu pro aplikaci oddělte testy částí z integračních testů do různých projektů. To pomáhá zajistit, že součásti testování infrastruktury nejsou omylem zahrnuty do testů částí. Oddělení jednotkových a integračních testů také umožňuje kontrolu nad tím, která sada testů je spuštěna.

Neexistuje prakticky žádný rozdíl mezi konfigurací pro testy aplikací Razor Pages a MVC aplikací. Jediný rozdíl je v tom, jak jsou pojmenovány testy. V aplikaci Razor Pages jsou testy koncových bodů stránky obvykle pojmenovány po třídě modelu stránky (například `IndexPageTests` k testování integrace komponent pro stránku Index). V aplikaci MVC jsou testy obvykle uspořádány podle tříd kontroleru a `HomeControllerTests` pojmenovány podle řadičů, které testují (například k testování integrace komponent pro domácí řadič).

## <a name="test-app-prerequisites"></a>Požadavky na testovací aplikaci

Zkušební projekt musí:

* Odkaz na balíček [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)
* Zadejte webovou sadku SDK v souboru projektu (`<Project Sdk="Microsoft.NET.Sdk.Web">`).

Tyto požadavky lze vidět v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Zkontrolujte soubor *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj.* Ukázková aplikace používá testovací rozhraní [xUnit](https://xunit.github.io/) a knihovnu analyzátoru [AngleSharp,](https://anglesharp.github.io/) takže ukázková aplikace také odkazuje:

* [jednotka x](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [ÚhelOstrý](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core se také používá v testech. Aplikace odkazuje:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.entityFrameworkCore.inmemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Prostředí SUT

Pokud [prostředí](xref:fundamentals/environments) SUT není nastavena, prostředí výchozí vývoj.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Základní testy s výchozí WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) se používá k vytvoření [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pro integrační testy. `TEntryPoint`je vstupní třída SUT, obvykle `Startup` třída.

Testovací třídy implementují rozhraní *pro uchycení třídy* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) k označení třídy obsahuje testy a poskytují instance sdíleného objektu napříč testy ve třídě.

Následující testovací třída `BasicTests`, `WebApplicationFactory` používá k zavádění SUT a poskytnout `Get_EndpointsReturnSuccessAndCorrectContentType` [HttpClient](/dotnet/api/system.net.http.httpclient) testovací metody . Metoda zkontroluje, zda je stavový kód odpovědi úspěšný (stavové kódy `Content-Type` v `text/html; charset=utf-8` rozsahu 200-299) a záhlaví je pro několik stránek aplikace.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) vytvoří `HttpClient` instanci, která automaticky následuje přesměrování a zpracovává cookies.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Ve výchozím nastavení nejsou nepodstatné soubory cookie zachovány v rámci požadavků, pokud jsou [povoleny zásady souhlasu GDPR.](xref:security/gdpr) Chcete-li zachovat nepodstatné soubory cookie, jako jsou ty, které používá poskytovatel TempData, označte je jako nezbytné v testech. Pokyny k označení souboru cookie jako nezbytného naleznete [v tématu Základní soubory cookie](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Přizpůsobit webovou aplikaciFactory

Konfiguraci webového hostitele lze vytvořit nezávisle na `WebApplicationFactory` testovacích třídách děděním z a vytvořením jedné nebo více vlastních továren:

1. Dědit `WebApplicationFactory` z a přepsat [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umožňuje konfiguraci kolekce služeb s [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Osazení databáze v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se `InitializeDbForTests` provádí metodou. Metoda je popsána v [integrační testy ukázka: Test organizace aplikace](#test-app-organization) části.

   Kontext databáze SUT je registrován `Startup.ConfigureServices` v jeho metodě. Zpětné volání testovací `builder.ConfigureServices` aplikace se spustí *po* spuštění `Startup.ConfigureServices` kódu aplikace. Pořadí provádění je narušující změna pro [obecný hostitel](xref:fundamentals/host/generic-host) s vydáním ASP.NET Core 3.0. Chcete-li pro testy použít jinou databázi než databázi aplikace, musí `builder.ConfigureServices`být kontext databáze aplikace nahrazen v aplikaci .

   Ukázková aplikace vyhledá popisovač služby pro kontext databáze a pomocí popisovače odebere registraci služby. Dále factory přidá nový, `ApplicationDbContext` který používá databázi v paměti pro testy.

   Chcete-li se připojit k jiné databázi `UseInMemoryDatabase` než databáze v paměti, změňte volání pro připojení kontextu k jiné databázi. Použití testovací databáze serveru SQL Server:

   * Odkaz na balíček [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet v souboru projektu.
   * Volání `UseSqlServer` s připojovacím řetězcem k databázi.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Použijte vlastní `CustomWebApplicationFactory` v testovacítřídy. Následující příklad používá továrnu `IndexPageTests` ve třídě:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Klient ukázkové aplikace je nakonfigurován `HttpClient` tak, aby zabránil následujícím přesměrováním. Jak je vysvětleno dále v části [Mock ověřování,](#mock-authentication) to umožňuje testování zkontrolovat výsledek první odpovědi aplikace. První odpověď je přesměrování v mnoha z `Location` těchto testů s hlavičkou.

3. Typický test používá `HttpClient` a pomocné metody ke zpracování požadavku a odpovědi:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Každý požadavek POST na SUT musí splňovat antiforgery kontrolu, která je automaticky provedena v aplikaci [ochrany dat antiforgery systému](xref:security/data-protection/introduction). Aby bylo možné zajistit požadavek POST testu, testovací aplikace musí:

1. Požádejte o stránku.
1. Analyzovat antiforgery cookie a požádat o ověření tokenu z odpovědi.
1. Vytvořte požadavek POST s antiforgery cookie a požádat o ověření tokenu na místě.

`SendAsync` Pomocné metody rozšíření (*Helpers/HttpClientExtensions.cs*) a `GetDocumentAsync` pomocná metoda (*Helpers/HtmlHelpers.cs*) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) používají [analyzátor AngleSharp](https://anglesharp.github.io/) ke zpracování kontroly antiforgerií pomocí následujících metod:

* `GetDocumentAsync`&ndash; Přijme [httpresponsemessage](/dotnet/api/system.net.http.httpresponsemessage) a vrátí `IHtmlDocument`. `GetDocumentAsync`používá továrnu, která připraví *virtuální* odpověď `HttpResponseMessage`na základě originálu . Další informace naleznete v [dokumentaci AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`metody rozšíření `HttpClient` pro vytvoření [httpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) a volání [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pro odesílání požadavků na SUT. Přetížení pro `SendAsync` přijetí formuláře`IHtmlFormElement`HTML ( ) a následující:
  * Tlačítko Odeslat formuláře`IHtmlElement`( )
  * Kolekce hodnot`IEnumerable<KeyValuePair<string, string>>`formuláře ( )
  * Tlačítko Odeslat (`IHtmlElement`)`IEnumerable<KeyValuePair<string, string>>`a hodnoty formuláře ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) je knihovna analýzy třetí strany používaná pro demonstrační účely v tomto tématu a ukázkové aplikaci. AngleSharp není podporována ani vyžadována pro testování integrace aplikací ASP.NET Core. Lze použít jiné analyzátory, například [balíček Html Agility Pack (HAP).](https://html-agility-pack.net/) Dalším přístupem je napsat kód pro zpracování tokenu ověření požadavku antiforgery systému a antiforgery cookie přímo.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Přizpůsobení klienta pomocí aplikace WithWebHostBuilder

Když je požadována další konfigurace v rámci testovací `WebApplicationFactory` metody, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) vytvoří nový s [IWebHostBuilder,](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) který je dále přizpůsoben konfigurací.

Testovací `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metoda [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstruje `WithWebHostBuilder`použití aplikace . Tento test provede odstranění záznamu v databázi spuštěním odeslání formuláře v SUT.

Vzhledem k `IndexPageTests` tomu, že jiný test ve třídě provádí operaci, která `Post_DeleteMessageHandler_ReturnsRedirectToRoot` odstraní všechny záznamy v databázi a může být spuštěna před metodou, databáze je znovu nasazena v této testovací metodě, aby bylo zajištěno, že záznam je přítomen pro SUT odstranit. Výběr prvního tlačítka odstranění `messages` formuláře v SUT je simulován v požadavku na SUT:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Možnosti klienta

V následující tabulce jsou uvedeny výchozí [možnosti WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) dostupné při vytváření `HttpClient` instancí.

| Možnost | Popis | Výchozí |
| ------ | ----------- | ------- |
| [Povolitautomatické přesměrování](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Získá nebo nastaví, zda `HttpClient` instance by měly automaticky sledovat odpovědi přesměrování. | `true` |
| [Základní adresa](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Získá nebo nastaví `HttpClient` základní adresu instancí. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Získá nebo `HttpClient` nastaví, zda instance by měly zpracovávat soubory cookie. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Získá nebo nastaví maximální počet odpovědí `HttpClient` přesměrování, které by měly následovat instance. | 7 |

Vytvořte `WebApplicationFactoryClientOptions` třídu a předajte ji metodě [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (výchozí hodnoty jsou zobrazeny v příkladu kódu):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Inject mock služby

Služby mohou být přepsány v testu s voláním [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) na tvůrce hostitele. **Chcete-li vložit falešné služby, `Startup` SUT `Startup.ConfigureServices` musí mít třídu s metodou.**

Ukázka SUT obsahuje službu s vymezeným oborem, která vrací nabídku. Nabídka je vložena do skrytého pole na stránce Rejstřík, když je požadována stránka Rejstřík.

*Služby/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Služby/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Stránky/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Stránky/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Při spuštění aplikace SUT se generují následující značky:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Chcete-li otestovat službu a nabídku vkládání v testu integrace mock mock je vložen do SUT testem. Falešná služba nahradí aplikace `QuoteService` službou poskytovanou testovací aplikací `TestQuoteService`s názvem :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`a služba s vymezenou sledovou službou je registrována:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Značky vytvořené během provádění testu odráží text nabídky `TestQuoteService`dodaný společností , tedy kontrolní výraz:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Mock ověřování

Testy ve `AuthTests` třídě kontrolují, zda je zabezpečený koncový bod:

* Přesměruje neověřeného uživatele na přihlašovací stránku aplikace.
* Vrátí obsah pro ověřeného uživatele.

V SUT `/SecurePage` stránka používá [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) konvence použít [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na stránku. Další informace naleznete v tématu [Razor Pages autorizace konvence](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

V `Get_SecurePageRedirectsAnUnauthenticatedUser` testu je možnost [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) nastavena tak, aby přesměrovala `false`přesměrování nastavením [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Tím, že klient nepovolí přesměrování, lze provést následující kontroly:

* Stavový kód vrácený SUT lze zkontrolovat proti očekávanému výsledku [HttpStatusCode.Redirect,](/dotnet/api/system.net.httpstatuscode) nikoli podle konečného stavového kódu po přesměrování na přihlašovací stránku, což by bylo [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Hodnota `Location` záhlaví v záhlaví odpovědí je zaškrtnuta, aby `http://localhost/Identity/Account/Login`se potvrdilo, že začíná `Location` na , nikoli na poslední odpověď na přihlašovací stránku, kde záhlaví nebude přítomno.

Testovací aplikace můžete <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> zesměšňovat v [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) za účelem testování aspektů ověřování a autorizace. Minimální scénář vrátí [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Je `TestAuthHandler` volána k ověření uživatele, pokud `Test` je `AddAuthentication` schéma `ConfigureTestServices`ověřování nastaveno na místo, kde je registrováno pro :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Další informace `WebApplicationFactoryClientOptions`naleznete v části [Možnosti klienta.](#client-options)

## <a name="set-the-environment"></a>Nastavení prostředí

Ve výchozím nastavení je prostředí hostitele a aplikace SUT nakonfigurováno tak, aby používalo vývojové prostředí. Chcete-li přepsat prostředí SUT:

* Nastavte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí (například `Staging`, `Production`nebo jinou `Testing`vlastní hodnotu, například ).
* Přepsat `CreateHostBuilder` v testovací aplikaci číst proměnné prostředí `ASPNETCORE`s předponou .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Jak testovací infrastruktura odvodí kořenovou cestu obsahu aplikace

Konstruktor `WebApplicationFactory` odvodí [kořenovou](xref:fundamentals/index#content-root) cestu obsahu aplikace vyhledáním [atributu WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) v sestavení obsahujícím integrační testy s klíčem rovnajícím se `TEntryPoint` sestavení `System.Reflection.Assembly.FullName`. V případě, že atribut se správným `WebApplicationFactory` klíčem není nalezen, přejde zpět k hledání souboru řešení (*.sln*) a připojí název `TEntryPoint` sestavení do adresáře řešení. Kořenový adresář aplikace (kořenová cesta obsahu) se používá ke zjišťování zobrazení a souborů obsahu.

## <a name="disable-shadow-copying"></a>Zakázat stínové kopírování

Stínové kopírování způsobí, že testy spustit v jiném adresáři než výstupní adresář. Aby testy fungovaly správně, musí být zakázáno stínové kopírování. [Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) používá xUnit a zakáže stínové kopírování pro xUnit zahrnutím souboru *xunit.runner.json* se správným nastavením konfigurace. Další informace naleznete [v tématu Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).

Přidejte soubor *xunit.runner.json* do kořenového adresáře testovacího projektu s následujícím obsahem:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Likvidace předmětů

Po provedení testů `IClassFixture` implementace [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) a [httpclient](/dotnet/api/system.net.http.httpclient) jsou uvolněny, když xUnit disponuje [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Pokud objekty, které vývojář konsitoval, `IClassFixture` vyžadují vyřazení, vyhoďte je v implementaci. Další informace naleznete [v tématu Implementace Dispose metoda](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Vzorek integračních testů

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se skládá ze dvou aplikací:

| Aplikace | Adresář projektu | Popis |
| --- | ----------------- | ----------- |
| Aplikace zprávy (SUT) | *projekt src/RazorPages* | Umožňuje uživateli přidávat, odstraňovat zprávy, odstraňovat všechny zprávy a analyzovat je. |
| Testovací aplikace | *testy/RazorPagesProjekt.Testy* | Používá se k integraci test SUT. |

Testy lze spustit pomocí předdefinovaných testovacích funkcí rozhraní IDE, jako je [například Visual Studio](https://visualstudio.microsoft.com). Pokud používáte [kód sady Visual Studio](https://code.visualstudio.com/) nebo příkazový řádek, proveďte následující příkaz na příkazovém řádku v adresáři *Tests/RazorPagesProject.Tests:*

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organizace aplikace zprávy (SUT)

SUT je razor pages systém zpráv s následujícími vlastnostmi:

* Stránka Index aplikace (*Pages/Index.cshtml* a *Pages/Index.cshtml.cs*) poskytuje metody modelu ui a modelu stránky pro řízení sčítání, mazání a analýzy zpráv (průměrná slova na zprávu).
* Zpráva je popsána `Message` třídou (*Data/Message.cs*) `Id` se dvěma vlastnostmi: (klíč) a `Text` (zpráva). Ubytovací `Text` zařízení je povinné a je omezeno na 200 znaků.
* Zprávy jsou uloženy pomocí [entity framework u databáze v paměti](/ef/core/providers/in-memory/)&#8224;.
* Aplikace obsahuje vrstvu přístupu k datům (DAL) ve své databázové třídě kontextu `AppDbContext` (*Data/AppDbContext.cs*).
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami.
* Aplikace obsahuje `/SecurePage` aplikaci, ke které má přístup pouze ověřený uživatel.

&#8224;Téma EF [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy s MSTest. Toto téma používá rozhraní [xUnit](https://xunit.github.io/) test. Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou identické.

I když aplikace nepoužívá vzor úložiště a není účinným příkladem [vzoru jednotky práce (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages podporuje tyto vzory vývoje. Další informace naleznete [v tématu Návrh vrstvy trvalost infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a [logiky testovacího řadiče](/aspnet/core/mvc/controllers/testing) (ukázka implementuje vzor úložiště).

### <a name="test-app-organization"></a>Testování organizace aplikace

Testovací aplikace je konzolová aplikace uvnitř adresáře *tests/RazorPagesProject.Tests.*

| Testovat adresář aplikace | Popis |
| ------------------ | ----------- |
| *AuthTesty* | Obsahuje zkušební metody pro:<ul><li>Přístup k zabezpečené stránce neověřeným uživatelem.</li><li>Přístup k zabezpečené stránce ověřeným uživatelem <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>pomocí mocku .</li><li>Získání uživatelského profilu GitHubu a kontrola přihlášení uživatele profilu.</li></ul> |
| *Základní testy* | Obsahuje testovací metodu pro směrování a typ obsahu. |
| *IntegraceTesty* | Obsahuje integrační testy pro `WebApplicationFactory` stránku Index pomocí vlastní třídy. |
| *Pomocníci/nástroje* | <ul><li>*Utilities.cs* obsahuje `InitializeDbForTests` metodu použitou k osivu databáze s testovacími daty.</li><li>*HtmlHelpers.cs* poskytuje metodu vrátit `IHtmlDocument` AngleSharp pro použití zkušební metody.</li><li>*HttpClientExtensions.cs* poskytnout přetížení `SendAsync` pro odeslání žádostí na SUT.</li></ul> |

Testovací rámec je [xUnit](https://xunit.github.io/). Testy integrace jsou prováděny pomocí [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), který zahrnuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Vzhledem k tomu, že balíček [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) se `TestHost` `TestServer` používá ke konfiguraci testovacího hostitele a testovacího serveru, balíčky a nevyžadují přímé odkazy na balíčky v souboru projektu testovací aplikace nebo konfiguraci vývojáře v testovací aplikaci.

**Nastavení databáze pro testování**

Integrační testy obvykle vyžadují malou datovou sadu v databázi před spuštěním testu. Například odstranění test vyžaduje odstranění záznamu databáze, takže databáze musí mít alespoň jeden záznam pro odstranění požadavku na úspěšné.

Ukázková aplikace osývá databázi třemi zprávami v *Utilities.cs* které testy můžou použít při jejich spuštění:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Kontext databáze SUT je registrován `Startup.ConfigureServices` v jeho metodě. Zpětné volání testovací `builder.ConfigureServices` aplikace se spustí *po* spuštění `Startup.ConfigureServices` kódu aplikace. Chcete-li pro testy použít jinou databázi, musí být `builder.ConfigureServices`kontext databáze aplikace nahrazen v aplikaci . Další informace naleznete v části [Customize WebApplicationFactory.](#customize-webapplicationfactory)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Testy integrace zajišťují, že součásti aplikace fungují správně na úrovni, která zahrnuje podpůrnou infrastrukturu aplikace, jako je databáze, systém souborů a síť. ASP.NET Core podporuje integrační testy pomocí rozhraní testování částí s testovacím webovým hostitelem a testovacím serverem v paměti.

Toto téma předpokládá základní znalosti o testování částí. Pokud neznáte koncepty testování, podívejte se na [testování částí v tématu .NET Core a .NET Standard](/dotnet/core/testing/) a jeho propojeném obsahu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázková aplikace je aplikace Razor Pages a předpokládá základní znalosti o stránkách Razor Pages. Pokud stránky Razor Pages neznají, podívejte se na následující témata:

* [Úvod do Razor Pages](xref:razor-pages/index)
* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testy jednotek stránek Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pro testování srabů doporučujeme nástroj, jako je [Selen](https://www.seleniumhq.org/), který dokáže automatizovat prohlížeč.

## <a name="introduction-to-integration-tests"></a>Úvod do integračních testů

Testy integrace vyhodnocují součásti aplikace na širší úrovni než [testy částí](/dotnet/core/testing/). Jednotkové testy se používají k testování izolovaných softwarových součástí, jako jsou například metody jednotlivých tříd. Testy integrace potvrzují, že dvě nebo více součástí aplikace spolupracují na dosažení očekávaného výsledku, případně včetně všech součástí potřebných k úplnému zpracování požadavku.

Tyto širší testy se používají k testování infrastruktury aplikace a celého rámce, často včetně následujících součástí:

* databáze
* Systém souborů
* Síťová zařízení
* Kanál požadavku a odpovědi

Jednotkové testy používají vyrobené součásti, známé jako *falešné* nebo *falešné objekty*, místo součástí infrastruktury.

Na rozdíl od testů částí, integrační testy:

* Použijte skutečné součásti, které aplikace používá v produkčním prostředí.
* Vyžadovat více kódu a zpracování dat.
* Běh trvá déle.

Proto omezit použití integračních testů na scénáře nejdůležitější infrastruktury. Pokud chování lze testovat pomocí testování částí nebo test integrace, zvolte test jednotky.

> [!TIP]
> Nepište integrační testy pro všechny možné permutace dat a přístup k souborům s databázemi a souborovými systémy. Bez ohledu na to, kolik míst v aplikaci interaguje s databázemi a systémy souborů, cílená sada testů pro čtení, zápis, aktualizaci a odstranění jsou obvykle schopna odpovídajícím způsobem testovat součásti databáze a systému souborů. Použijte testy částí pro rutinní testy logiky metody, které interagují s těmito součástmi. V jednotkových testech má použití falešných a mocků infrastruktury za následek rychlejší spuštění testu.

> [!NOTE]
> V diskusích o integračních testech se testovaný projekt často nazývá *Testovaný systém*, zkráceně "SUT".
>
> *"SUT" se používá v celém tomto tématu odkazovat na testované ASP.NET aplikace Core.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET základní integrační testy

Integrační testy v ASP.NET Core vyžadují následující:

* Testovací projekt se používá k omezení a provedení testů. Testovací projekt má odkaz na SUT.
* Testovací projekt vytvoří testovací webový hostitel pro SUT a používá klienta testovacího serveru pro zpracování požadavků a odpovědí s SUT.
* Testovací běžec se používá k provedení testů a hlášení výsledků testů.

Testy integrace následovat posloupnost událostí, které zahrnují obvyklé *Uspořádat*, *Zákon*a *Assert* testovací kroky:

1. Webový hostitel SUT je nakonfigurován.
1. Pro odesílání požadavků do aplikace je vytvořen klient testovacího serveru.
1. Krok *testu Uspořádat* se provede: Testovací aplikace připraví požadavek.
1. Krok testu *zákona* je proveden: Klient odešle požadavek a obdrží odpověď.
1. Krok testu *assert* je proveden: *Skutečná* odpověď je ověřena jako *průchod* nebo *selhání* na základě *očekávané* odpovědi.
1. Proces pokračuje, dokud nejsou provedeny všechny testy.
1. Výsledky testů jsou hlášeny.

Testovací webový hostitel je obvykle konfigurován jinak než běžný webový hostitel aplikace pro testovací běhy. Pro testy může být například použita jiná databáze nebo jiné nastavení aplikace.

Součásti infrastruktury, jako je například testovací webový hostitel a testovací server v paměti ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), jsou poskytovány nebo spravovány balíčkem [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) Použití tohoto balíčku zjednodušuje vytváření a provádění testu.

Balíček `Microsoft.AspNetCore.Mvc.Testing` zpracovává následující úkoly:

* Zkopíruje soubor závislostí (*.deps*) z SUT do adresáře *bin* testovacího projektu.
* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na kořen projektu SUT tak, aby statické soubory a stránky/ zobrazení byly nalezeny při provádění testů.
* Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění `TestServer`sut s .

Dokumentace [k testům částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) popisuje, jak nastavit testovací projekt a testovací běh, spolu s podrobnými pokyny, jak spustit testy a doporučení, jak pojmenovat testy a testovací třídy.

> [!NOTE]
> Při vytváření testovacího projektu pro aplikaci oddělte testy částí z integračních testů do různých projektů. To pomáhá zajistit, že součásti testování infrastruktury nejsou omylem zahrnuty do testů částí. Oddělení jednotkových a integračních testů také umožňuje kontrolu nad tím, která sada testů je spuštěna.

Neexistuje prakticky žádný rozdíl mezi konfigurací pro testy aplikací Razor Pages a MVC aplikací. Jediný rozdíl je v tom, jak jsou pojmenovány testy. V aplikaci Razor Pages jsou testy koncových bodů stránky obvykle pojmenovány po třídě modelu stránky (například `IndexPageTests` k testování integrace komponent pro stránku Index). V aplikaci MVC jsou testy obvykle uspořádány podle tříd kontroleru a `HomeControllerTests` pojmenovány podle řadičů, které testují (například k testování integrace komponent pro domácí řadič).

## <a name="test-app-prerequisites"></a>Požadavky na testovací aplikaci

Zkušební projekt musí:

* Odkaz na následující balení:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testování](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Zadejte webovou sadku SDK v souboru projektu (`<Project Sdk="Microsoft.NET.Sdk.Web">`). Webová sada SDK je vyžadována při odkazování na [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Tyto požadavky lze vidět v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Zkontrolujte soubor *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj.* Ukázková aplikace používá testovací rozhraní [xUnit](https://xunit.github.io/) a knihovnu analyzátoru [AngleSharp,](https://anglesharp.github.io/) takže ukázková aplikace také odkazuje:

* [jednotka x](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [ÚhelOstrý](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Prostředí SUT

Pokud [prostředí](xref:fundamentals/environments) SUT není nastavena, prostředí výchozí vývoj.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Základní testy s výchozí WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) se používá k vytvoření [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pro integrační testy. `TEntryPoint`je vstupní třída SUT, obvykle `Startup` třída.

Testovací třídy implementují rozhraní *pro uchycení třídy* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) k označení třídy obsahuje testy a poskytují instance sdíleného objektu napříč testy ve třídě.

Následující testovací třída `BasicTests`, `WebApplicationFactory` používá k zavádění SUT a poskytnout `Get_EndpointsReturnSuccessAndCorrectContentType` [HttpClient](/dotnet/api/system.net.http.httpclient) testovací metody . Metoda zkontroluje, zda je stavový kód odpovědi úspěšný (stavové kódy `Content-Type` v `text/html; charset=utf-8` rozsahu 200-299) a záhlaví je pro několik stránek aplikace.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) vytvoří `HttpClient` instanci, která automaticky následuje přesměrování a zpracovává cookies.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Ve výchozím nastavení nejsou nepodstatné soubory cookie zachovány v rámci požadavků, pokud jsou [povoleny zásady souhlasu GDPR.](xref:security/gdpr) Chcete-li zachovat nepodstatné soubory cookie, jako jsou ty, které používá poskytovatel TempData, označte je jako nezbytné v testech. Pokyny k označení souboru cookie jako nezbytného naleznete [v tématu Základní soubory cookie](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Přizpůsobit webovou aplikaciFactory

Konfiguraci webového hostitele lze vytvořit nezávisle na `WebApplicationFactory` testovacích třídách děděním z a vytvořením jedné nebo více vlastních továren:

1. Dědit `WebApplicationFactory` z a přepsat [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umožňuje konfiguraci kolekce služeb s [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Osazení databáze v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se `InitializeDbForTests` provádí metodou. Metoda je popsána v [integrační testy ukázka: Test organizace aplikace](#test-app-organization) části.

2. Použijte vlastní `CustomWebApplicationFactory` v testovacítřídy. Následující příklad používá továrnu `IndexPageTests` ve třídě:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Klient ukázkové aplikace je nakonfigurován `HttpClient` tak, aby zabránil následujícím přesměrováním. Jak je vysvětleno dále v části [Mock ověřování,](#mock-authentication) to umožňuje testování zkontrolovat výsledek první odpovědi aplikace. První odpověď je přesměrování v mnoha z `Location` těchto testů s hlavičkou.

3. Typický test používá `HttpClient` a pomocné metody ke zpracování požadavku a odpovědi:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Každý požadavek POST na SUT musí splňovat antiforgery kontrolu, která je automaticky provedena v aplikaci [ochrany dat antiforgery systému](xref:security/data-protection/introduction). Aby bylo možné zajistit požadavek POST testu, testovací aplikace musí:

1. Požádejte o stránku.
1. Analyzovat antiforgery cookie a požádat o ověření tokenu z odpovědi.
1. Vytvořte požadavek POST s antiforgery cookie a požádat o ověření tokenu na místě.

`SendAsync` Pomocné metody rozšíření (*Helpers/HttpClientExtensions.cs*) a `GetDocumentAsync` pomocná metoda (*Helpers/HtmlHelpers.cs*) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) používají [analyzátor AngleSharp](https://anglesharp.github.io/) ke zpracování kontroly antiforgerií pomocí následujících metod:

* `GetDocumentAsync`&ndash; Přijme [httpresponsemessage](/dotnet/api/system.net.http.httpresponsemessage) a vrátí `IHtmlDocument`. `GetDocumentAsync`používá továrnu, která připraví *virtuální* odpověď `HttpResponseMessage`na základě originálu . Další informace naleznete v [dokumentaci AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`metody rozšíření `HttpClient` pro vytvoření [httpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) a volání [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pro odesílání požadavků na SUT. Přetížení pro `SendAsync` přijetí formuláře`IHtmlFormElement`HTML ( ) a následující:
  * Tlačítko Odeslat formuláře`IHtmlElement`( )
  * Kolekce hodnot`IEnumerable<KeyValuePair<string, string>>`formuláře ( )
  * Tlačítko Odeslat (`IHtmlElement`)`IEnumerable<KeyValuePair<string, string>>`a hodnoty formuláře ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) je knihovna analýzy třetí strany používaná pro demonstrační účely v tomto tématu a ukázkové aplikaci. AngleSharp není podporována ani vyžadována pro testování integrace aplikací ASP.NET Core. Lze použít jiné analyzátory, například [balíček Html Agility Pack (HAP).](https://html-agility-pack.net/) Dalším přístupem je napsat kód pro zpracování tokenu ověření požadavku antiforgery systému a antiforgery cookie přímo.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Přizpůsobení klienta pomocí aplikace WithWebHostBuilder

Když je požadována další konfigurace v rámci testovací `WebApplicationFactory` metody, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) vytvoří nový s [IWebHostBuilder,](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) který je dále přizpůsoben konfigurací.

Testovací `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metoda [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstruje `WithWebHostBuilder`použití aplikace . Tento test provede odstranění záznamu v databázi spuštěním odeslání formuláře v SUT.

Vzhledem k `IndexPageTests` tomu, že jiný test ve třídě provádí operaci, která `Post_DeleteMessageHandler_ReturnsRedirectToRoot` odstraní všechny záznamy v databázi a může být spuštěna před metodou, databáze je znovu nasazena v této testovací metodě, aby bylo zajištěno, že záznam je přítomen pro SUT odstranit. Výběr prvního tlačítka odstranění `messages` formuláře v SUT je simulován v požadavku na SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Možnosti klienta

V následující tabulce jsou uvedeny výchozí [možnosti WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) dostupné při vytváření `HttpClient` instancí.

| Možnost | Popis | Výchozí |
| ------ | ----------- | ------- |
| [Povolitautomatické přesměrování](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Získá nebo nastaví, zda `HttpClient` instance by měly automaticky sledovat odpovědi přesměrování. | `true` |
| [Základní adresa](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Získá nebo nastaví `HttpClient` základní adresu instancí. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Získá nebo `HttpClient` nastaví, zda instance by měly zpracovávat soubory cookie. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Získá nebo nastaví maximální počet odpovědí `HttpClient` přesměrování, které by měly následovat instance. | 7 |

Vytvořte `WebApplicationFactoryClientOptions` třídu a předajte ji metodě [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (výchozí hodnoty jsou zobrazeny v příkladu kódu):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Inject mock služby

Služby mohou být přepsány v testu s voláním [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) na tvůrce hostitele. **Chcete-li vložit falešné služby, `Startup` SUT `Startup.ConfigureServices` musí mít třídu s metodou.**

Ukázka SUT obsahuje službu s vymezeným oborem, která vrací nabídku. Nabídka je vložena do skrytého pole na stránce Rejstřík, když je požadována stránka Rejstřík.

*Služby/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Služby/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Stránky/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Stránky/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Při spuštění aplikace SUT se generují následující značky:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Chcete-li otestovat službu a nabídku vkládání v testu integrace mock mock je vložen do SUT testem. Falešná služba nahradí aplikace `QuoteService` službou poskytovanou testovací aplikací `TestQuoteService`s názvem :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`a služba s vymezenou sledovou službou je registrována:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Značky vytvořené během provádění testu odráží text nabídky `TestQuoteService`dodaný společností , tedy kontrolní výraz:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Mock ověřování

Testy ve `AuthTests` třídě kontrolují, zda je zabezpečený koncový bod:

* Přesměruje neověřeného uživatele na přihlašovací stránku aplikace.
* Vrátí obsah pro ověřeného uživatele.

V SUT `/SecurePage` stránka používá [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) konvence použít [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na stránku. Další informace naleznete v tématu [Razor Pages autorizace konvence](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

V `Get_SecurePageRedirectsAnUnauthenticatedUser` testu je možnost [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) nastavena tak, aby přesměrovala `false`přesměrování nastavením [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Tím, že klient nepovolí přesměrování, lze provést následující kontroly:

* Stavový kód vrácený SUT lze zkontrolovat proti očekávanému výsledku [HttpStatusCode.Redirect,](/dotnet/api/system.net.httpstatuscode) nikoli podle konečného stavového kódu po přesměrování na přihlašovací stránku, což by bylo [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Hodnota `Location` záhlaví v záhlaví odpovědí je zaškrtnuta, aby `http://localhost/Identity/Account/Login`se potvrdilo, že začíná `Location` na , nikoli na poslední odpověď na přihlašovací stránku, kde záhlaví nebude přítomno.

Testovací aplikace můžete <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> zesměšňovat v [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) za účelem testování aspektů ověřování a autorizace. Minimální scénář vrátí [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Je `TestAuthHandler` volána k ověření uživatele, pokud `Test` je `AddAuthentication` schéma `ConfigureTestServices`ověřování nastaveno na místo, kde je registrováno pro :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Další informace `WebApplicationFactoryClientOptions`naleznete v části [Možnosti klienta.](#client-options)

## <a name="set-the-environment"></a>Nastavení prostředí

Ve výchozím nastavení je prostředí hostitele a aplikace SUT nakonfigurováno tak, aby používalo vývojové prostředí. Chcete-li přepsat prostředí SUT:

* Nastavte `ASPNETCORE_ENVIRONMENT` proměnnou prostředí (například `Staging`, `Production`nebo jinou `Testing`vlastní hodnotu, například ).
* Přepsat `CreateHostBuilder` v testovací aplikaci číst proměnné prostředí `ASPNETCORE`s předponou .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Jak testovací infrastruktura odvodí kořenovou cestu obsahu aplikace

Konstruktor `WebApplicationFactory` odvodí [kořenovou](xref:fundamentals/index#content-root) cestu obsahu aplikace vyhledáním [atributu WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) v sestavení obsahujícím integrační testy s klíčem rovnajícím se `TEntryPoint` sestavení `System.Reflection.Assembly.FullName`. V případě, že atribut se správným `WebApplicationFactory` klíčem není nalezen, přejde zpět k hledání souboru řešení (*.sln*) a připojí název `TEntryPoint` sestavení do adresáře řešení. Kořenový adresář aplikace (kořenová cesta obsahu) se používá ke zjišťování zobrazení a souborů obsahu.

## <a name="disable-shadow-copying"></a>Zakázat stínové kopírování

Stínové kopírování způsobí, že testy spustit v jiném adresáři než výstupní adresář. Aby testy fungovaly správně, musí být zakázáno stínové kopírování. [Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) používá xUnit a zakáže stínové kopírování pro xUnit zahrnutím souboru *xunit.runner.json* se správným nastavením konfigurace. Další informace naleznete [v tématu Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).

Přidejte soubor *xunit.runner.json* do kořenového adresáře testovacího projektu s následujícím obsahem:

```json
{
  "shadowCopy": false
}
```

Pokud používáte Visual Studio, nastavte vlastnost **Kopírovat do výstupního adresáře** souboru na **Kopírovat vždy**. Pokud nepoužíváte Visual Studio, přidejte `Content` cíl do souboru projektu testovací aplikace:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Likvidace předmětů

Po provedení testů `IClassFixture` implementace [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) a [httpclient](/dotnet/api/system.net.http.httpclient) jsou uvolněny, když xUnit disponuje [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Pokud objekty, které vývojář konsitoval, `IClassFixture` vyžadují vyřazení, vyhoďte je v implementaci. Další informace naleznete [v tématu Implementace Dispose metoda](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Vzorek integračních testů

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se skládá ze dvou aplikací:

| Aplikace | Adresář projektu | Popis |
| --- | ----------------- | ----------- |
| Aplikace zprávy (SUT) | *projekt src/RazorPages* | Umožňuje uživateli přidávat, odstraňovat zprávy, odstraňovat všechny zprávy a analyzovat je. |
| Testovací aplikace | *testy/RazorPagesProjekt.Testy* | Používá se k integraci test SUT. |

Testy lze spustit pomocí předdefinovaných testovacích funkcí rozhraní IDE, jako je [například Visual Studio](https://visualstudio.microsoft.com). Pokud používáte [kód sady Visual Studio](https://code.visualstudio.com/) nebo příkazový řádek, proveďte následující příkaz na příkazovém řádku v adresáři *Tests/RazorPagesProject.Tests:*

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organizace aplikace zprávy (SUT)

SUT je razor pages systém zpráv s následujícími vlastnostmi:

* Stránka Index aplikace (*Pages/Index.cshtml* a *Pages/Index.cshtml.cs*) poskytuje metody modelu ui a modelu stránky pro řízení sčítání, mazání a analýzy zpráv (průměrná slova na zprávu).
* Zpráva je popsána `Message` třídou (*Data/Message.cs*) `Id` se dvěma vlastnostmi: (klíč) a `Text` (zpráva). Ubytovací `Text` zařízení je povinné a je omezeno na 200 znaků.
* Zprávy jsou uloženy pomocí [entity framework u databáze v paměti](/ef/core/providers/in-memory/)&#8224;.
* Aplikace obsahuje vrstvu přístupu k datům (DAL) ve své databázové třídě kontextu `AppDbContext` (*Data/AppDbContext.cs*).
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami.
* Aplikace obsahuje `/SecurePage` aplikaci, ke které má přístup pouze ověřený uživatel.

&#8224;Téma EF [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy s MSTest. Toto téma používá rozhraní [xUnit](https://xunit.github.io/) test. Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou identické.

I když aplikace nepoužívá vzor úložiště a není účinným příkladem [vzoru jednotky práce (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages podporuje tyto vzory vývoje. Další informace naleznete [v tématu Návrh vrstvy trvalost infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a [logiky testovacího řadiče](/aspnet/core/mvc/controllers/testing) (ukázka implementuje vzor úložiště).

### <a name="test-app-organization"></a>Testování organizace aplikace

Testovací aplikace je konzolová aplikace uvnitř adresáře *tests/RazorPagesProject.Tests.*

| Testovat adresář aplikace | Popis |
| ------------------ | ----------- |
| *AuthTesty* | Obsahuje zkušební metody pro:<ul><li>Přístup k zabezpečené stránce neověřeným uživatelem.</li><li>Přístup k zabezpečené stránce ověřeným uživatelem <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>pomocí mocku .</li><li>Získání uživatelského profilu GitHubu a kontrola přihlášení uživatele profilu.</li></ul> |
| *Základní testy* | Obsahuje testovací metodu pro směrování a typ obsahu. |
| *IntegraceTesty* | Obsahuje integrační testy pro `WebApplicationFactory` stránku Index pomocí vlastní třídy. |
| *Pomocníci/nástroje* | <ul><li>*Utilities.cs* obsahuje `InitializeDbForTests` metodu použitou k osivu databáze s testovacími daty.</li><li>*HtmlHelpers.cs* poskytuje metodu vrátit `IHtmlDocument` AngleSharp pro použití zkušební metody.</li><li>*HttpClientExtensions.cs* poskytnout přetížení `SendAsync` pro odeslání žádostí na SUT.</li></ul> |

Testovací rámec je [xUnit](https://xunit.github.io/). Testy integrace jsou prováděny pomocí [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), který zahrnuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Vzhledem k tomu, že balíček [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) se `TestHost` `TestServer` používá ke konfiguraci testovacího hostitele a testovacího serveru, balíčky a nevyžadují přímé odkazy na balíčky v souboru projektu testovací aplikace nebo konfiguraci vývojáře v testovací aplikaci.

**Nastavení databáze pro testování**

Integrační testy obvykle vyžadují malou datovou sadu v databázi před spuštěním testu. Například odstranění test vyžaduje odstranění záznamu databáze, takže databáze musí mít alespoň jeden záznam pro odstranění požadavku na úspěšné.

Ukázková aplikace osývá databázi třemi zprávami v *Utilities.cs* které testy můžou použít při jejich spuštění:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Testování částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
