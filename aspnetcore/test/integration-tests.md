---
title: Integrační testy v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak testy integrace zajišťují, aby komponenty aplikace správně fungovaly na úrovni infrastruktury, včetně databáze, systému souborů a sítě.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: 414e47b9c5a1c843755bd79d313f503b554945db
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664694"
---
# <a name="integration-tests-in-aspnet-core"></a>Integrační testy v ASP.NET Core

[Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)a [Jos Van der dlaždicí](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Testy integrace zajistí správné fungování komponent aplikace na úrovni, která zahrnuje podpůrnou infrastrukturu aplikace, jako je třeba databáze, systém souborů a síť. ASP.NET Core podporuje testy integrace pomocí architektury testování částí s testovacím webovým hostitelem a testovacím serverem v paměti.

Toto téma předpokládá základní znalost testů jednotek. Pokud neznáte koncepty testování, přečtěte si téma [testování částí v .NET Core a .NET Standard](/dotnet/core/testing/) a jeho propojený obsah.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace je Razor Pages aplikace a předpokládá základní porozumění Razor Pages. Pokud neznáte Razor Pages, přečtěte si následující témata:

* [Úvod do Razor Pages](xref:razor-pages/index)
* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testy jednotek stránek Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pro testování jednostránkové doporučujeme nástroj, jako je například [selen](https://www.seleniumhq.org/), který může automatizovat prohlížeč.

## <a name="introduction-to-integration-tests"></a>Úvod do integračních testů

Testy integrace vyhodnocují komponenty aplikace na širší úrovni než [testy jednotek](/dotnet/core/testing/). Testy jednotek se používají k testování izolovaných softwarových komponent, jako jsou například jednotlivé metody třídy. Testy integrace potvrzují, že dvě nebo více součástí aplikace společně tvoří očekávaný výsledek, případně včetně všech komponent vyžadovaných k úplnému zpracování žádosti.

Tyto širší testy se používají k testování infrastruktury a celé architektury aplikace, často včetně následujících komponent:

* databáze
* Systém souborů
* Síťová zařízení
* Kanál požadavků a odpovědí

Testy jednotek používají prefabrikované komponenty, označované jako *napodobeniny* nebo *makety objektů*, místo součástí infrastruktury.

Na rozdíl od testování částí, integrační testy:

* Používejte skutečné komponenty, které aplikace používá v produkčním prostředí.
* Vyžadovat další zpracování kódu a dat.
* Spuštění trvá déle.

Proto omezte použití integračních testů na nejdůležitější scénáře infrastruktury. Pokud může být chování Testováno pomocí testu jednotky nebo testu integrace, vyberte test jednotky.

> [!TIP]
> Nepište integrační testy pro všechny možné permutace dat a přístup k souborům s databázemi a systémy souborů. Bez ohledu na to, kolik míst v rámci aplikace komunikuje s databázemi a systémy souborů, je obvykle schopnost provádět odpovídající testování součástí integračních testů pro čtení, zápis, aktualizace a odstraňování. Testování částí použijte pro rutinní testy logiky metod, které s těmito komponentami pracují. Při testování částí je použití falešných nebo pokusů infrastruktury výsledkem rychlejšího spuštění testu.

> [!NOTE]
> V diskuzích o integračních testech se testovaný projekt často nazývá testovaný *systém*nebo "SUT" pro krátké účely.
>
> *"SUT" se v tomto tématu používá pro odkaz na testované aplikace ASP.NET Core.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core testy integrace

Integrační testy v ASP.NET Core vyžadují následující:

* Testovací projekt se používá k zahrnutí a spuštění testů. Testovací projekt má odkaz na SUT.
* Testovací projekt vytvoří testovací webový hostitel pro SUT a používá klienta testovacího serveru ke zpracování požadavků a odpovědí s SUT.
* Test Runner se používá ke spuštění testů a hlášení výsledků testu.

Integrační testy následují sekvenci událostí, která zahrnuje běžné kroky pro *uspořádání*, *jednání*a *vyhodnocení* :

1. Webový hostitel SUT je nakonfigurován.
1. Je vytvořen klient testovacího serveru, který odesílá žádosti do aplikace.
1. Krok *Uspořádat* test se spustí: testovací aplikace připraví požadavek.
1. Testovací krok *Act* se spustí: klient odešle požadavek a přijme odpověď.
1. Testovací krok *kontrolního výrazu* je proveden: *skutečná* odpověď je ověřena jako *Pass* nebo *selže* na základě *očekávané* odpovědi.
1. Proces pokračuje, dokud nebudou provedeny všechny testy.
1. Výsledky testu jsou hlášeny.

Obvykle je testovací webový hostitel nakonfigurovaný jinak než normální webový hostitel aplikace pro testovací běhy. Pro testy lze například použít jinou databázi nebo jiná nastavení aplikace.

Komponenty infrastruktury, jako je testovací webový hostitel a[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)(In-Memory test server), jsou poskytovány nebo spravovány balíčkem [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) . Použití tohoto balíčku zjednodušuje vytváření a spouštění testů.

Balíček `Microsoft.AspNetCore.Mvc.Testing` zpracovává následující úlohy:

* Zkopíruje soubor závislosti ( *. DEPS*) z SUT do adresáře *bin* testovacího projektu.
* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na kořen projektu SUT, aby při spuštění testů byly nalezeny statické soubory a stránky/zobrazení.
* Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění SUT pomocí `TestServer`.

Dokumentace k testování [částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) popisuje, jak nastavit testovací projekt a Test Runner spolu s podrobnými pokyny ke spuštění testů a doporučení pro pojmenování testů a testovacích tříd.

> [!NOTE]
> Při vytváření testovacího projektu pro aplikaci oddělte testy jednotek od testů integrace do různých projektů. To pomáhá zajistit, že komponenty pro testování infrastruktury nejsou omylem zahrnuty do testů jednotek. Oddělení testů jednotek a integračních testů také umožňuje kontrolu nad tím, která sada testů je spuštěna.

Neexistuje prakticky žádný rozdíl mezi konfigurací pro testy aplikací Razor Pages a MVC. Jediný rozdíl je v tom, jak jsou testy pojmenovány. V Razor Pages aplikaci se testy koncových bodů stránky obvykle nazývají za třídou modelu stránky (například `IndexPageTests` k testování integrace komponent pro stránku indexu). V aplikaci MVC jsou testy obvykle uspořádány podle tříd kontroleru a pojmenovány po kontrolách, které otestuje (například `HomeControllerTests` k testování integrace komponent pro domovský kontroler).

## <a name="test-app-prerequisites"></a>Požadavky na test aplikace

Testovací projekt musí:

* Odkázat na balíček [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .
* Zadejte webovou sadu SDK v souboru projektu (`<Project Sdk="Microsoft.NET.Sdk.Web">`).

Tyto požadavky se dají zobrazit v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Zkontrolujte soubor *Tests/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* . Ukázková aplikace používá [xUnit](https://xunit.github.io/) test Framework a knihovnu analyzátoru [AngleSharp](https://anglesharp.github.io/) , takže ukázková aplikace také odkazuje na:

* [xUnit](https://www.nuget.org/packages/xunit)
* [xUnit. Runner. VisualStudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core se používá také v testech. Odkazy na aplikaci:

* [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft. AspNetCore. identity. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft. EntityFrameworkCore. inMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Prostředí SUT

Pokud není [prostředí](xref:fundamentals/environments) SUT nastaveno, prostředí se standardně vyvíjí.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Základní testy s výchozím WebApplicationFactory

[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) se používá k vytvoření [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pro integrační testy. `TEntryPoint` je třída vstupního bodu SUT, obvykle třída `Startup`.

Třídy testu implementují rozhraní[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)( *Class* ) k označení třídy obsahuje testy a poskytování instancí sdíleného objektu napříč testy ve třídě.

Následující testovací třída, `BasicTests`, používá `WebApplicationFactory` k zavedení SUT a poskytnutí [HttpClient](/dotnet/api/system.net.http.httpclient) testovací metodě `Get_EndpointsReturnSuccessAndCorrectContentType`. Metoda zkontroluje, jestli je kód stavu odpovědi úspěšný (stavové kódy v rozsahu 200-299), a `Content-Type` záhlaví `text/html; charset=utf-8` pro několik stránek aplikace.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) vytvoří instanci `HttpClient`, která se automaticky řídí přesměrováním a zpracováním souborů cookie.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Ve výchozím nastavení se soubory cookie, které nejsou nezbytné, nezachovají mezi požadavky, pokud je povolená [zásada pro vyjádření souhlasu GDPR](xref:security/gdpr) . Chcete-li zachovat soubory cookie, které nejsou nezbytné, například ty, které používá poskytovatel TempData, označte je jako nezbytné v testech. Pokyny k označení souboru cookie jako nezbytného najdete v tématu [základní soubory cookie](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Přizpůsobení WebApplicationFactory

Konfiguraci webového hostitele lze vytvořit nezávisle na testovacích třídách děděním z `WebApplicationFactory` k vytvoření jednoho nebo více vlastních továren:

1. Zdědit z `WebApplicationFactory` a přepsat [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umožňuje konfiguraci kolekce služeb s [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Osazení databází v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) provádí metoda `InitializeDbForTests`. Tato metoda je popsána v části [Ukázka integračních testů: organizace testovacích aplikací](#test-app-organization) .

   Kontext databáze SUT je zaregistrován v jeho metodě `Startup.ConfigureServices`. Zpětné volání testovací aplikace `builder.ConfigureServices` se spustí *po* spuštění kódu `Startup.ConfigureServices` aplikace. Pořadí spouštění je zásadní změnou pro [obecného hostitele](xref:fundamentals/host/generic-host) s vydáním ASP.NET Core 3,0. Pro použití jiné databáze pro testy, než je databáze aplikace, musí být kontext databáze aplikace nahrazen `builder.ConfigureServices`.

   Ukázková aplikace vyhledá popisovač služby pro kontext databáze a použije popisovač k odebrání registrace služby. V dalším kroku továrna přidá novou `ApplicationDbContext`, která pro testy používá databázi v paměti.

   Chcete-li se připojit k jiné databázi než databáze v paměti, změňte volání `UseInMemoryDatabase` pro připojení kontextu k jiné databázi. Použití testovací databáze SQL Server:

   * V souboru projektu se odkázat na balíček NuGet [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) .
   * Zavolá `UseSqlServer` s připojovacím řetězcem k databázi.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Použijte vlastní `CustomWebApplicationFactory` v testovacích třídách. Následující příklad používá objekt pro vytváření ve třídě `IndexPageTests`:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Klient ukázkové aplikace je nakonfigurovaný tak, aby zabránil `HttpClient`ům následujících přesměrování. Jak je vysvětleno dále v části [model ověřování](#mock-authentication) , umožňuje testům kontrolovat výsledek první odpovědi aplikace. První odpověď je přesměrování v mnoha těchto testech s hlavičkou `Location`.

3. Typický test používá `HttpClient` a pomocné metody pro zpracování žádosti a odpovědi:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Jakýkoli požadavek POST na SUT musí splňovat kontrolu proti padělání, kterou automaticky provádí [systém ochrany dat proti padělání dat](xref:security/data-protection/introduction)aplikace. Aby bylo možné uspořádat požadavek POST testu, musí aplikace testů:

1. Vytvořte požadavek na stránku.
1. Analyzovat soubor cookie pro antipadělání a žádat ověřovací token z odpovědi.
1. Proveďte požadavek POST se souborem cookie antipadělání a ověřovacím tokenem žádosti.

Metody rozšíření pomocné rutiny `SendAsync` (*helps/HttpClientExtensions. cs*) a pomocná metoda `GetDocumentAsync` (*helps/HtmlHelpers. cs*) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) používají analyzátor [AngleSharp](https://anglesharp.github.io/) pro zpracování kontroly proti padělání pomocí následujících metod:

* `GetDocumentAsync` &ndash; přijme [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) a vrátí `IHtmlDocument`. `GetDocumentAsync` používá továrnu, která připraví *virtuální odpověď* na základě původního `HttpResponseMessage`. Další informace najdete v [dokumentaci k AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` metody rozšíření pro `HttpClient` vytvoření [zprávy HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) a volání [SendAsync (zprávy HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pro odeslání požadavků do SUT. Přetížení pro `SendAsync` přijměte formulář HTML (`IHtmlFormElement`) a následující:
  * Tlačítko Odeslat ve formuláři (`IHtmlElement`)
  * Kolekce hodnot formulářů (`IEnumerable<KeyValuePair<string, string>>`)
  * Tlačítko Odeslat (`IHtmlElement`) a hodnoty formuláře (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) je knihovna analýzy třetí strany, která se používá pro demonstrační účely v tomto tématu a v ukázkové aplikaci. AngleSharp se nepodporuje nebo nevyžaduje pro testování integrace ASP.NET Corech aplikací. Lze použít jiné analyzátory, jako je například [HTML flexibility Pack (HAP)](https://html-agility-pack.net/). Další možností je napsat kód pro zpracování tokenu žádosti o ověření požadavku systému antipadělání a souboru cookie.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Přizpůsobení klienta pomocí WithWebHostBuilder

Pokud je v rámci testovací metody vyžadována další konfigurace, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) vytvoří nový `WebApplicationFactory` s [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , který je dále přizpůsoben konfigurací.

Testovací metoda `Post_DeleteMessageHandler_ReturnsRedirectToRoot` [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstruje použití `WithWebHostBuilder`. Tento test provede v databázi odstranění záznamu aktivací formuláře v SUT.

Vzhledem k tomu, že jiný test ve třídě `IndexPageTests` provádí operaci, která odstraní všechny záznamy v databázi a může běžet před `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodou, databáze je v této testovací metodě znovu dosazený, aby se zajistilo, že SUT k odstranění bude přítomen záznam. Výběr prvního tlačítka pro odstranění `messages`ového formuláře v SUT se simuluje v požadavku na SUT:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Možnosti klienta

Následující tabulka ukazuje výchozí [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) , který je k dispozici při vytváření instancí `HttpClient`.

| Možnost | Popis | Výchozí |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Získá nebo nastaví, jestli se instance `HttpClient` mají automaticky sledovat odpovědi přesměrování. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Získá nebo nastaví základní adresu instancí `HttpClient`. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Získá nebo nastaví, jestli se mají instance `HttpClient` zpracovávat soubory cookie. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Získá nebo nastaví maximální počet odpovědí přesměrování, které by `HttpClient` instance měly dodržovat. | 7 |

Vytvořte třídu `WebApplicationFactoryClientOptions` a předejte ji metodě [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (výchozí hodnoty jsou uvedeny v příkladu kódu):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Vložení makety služeb

Služby lze přepsat v rámci testu voláním [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) v Tvůrci hostitele. **Pro vložení makety služeb musí mít SUT třídu `Startup` s metodou `Startup.ConfigureServices`.**

Vzorový SUT obsahuje oborovou službu, která vrací citát. Nabídka je vložena do skrytého pole na stránce index, když je požadována stránka indexu.

*Services/IQuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/index. cshtml. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/index. cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Při spuštění aplikace SUT se vygeneruje následující kód:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Chcete-li otestovat vkládání služby a uvozovek v rámci integračního testu, je do SUTu vložena přípravou služba. Napodobná služba nahrazuje `QuoteService` aplikace pomocí služby, kterou poskytuje testovací aplikace, označovanou jako `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

je volána `ConfigureTestServices` a je zaregistrována Oborová služba:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Označení vyprodukované během provádění testu odráží text citace dodaný `TestQuoteService`, takže kontrolní výraz projde:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Modely ověřování

Testy ve třídě `AuthTests` zkontrolují zabezpečený koncový bod:

* Přesměruje neověřeného uživatele na přihlašovací stránku aplikace.
* Vrátí obsah pro ověřeného uživatele.

V SUT stránka `/SecurePage` používá konvenci [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pro použití [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na stránku. Další informace najdete v tématu [Razor Pages autorizačních konvencí](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

V `Get_SecurePageRedirectsAnUnauthenticatedUser` testu je [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) nastaveno na zakázat přesměrování nastavením [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na hodnotu `false`:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Když klientovi zakážete postup přesměrování, můžete provést následující kontroly:

* Stavový kód vrácený SUT může být zkontrolován na základě očekávaného výsledku [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) , nikoli konečného stavového kódu po přesměrování na přihlašovací stránku, která by byla [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).
* Hodnota hlavičky `Location` v hlavičkách odpovědi je zaškrtnuta, aby se ověřilo, že začíná na `http://localhost/Identity/Account/Login`, nikoli na poslední reakci přihlašovací stránky, kde se nevyskytuje hlavička `Location`.

Testovací aplikace může napodobovat <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> v [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) , aby bylo možné testovat aspekty ověřování a autorizace. Minimální scénář vrátí [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler` je volána k ověření uživatele, když je schéma ověřování nastaveno na `Test` v případě, že je `AddAuthentication` zaregistrován pro `ConfigureTestServices`:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Další informace o `WebApplicationFactoryClientOptions`naleznete v části [Možnosti klienta](#client-options) .

## <a name="set-the-environment"></a>Nastavení prostředí

Ve výchozím nastavení je hostitel a prostředí aplikace SUT nakonfigurovaná tak, aby používala vývojové prostředí. Přepsání prostředí SUT:

* Nastavte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` (například `Staging`, `Production`nebo jinou vlastní hodnotu, jako je například `Testing`).
* V testovací aplikaci můžete přepsat `CreateHostBuilder` pro čtení proměnných prostředí s předponou `ASPNETCORE`.

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Jak testovací infrastruktura odvodí kořenovou cestu obsahu aplikace

Konstruktor `WebApplicationFactory` odvodí [kořenovou cestu obsahu](xref:fundamentals/index#content-root) aplikace hledáním [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) na sestavení obsahujícího testy integrace s klíčem, který se rovná `System.Reflection.Assembly.FullName`sestavení `TEntryPoint`. V případě, že atribut se správným klíčem nebyl nalezen, `WebApplicationFactory` vrátí se k hledání souboru řešení ( *. sln*) a připojí název sestavení `TEntryPoint` k adresáři řešení. Kořenový adresář aplikace (kořenová cesta obsahu) se používá ke zjišťování zobrazení a souborů obsahu.

## <a name="disable-shadow-copying"></a>Zakázat stínové kopírování

Stínové kopírování způsobí, že se testy spustí v jiném adresáři než výstupní adresář. Aby testy správně fungovaly, musí být stínové kopírování zakázané. [Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) používá xUnit a zakáže stínové kopírování pro xUnit zahrnutím souboru *xUnit. Runner. JSON* se správným nastavením konfigurace. Další informace najdete v tématu [Konfigurace xUnit pomocí formátu JSON](https://xunit.github.io/docs/configuring-with-json.html).

Do kořenového adresáře testovacího projektu přidejte soubor *xUnit. Runner. JSON* s následujícím obsahem:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Vyřazení objektů

Po provedení testů pro `IClassFixture` implementaci jsou [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) a [HttpClient](/dotnet/api/system.net.http.httpclient) uvolněny, když xUnit vyřadí [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Pokud objekty, které vytvořil vývojář, vyžadují odstranění, vyřadí je do implementace `IClassFixture`. Další informace naleznete v tématu [implementace metody Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Ukázka integračních testů

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se skládá ze dvou aplikací:

| Aplikace | Adresář projektu | Popis |
| --- | ----------------- | ----------- |
| Aplikace zprávy (SUT) | *src/RazorPagesProject* | Povoluje uživateli přidávat, odstraňovat a analyzovat zprávy a analyzovat je. |
| Testovací aplikace | *testuje/RazorPagesProject. Tests* | Slouží k integraci testu SUT. |

Testy lze spustit pomocí vestavěných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](https://visualstudio.microsoft.com). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz v příkazovém řádku v adresáři *Tests/RazorPagesProject. Tests* :

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organizace pro aplikace zprávy (SUT)

SUT je Razor Pages systém zpráv s následujícími charakteristikami:

* Stránka indexu aplikace (*stránky/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (průměrná slova na zprávu).
* Zpráva je popsána ve třídě `Message` (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva). Vlastnost `Text` je povinná a omezená na 200 znaků.
* Zprávy se ukládají&#8224;pomocí [databáze Entity Framework v paměti](/ef/core/providers/in-memory/).
* Aplikace obsahuje vrstvu pro přístup k datům (DAL) ve své třídě kontext databáze `AppDbContext` (*data/AppDbContext. cs*).
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami.
* Aplikace obsahuje `/SecurePage`, ke kterým může mít pøístup jenom ověřený uživatel.

&#8224;Téma EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest. Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) . Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.

I když aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotky práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages podporuje tyto vzory vývoje. Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a [testovacího kontroleru testů](/aspnet/core/mvc/controllers/testing) (ukázka implementuje vzor úložiště).

### <a name="test-app-organization"></a>Organizace testovací aplikace

Testovací aplikace je Konzolová aplikace v adresáři *Tests/RazorPagesProject. Tests* .

| Testovací adresář aplikace | Popis |
| ------------------ | ----------- |
| *AuthTests* | Obsahuje testovací metody pro:<ul><li>Přístup k zabezpečené stránce neověřenému uživateli</li><li>Přístup k zabezpečené stránce ověřeným uživatelem pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Získání profilu uživatele GitHub a kontrola přihlášení uživatele profilu.</li></ul> |
| *BasicTests* | Obsahuje testovací metodu pro typ směrování a obsahu. |
| *IntegrationTests* | Obsahuje testy integrace pro stránku index s použitím vlastní třídy `WebApplicationFactory`. |
| *Pomocné programy a pomůcky* | <ul><li>*Utilities.cs* obsahuje metodu `InitializeDbForTests`, která se používá k osazení databáze testovacími daty.</li><li>*HtmlHelpers.cs* poskytuje metodu, která vrátí AngleSharp `IHtmlDocument` pro použití testovacími metodami.</li><li>*HttpClientExtensions.cs* poskytují přetížení `SendAsync` pro odeslání požadavků do SUT.</li></ul> |

Testovací rozhraní je [xUnit](https://xunit.github.io/). Integrační testy se provádějí pomocí [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), který zahrnuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Vzhledem k tomu, že se balíček [Microsoft. AspNetCore. Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) test používá ke konfiguraci hostitelského a testovacího serveru, balíčky `TestHost` a `TestServer` nevyžadují přímé odkazy na balíčky v souboru projektu testovací aplikace nebo v konfiguraci vývojáře v testovací aplikaci.

**Dosazení databáze pro testování**

Integrační testy obvykle vyžadují malou datovou sadu v databázi před spuštěním testu. Například odstraňovací test volá pro odstranění záznamu databáze, takže databáze musí mít alespoň jeden záznam, aby žádost o odstranění proběhla úspěšně.

Ukázková aplikace se dosazuje do databáze se třemi zprávami v *Utilities.cs* , které testy můžou použít, když se spustí:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Kontext databáze SUT je zaregistrován v jeho metodě `Startup.ConfigureServices`. Zpětné volání testovací aplikace `builder.ConfigureServices` se spustí *po* spuštění kódu `Startup.ConfigureServices` aplikace. Chcete-li pro testy použít jinou databázi, musí být kontext databáze aplikace nahrazen `builder.ConfigureServices`. Další informace najdete v části [přizpůsobení WebApplicationFactory](#customize-webapplicationfactory) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Testy integrace zajistí správné fungování komponent aplikace na úrovni, která zahrnuje podpůrnou infrastrukturu aplikace, jako je třeba databáze, systém souborů a síť. ASP.NET Core podporuje testy integrace pomocí architektury testování částí s testovacím webovým hostitelem a testovacím serverem v paměti.

Toto téma předpokládá základní znalost testů jednotek. Pokud neznáte koncepty testování, přečtěte si téma [testování částí v .NET Core a .NET Standard](/dotnet/core/testing/) a jeho propojený obsah.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace je Razor Pages aplikace a předpokládá základní porozumění Razor Pages. Pokud neznáte Razor Pages, přečtěte si následující témata:

* [Úvod do Razor Pages](xref:razor-pages/index)
* [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testy jednotek stránek Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pro testování jednostránkové doporučujeme nástroj, jako je například [selen](https://www.seleniumhq.org/), který může automatizovat prohlížeč.

## <a name="introduction-to-integration-tests"></a>Úvod do integračních testů

Testy integrace vyhodnocují komponenty aplikace na širší úrovni než [testy jednotek](/dotnet/core/testing/). Testy jednotek se používají k testování izolovaných softwarových komponent, jako jsou například jednotlivé metody třídy. Testy integrace potvrzují, že dvě nebo více součástí aplikace společně tvoří očekávaný výsledek, případně včetně všech komponent vyžadovaných k úplnému zpracování žádosti.

Tyto širší testy se používají k testování infrastruktury a celé architektury aplikace, často včetně následujících komponent:

* databáze
* Systém souborů
* Síťová zařízení
* Kanál požadavků a odpovědí

Testy jednotek používají prefabrikované komponenty, označované jako *napodobeniny* nebo *makety objektů*, místo součástí infrastruktury.

Na rozdíl od testování částí, integrační testy:

* Používejte skutečné komponenty, které aplikace používá v produkčním prostředí.
* Vyžadovat další zpracování kódu a dat.
* Spuštění trvá déle.

Proto omezte použití integračních testů na nejdůležitější scénáře infrastruktury. Pokud může být chování Testováno pomocí testu jednotky nebo testu integrace, vyberte test jednotky.

> [!TIP]
> Nepište integrační testy pro všechny možné permutace dat a přístup k souborům s databázemi a systémy souborů. Bez ohledu na to, kolik míst v rámci aplikace komunikuje s databázemi a systémy souborů, je obvykle schopnost provádět odpovídající testování součástí integračních testů pro čtení, zápis, aktualizace a odstraňování. Testování částí použijte pro rutinní testy logiky metod, které s těmito komponentami pracují. Při testování částí je použití falešných nebo pokusů infrastruktury výsledkem rychlejšího spuštění testu.

> [!NOTE]
> V diskuzích o integračních testech se testovaný projekt často nazývá testovaný *systém*nebo "SUT" pro krátké účely.
>
> *"SUT" se v tomto tématu používá pro odkaz na testované aplikace ASP.NET Core.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core testy integrace

Integrační testy v ASP.NET Core vyžadují následující:

* Testovací projekt se používá k zahrnutí a spuštění testů. Testovací projekt má odkaz na SUT.
* Testovací projekt vytvoří testovací webový hostitel pro SUT a používá klienta testovacího serveru ke zpracování požadavků a odpovědí s SUT.
* Test Runner se používá ke spuštění testů a hlášení výsledků testu.

Integrační testy následují sekvenci událostí, která zahrnuje běžné kroky pro *uspořádání*, *jednání*a *vyhodnocení* :

1. Webový hostitel SUT je nakonfigurován.
1. Je vytvořen klient testovacího serveru, který odesílá žádosti do aplikace.
1. Krok *Uspořádat* test se spustí: testovací aplikace připraví požadavek.
1. Testovací krok *Act* se spustí: klient odešle požadavek a přijme odpověď.
1. Testovací krok *kontrolního výrazu* je proveden: *skutečná* odpověď je ověřena jako *Pass* nebo *selže* na základě *očekávané* odpovědi.
1. Proces pokračuje, dokud nebudou provedeny všechny testy.
1. Výsledky testu jsou hlášeny.

Obvykle je testovací webový hostitel nakonfigurovaný jinak než normální webový hostitel aplikace pro testovací běhy. Pro testy lze například použít jinou databázi nebo jiná nastavení aplikace.

Komponenty infrastruktury, jako je testovací webový hostitel a[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)(In-Memory test server), jsou poskytovány nebo spravovány balíčkem [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) . Použití tohoto balíčku zjednodušuje vytváření a spouštění testů.

Balíček `Microsoft.AspNetCore.Mvc.Testing` zpracovává následující úlohy:

* Zkopíruje soubor závislosti ( *. DEPS*) z SUT do adresáře *bin* testovacího projektu.
* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na kořen projektu SUT, aby při spuštění testů byly nalezeny statické soubory a stránky/zobrazení.
* Poskytuje třídu [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pro zjednodušení zavádění SUT pomocí `TestServer`.

Dokumentace k testování [částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) popisuje, jak nastavit testovací projekt a Test Runner spolu s podrobnými pokyny ke spuštění testů a doporučení pro pojmenování testů a testovacích tříd.

> [!NOTE]
> Při vytváření testovacího projektu pro aplikaci oddělte testy jednotek od testů integrace do různých projektů. To pomáhá zajistit, že komponenty pro testování infrastruktury nejsou omylem zahrnuty do testů jednotek. Oddělení testů jednotek a integračních testů také umožňuje kontrolu nad tím, která sada testů je spuštěna.

Neexistuje prakticky žádný rozdíl mezi konfigurací pro testy aplikací Razor Pages a MVC. Jediný rozdíl je v tom, jak jsou testy pojmenovány. V Razor Pages aplikaci se testy koncových bodů stránky obvykle nazývají za třídou modelu stránky (například `IndexPageTests` k testování integrace komponent pro stránku indexu). V aplikaci MVC jsou testy obvykle uspořádány podle tříd kontroleru a pojmenovány po kontrolách, které otestuje (například `HomeControllerTests` k testování integrace komponent pro domovský kontroler).

## <a name="test-app-prerequisites"></a>Požadavky na test aplikace

Testovací projekt musí:

* Odkázat na následující balíčky:
  * [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft. AspNetCore. Mvc. testování](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Zadejte webovou sadu SDK v souboru projektu (`<Project Sdk="Microsoft.NET.Sdk.Web">`). Webová sada SDK se vyžaduje při odkazování na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

Tyto požadavky se dají zobrazit v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Zkontrolujte soubor *Tests/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* . Ukázková aplikace používá [xUnit](https://xunit.github.io/) test Framework a knihovnu analyzátoru [AngleSharp](https://anglesharp.github.io/) , takže ukázková aplikace také odkazuje na:

* [xUnit](https://www.nuget.org/packages/xunit/)
* [xUnit. Runner. VisualStudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Prostředí SUT

Pokud není [prostředí](xref:fundamentals/environments) SUT nastaveno, prostředí se standardně vyvíjí.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Základní testy s výchozím WebApplicationFactory

[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) se používá k vytvoření [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pro integrační testy. `TEntryPoint` je třída vstupního bodu SUT, obvykle třída `Startup`.

Třídy testu implementují rozhraní[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)( *Class* ) k označení třídy obsahuje testy a poskytování instancí sdíleného objektu napříč testy ve třídě.

Následující testovací třída, `BasicTests`, používá `WebApplicationFactory` k zavedení SUT a poskytnutí [HttpClient](/dotnet/api/system.net.http.httpclient) testovací metodě `Get_EndpointsReturnSuccessAndCorrectContentType`. Metoda zkontroluje, jestli je kód stavu odpovědi úspěšný (stavové kódy v rozsahu 200-299), a `Content-Type` záhlaví `text/html; charset=utf-8` pro několik stránek aplikace.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) vytvoří instanci `HttpClient`, která se automaticky řídí přesměrováním a zpracováním souborů cookie.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Ve výchozím nastavení se soubory cookie, které nejsou nezbytné, nezachovají mezi požadavky, pokud je povolená [zásada pro vyjádření souhlasu GDPR](xref:security/gdpr) . Chcete-li zachovat soubory cookie, které nejsou nezbytné, například ty, které používá poskytovatel TempData, označte je jako nezbytné v testech. Pokyny k označení souboru cookie jako nezbytného najdete v tématu [základní soubory cookie](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Přizpůsobení WebApplicationFactory

Konfiguraci webového hostitele lze vytvořit nezávisle na testovacích třídách děděním z `WebApplicationFactory` k vytvoření jednoho nebo více vlastních továren:

1. Zdědit z `WebApplicationFactory` a přepsat [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umožňuje konfiguraci kolekce služeb s [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Osazení databází v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) provádí metoda `InitializeDbForTests`. Tato metoda je popsána v části [Ukázka integračních testů: organizace testovacích aplikací](#test-app-organization) .

2. Použijte vlastní `CustomWebApplicationFactory` v testovacích třídách. Následující příklad používá objekt pro vytváření ve třídě `IndexPageTests`:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Klient ukázkové aplikace je nakonfigurovaný tak, aby zabránil `HttpClient`ům následujících přesměrování. Jak je vysvětleno dále v části [model ověřování](#mock-authentication) , umožňuje testům kontrolovat výsledek první odpovědi aplikace. První odpověď je přesměrování v mnoha těchto testech s hlavičkou `Location`.

3. Typický test používá `HttpClient` a pomocné metody pro zpracování žádosti a odpovědi:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Jakýkoli požadavek POST na SUT musí splňovat kontrolu proti padělání, kterou automaticky provádí [systém ochrany dat proti padělání dat](xref:security/data-protection/introduction)aplikace. Aby bylo možné uspořádat požadavek POST testu, musí aplikace testů:

1. Vytvořte požadavek na stránku.
1. Analyzovat soubor cookie pro antipadělání a žádat ověřovací token z odpovědi.
1. Proveďte požadavek POST se souborem cookie antipadělání a ověřovacím tokenem žádosti.

Metody rozšíření pomocné rutiny `SendAsync` (*helps/HttpClientExtensions. cs*) a pomocná metoda `GetDocumentAsync` (*helps/HtmlHelpers. cs*) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) používají analyzátor [AngleSharp](https://anglesharp.github.io/) pro zpracování kontroly proti padělání pomocí následujících metod:

* `GetDocumentAsync` &ndash; přijme [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) a vrátí `IHtmlDocument`. `GetDocumentAsync` používá továrnu, která připraví *virtuální odpověď* na základě původního `HttpResponseMessage`. Další informace najdete v [dokumentaci k AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` metody rozšíření pro `HttpClient` vytvoření [zprávy HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) a volání [SendAsync (zprávy HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pro odeslání požadavků do SUT. Přetížení pro `SendAsync` přijměte formulář HTML (`IHtmlFormElement`) a následující:
  * Tlačítko Odeslat ve formuláři (`IHtmlElement`)
  * Kolekce hodnot formulářů (`IEnumerable<KeyValuePair<string, string>>`)
  * Tlačítko Odeslat (`IHtmlElement`) a hodnoty formuláře (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) je knihovna analýzy třetí strany, která se používá pro demonstrační účely v tomto tématu a v ukázkové aplikaci. AngleSharp se nepodporuje nebo nevyžaduje pro testování integrace ASP.NET Corech aplikací. Lze použít jiné analyzátory, jako je například [HTML flexibility Pack (HAP)](https://html-agility-pack.net/). Další možností je napsat kód pro zpracování tokenu žádosti o ověření požadavku systému antipadělání a souboru cookie.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Přizpůsobení klienta pomocí WithWebHostBuilder

Pokud je v rámci testovací metody vyžadována další konfigurace, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) vytvoří nový `WebApplicationFactory` s [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , který je dále přizpůsoben konfigurací.

Testovací metoda `Post_DeleteMessageHandler_ReturnsRedirectToRoot` [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstruje použití `WithWebHostBuilder`. Tento test provede v databázi odstranění záznamu aktivací formuláře v SUT.

Vzhledem k tomu, že jiný test ve třídě `IndexPageTests` provádí operaci, která odstraní všechny záznamy v databázi a může běžet před `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodou, databáze je v této testovací metodě znovu dosazený, aby se zajistilo, že SUT k odstranění bude přítomen záznam. Výběr prvního tlačítka pro odstranění `messages`ového formuláře v SUT se simuluje v požadavku na SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Možnosti klienta

Následující tabulka ukazuje výchozí [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) , který je k dispozici při vytváření instancí `HttpClient`.

| Možnost | Popis | Výchozí |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Získá nebo nastaví, jestli se instance `HttpClient` mají automaticky sledovat odpovědi přesměrování. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Získá nebo nastaví základní adresu instancí `HttpClient`. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Získá nebo nastaví, jestli se mají instance `HttpClient` zpracovávat soubory cookie. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Získá nebo nastaví maximální počet odpovědí přesměrování, které by `HttpClient` instance měly dodržovat. | 7 |

Vytvořte třídu `WebApplicationFactoryClientOptions` a předejte ji metodě [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (výchozí hodnoty jsou uvedeny v příkladu kódu):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Vložení makety služeb

Služby lze přepsat v rámci testu voláním [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) v Tvůrci hostitele. **Pro vložení makety služeb musí mít SUT třídu `Startup` s metodou `Startup.ConfigureServices`.**

Vzorový SUT obsahuje oborovou službu, která vrací citát. Nabídka je vložena do skrytého pole na stránce index, když je požadována stránka indexu.

*Services/IQuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/index. cshtml. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/index. cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Při spuštění aplikace SUT se vygeneruje následující kód:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Chcete-li otestovat vkládání služby a uvozovek v rámci integračního testu, je do SUTu vložena přípravou služba. Napodobná služba nahrazuje `QuoteService` aplikace pomocí služby, kterou poskytuje testovací aplikace, označovanou jako `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

je volána `ConfigureTestServices` a je zaregistrována Oborová služba:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Označení vyprodukované během provádění testu odráží text citace dodaný `TestQuoteService`, takže kontrolní výraz projde:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Modely ověřování

Testy ve třídě `AuthTests` zkontrolují zabezpečený koncový bod:

* Přesměruje neověřeného uživatele na přihlašovací stránku aplikace.
* Vrátí obsah pro ověřeného uživatele.

V SUT stránka `/SecurePage` používá konvenci [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pro použití [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) na stránku. Další informace najdete v tématu [Razor Pages autorizačních konvencí](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

V `Get_SecurePageRedirectsAnUnauthenticatedUser` testu je [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) nastaveno na zakázat přesměrování nastavením [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na hodnotu `false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Když klientovi zakážete postup přesměrování, můžete provést následující kontroly:

* Stavový kód vrácený SUT může být zkontrolován na základě očekávaného výsledku [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) , nikoli konečného stavového kódu po přesměrování na přihlašovací stránku, která by byla [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).
* Hodnota hlavičky `Location` v hlavičkách odpovědi je zaškrtnuta, aby se ověřilo, že začíná na `http://localhost/Identity/Account/Login`, nikoli na poslední reakci přihlašovací stránky, kde se nevyskytuje hlavička `Location`.

Testovací aplikace může napodobovat <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> v [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) , aby bylo možné testovat aspekty ověřování a autorizace. Minimální scénář vrátí [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler` je volána k ověření uživatele, když je schéma ověřování nastaveno na `Test` v případě, že je `AddAuthentication` zaregistrován pro `ConfigureTestServices`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Další informace o `WebApplicationFactoryClientOptions`naleznete v části [Možnosti klienta](#client-options) .

## <a name="set-the-environment"></a>Nastavení prostředí

Ve výchozím nastavení je hostitel a prostředí aplikace SUT nakonfigurovaná tak, aby používala vývojové prostředí. Přepsání prostředí SUT:

* Nastavte proměnnou prostředí `ASPNETCORE_ENVIRONMENT` (například `Staging`, `Production`nebo jinou vlastní hodnotu, jako je například `Testing`).
* V testovací aplikaci můžete přepsat `CreateHostBuilder` pro čtení proměnných prostředí s předponou `ASPNETCORE`.

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Jak testovací infrastruktura odvodí kořenovou cestu obsahu aplikace

Konstruktor `WebApplicationFactory` odvodí [kořenovou cestu obsahu](xref:fundamentals/index#content-root) aplikace hledáním [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) na sestavení obsahujícího testy integrace s klíčem, který se rovná `System.Reflection.Assembly.FullName`sestavení `TEntryPoint`. V případě, že atribut se správným klíčem nebyl nalezen, `WebApplicationFactory` vrátí se k hledání souboru řešení ( *. sln*) a připojí název sestavení `TEntryPoint` k adresáři řešení. Kořenový adresář aplikace (kořenová cesta obsahu) se používá ke zjišťování zobrazení a souborů obsahu.

## <a name="disable-shadow-copying"></a>Zakázat stínové kopírování

Stínové kopírování způsobí, že se testy spustí v jiném adresáři než výstupní adresář. Aby testy správně fungovaly, musí být stínové kopírování zakázané. [Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) používá xUnit a zakáže stínové kopírování pro xUnit zahrnutím souboru *xUnit. Runner. JSON* se správným nastavením konfigurace. Další informace najdete v tématu [Konfigurace xUnit pomocí formátu JSON](https://xunit.github.io/docs/configuring-with-json.html).

Do kořenového adresáře testovacího projektu přidejte soubor *xUnit. Runner. JSON* s následujícím obsahem:

```json
{
  "shadowCopy": false
}
```

Pokud používáte sadu Visual Studio, nastavte vlastnost **Kopírovat do výstupního adresáře** na **Kopírovat vždy**. Pokud se Visual Studio nepoužívá, přidejte do souboru projektu testovací aplikace `Content` cíl:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Vyřazení objektů

Po provedení testů pro `IClassFixture` implementaci jsou [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) a [HttpClient](/dotnet/api/system.net.http.httpclient) uvolněny, když xUnit vyřadí [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Pokud objekty, které vytvořil vývojář, vyžadují odstranění, vyřadí je do implementace `IClassFixture`. Další informace naleznete v tématu [implementace metody Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Ukázka integračních testů

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se skládá ze dvou aplikací:

| Aplikace | Adresář projektu | Popis |
| --- | ----------------- | ----------- |
| Aplikace zprávy (SUT) | *src/RazorPagesProject* | Povoluje uživateli přidávat, odstraňovat a analyzovat zprávy a analyzovat je. |
| Testovací aplikace | *testuje/RazorPagesProject. Tests* | Slouží k integraci testu SUT. |

Testy lze spustit pomocí vestavěných funkcí testu integrovaného vývojového prostředí (IDE), jako je například [Visual Studio](https://visualstudio.microsoft.com). Pokud používáte [Visual Studio Code](https://code.visualstudio.com/) nebo příkazový řádek, spusťte následující příkaz v příkazovém řádku v adresáři *Tests/RazorPagesProject. Tests* :

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organizace pro aplikace zprávy (SUT)

SUT je Razor Pages systém zpráv s následujícími charakteristikami:

* Stránka indexu aplikace (*stránky/index. cshtml* a *pages/index. cshtml. cs*) poskytuje metody uživatelského rozhraní a modelu stránky pro řízení přidávání, odstraňování a analýzy zpráv (průměrná slova na zprávu).
* Zpráva je popsána ve třídě `Message` (*data/Message. cs*) se dvěma vlastnostmi: `Id` (klíč) a `Text` (zpráva). Vlastnost `Text` je povinná a omezená na 200 znaků.
* Zprávy se ukládají&#8224;pomocí [databáze Entity Framework v paměti](/ef/core/providers/in-memory/).
* Aplikace obsahuje vrstvu pro přístup k datům (DAL) ve své třídě kontext databáze `AppDbContext` (*data/AppDbContext. cs*).
* Pokud je databáze při spuštění aplikace prázdná, úložiště zpráv se inicializuje se třemi zprávami.
* Aplikace obsahuje `/SecurePage`, ke kterým může mít pøístup jenom ověřený uživatel.

&#8224;Téma EF, [test s pamětí](/ef/core/miscellaneous/testing/in-memory), vysvětluje, jak používat databázi v paměti pro testy pomocí MSTest. Toto téma používá testovací rozhraní [xUnit](https://xunit.github.io/) . Koncepty testů a testovací implementace v různých testovacích architekturách jsou podobné, ale nejsou totožné.

I když aplikace nepoužívá vzor úložiště a není efektivním příkladem [vzoru jednotky práce (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages podporuje tyto vzory vývoje. Další informace najdete v tématu [navrhování vrstvy trvalosti infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) a [testovacího kontroleru testů](/aspnet/core/mvc/controllers/testing) (ukázka implementuje vzor úložiště).

### <a name="test-app-organization"></a>Organizace testovací aplikace

Testovací aplikace je Konzolová aplikace v adresáři *Tests/RazorPagesProject. Tests* .

| Testovací adresář aplikace | Popis |
| ------------------ | ----------- |
| *AuthTests* | Obsahuje testovací metody pro:<ul><li>Přístup k zabezpečené stránce neověřenému uživateli</li><li>Přístup k zabezpečené stránce ověřeným uživatelem pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Získání profilu uživatele GitHub a kontrola přihlášení uživatele profilu.</li></ul> |
| *BasicTests* | Obsahuje testovací metodu pro typ směrování a obsahu. |
| *IntegrationTests* | Obsahuje testy integrace pro stránku index s použitím vlastní třídy `WebApplicationFactory`. |
| *Pomocné programy a pomůcky* | <ul><li>*Utilities.cs* obsahuje metodu `InitializeDbForTests`, která se používá k osazení databáze testovacími daty.</li><li>*HtmlHelpers.cs* poskytuje metodu, která vrátí AngleSharp `IHtmlDocument` pro použití testovacími metodami.</li><li>*HttpClientExtensions.cs* poskytují přetížení `SendAsync` pro odeslání požadavků do SUT.</li></ul> |

Testovací rozhraní je [xUnit](https://xunit.github.io/). Integrační testy se provádějí pomocí [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), který zahrnuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Vzhledem k tomu, že se balíček [Microsoft. AspNetCore. Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) test používá ke konfiguraci hostitelského a testovacího serveru, balíčky `TestHost` a `TestServer` nevyžadují přímé odkazy na balíčky v souboru projektu testovací aplikace nebo v konfiguraci vývojáře v testovací aplikaci.

**Dosazení databáze pro testování**

Integrační testy obvykle vyžadují malou datovou sadu v databázi před spuštěním testu. Například odstraňovací test volá pro odstranění záznamu databáze, takže databáze musí mít alespoň jeden záznam, aby žádost o odstranění proběhla úspěšně.

Ukázková aplikace se dosazuje do databáze se třemi zprávami v *Utilities.cs* , které testy můžou použít, když se spustí:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Testování částí](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
