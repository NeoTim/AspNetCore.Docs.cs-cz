---
title: ASP.NET Core Blazor modelech hostování
author: guardrex
description: Pochopení Blazor WebAssembly a Blazor Server hostování modelů.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/11/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 0830214029d59d0820ed3c5806267c766314c74f
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009671"
---
# <a name="aspnet-core-no-locblazor-hosting-models"></a>ASP.NET Core Blazor modelech hostování

Od [Daniel Skořepa](https://github.com/danroth27)

Blazor je webová architektura navržená tak, aby běžela na straně klienta v prohlížeči [na bázi .NET](https://webassembly.org/)runtime ( *Blazor WebAssembly* ) nebo na straně serveru v ASP.NET Core ( *Blazor Server* ). Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.

## Blazor WebAssembly

Hlavní hostující model pro Blazor je spuštěn na straně klienta v prohlížeči na webovém sestavení. BlazorAplikace, její závislosti a modul runtime .NET se stáhnou do prohlížeče. Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče. Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu. Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům. Vzhledem k tomu, že je aplikace vytvořená pro nasazení bez back-endu ASP.NET Core aplikace, nazývá se *samostatná Blazor WebAssembly aplikace*.

![::: No-Loc (Blazor WebAssembly):::: The::: No-Loc (Blazor)::: aplikace běží ve vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/blazor-webassembly.png)

K vytvoření Blazor aplikace pomocí modelu hostování na straně klienta použijte šablonu ** Blazor WebAssembly aplikace** ( [`dotnet new blazorwasm`](/dotnet/core/tools/dotnet-new) ).

Po výběru šablony ** Blazor WebAssembly aplikace** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core Hosted** ( [`dotnet new blazorwasm --hosted`](/dotnet/core/tools/dotnet-new) ). Aplikace ASP.NET Core zachovává Blazor aplikaci klientům. Aplikace s ASP.NET Core back-end se nazývá *hostovaná Blazor WebAssembly aplikace*. Blazor WebAssemblyAplikace může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction) ( <xref:tutorials/signalr-blazor-webassembly> ).

`blazor.webassembly.js`Skript je poskytován architekturou a popisovači:

* Stažení modulu runtime .NET, aplikace a závislostí aplikace.
* Inicializace modulu runtime pro spuštění aplikace.

Blazor WebAssemblyModel hostování nabízí několik výhod:

* Neexistuje žádná závislost na straně serveru .NET. Aplikace po stažení do klienta plně funguje.
* Prostředky a možnosti klienta jsou plně využité.
* Práce je ze serveru převedena na klienta.
* Pro hostování aplikace není vyžadován ASP.NET Core webový server. Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).

Existuje downsides k Blazor WebAssembly hostování:

* Aplikace je omezená na možnosti prohlížeče.
* Je vyžadován klientský hardware a software (například podpora WebAssembly).
* Velikost ke stažení je větší a aplikace trvá déle, než se načtou.
* Podpora modulu runtime .NET a nástrojů je méně vyspělá. Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.

Hostovaný Blazor aplikační model podporuje [kontejnery Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Klikněte pravým tlačítkem na projekt serveru v aplikaci Visual Studio a vyberte **Přidat**  >  **podporu Docker**.

## Blazor Server

V případě Blazor Server hostujícího modelu se aplikace spouští na serveru z aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes::: No-Loc (Signal)::: Connection.](hosting-models/_static/blazor-server.png)

K vytvoření Blazor aplikace pomocí Blazor Server modelu hostování použijte šablonu ASP.NET Core ** Blazor Server App** Template ( [`dotnet new blazorserver`](/dotnet/core/tools/dotnet-new) ). Aplikace ASP.NET Core hostuje Blazor Server aplikaci a vytvoří SignalR koncový bod, ve kterém se klienti připojují.

Aplikace ASP.NET Core odkazuje na `Startup` třídu aplikace, která se má přidat:

* Služby na straně serveru.
* Aplikace do kanálu pro zpracování požadavků.

V klientovi `blazor.server.js` skript naváže SignalR spojení se serverem. Skript se obsluhuje do aplikace na straně klienta z vloženého prostředku ve ASP.NET Core sdíleném rozhraní. Aplikace na straně klienta zodpovídá za zachování a obnovení stavu aplikace podle potřeby. 

Blazor ServerModel hostování nabízí několik výhod:

* Velikost ke stažení je výrazně menší než u Blazor WebAssembly aplikace a aplikace se načítá mnohem rychleji.
* Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.
* Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.
* Podporují se tenké klienty. Například Blazor Server aplikace pracují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.
* Základ kódu pro .NET/C# aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.

> [!IMPORTANT]
> Blazor ServerAplikace se předem vykreslí jako odpověď na první požadavek klienta, který nastaví stav uživatelského rozhraní na serveru. Když se klient pokusí vytvořit SignalR připojení, klient se **musí znovu připojit ke stejnému serveru**. Blazor Server aplikace, které používají více než jeden back-end Server, by měly implementovat *rychlé relace* pro SignalR připojení. Další informace najdete v části [připojení k serveru](#connection-to-the-server) .

Existuje downsides k Blazor Server hostování:

* Vyšší latence obvykle existuje. Každá interakce uživatele zahrnuje směrování sítě.
* Neexistuje žádná podpora offline. Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.
* Pro aplikace s mnoha uživateli je škálovatelnost náročná. Server musí spravovat více připojení klientů a zpracovávat stav klienta.
* Pro obsluhu aplikace je vyžadován ASP.NET Core Server. Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).

Blazor ServerAplikační model podporuje [kontejnery Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Klikněte pravým tlačítkem na projekt v aplikaci Visual Studio a vyberte **Přidat**  >  **podporu Docker**.

### <a name="comparison-to-server-rendered-ui"></a>Porovnání s uživatelským rozhraním vykresleným serverem

Jedním ze způsobů, Blazor Server jak pochopit aplikace, je pochopit, jak se liší od tradičních modelů pro vykreslování uživatelského rozhraní v aplikacích ASP.NET Core pomocí Razor zobrazení nebo Razor stránek. Oba modely používají Razor jazyk k popisu obsahu HTML, ale významně se liší v tom, jak se vykreslují kód.

Když Razor je vykreslena stránka nebo zobrazení, každý řádek Razor kódu GENERUJE kód HTML v textovém formátu. Po vykreslení Server uvolní instanci stránky nebo zobrazení, včetně veškerého stavu, který byl vytvořen. Pokud dojde k jiné žádosti na stránku, například když se ověření serveru nepovede a zobrazí se shrnutí ověření:

* Celá stránka se znovu vykreslí do textu HTML.
* Stránka je odeslána klientovi.

BlazorAplikace se skládá z opakovaně použitelných prvků uživatelského rozhraní s názvem *Components*. Komponenta obsahuje kód, značky a další komponenty jazyka C#. Když je vykreslena komponenta, Blazor vytvoří graf zahrnutých komponent podobně jako HTML nebo XML model DOM (Document Object Model) (DOM). Tento graf obsahuje stav součásti uložený ve vlastnostech a polích. Blazor vyhodnotí graf komponent pro vytvoření binární reprezentace značky. Binární formát může být:

* Byl převeden na text HTML (během předvykreslování &dagger; ).
* Slouží k efektivní aktualizaci značek během pravidelného vykreslování.

&dagger;*Předvykreslování*: požadovaná Razor součást je kompilována na serveru do statického kódu HTML a odeslána klientovi, kde je vykreslena uživateli. Po navázání spojení mezi klientem a serverem se statické předem vykreslené prvky součásti nahrazují pomocí interaktivních prvků. Předvykreslování umožňuje aplikaci lépe reagovat na uživatele.

Aktualizace uživatelského rozhraní v nástroji Blazor je aktivována:

* Interakce s uživatelem, například výběr tlačítka.
* Aktivační události aplikace, jako je například časovač.

Graf se znovu vykreslí a počítá se *rozdíl* v uživatelském rozhraní (rozdíl). Tento rozdíl je nejmenší sada úprav modelu DOM, která je nutná k aktualizaci uživatelského rozhraní na klientovi. Rozdíl se pošle klientovi v binárním formátu a použije ho prohlížeč.

Komponenta je uvolněna poté, co uživatel z něj přejde na klienta. I když uživatel pracuje s komponentou, musí se stav komponenty (služby, prostředky) uchovávat v paměti serveru. Vzhledem k tomu, že stav mnoha součástí může být serverem současně udržován, vyčerpání paměti je problém, který je třeba řešit. Pokyny k vytvoření Blazor Server aplikace pro zajištění nejlepšího využití paměti serveru najdete v tématu <xref:blazor/security/server/threat-mitigation> .

### <a name="circuits"></a>Spoj

Blazor ServerAplikace je postavená na [ SignalR ASP.NET Core ](xref:signalr/introduction). Každý klient komunikuje se serverem přes jedno nebo více SignalR připojení s názvem *okruh*. Okruh je Blazor abstrakcí prostřednictvím SignalR připojení, která mohou tolerovat dočasné síťové přerušení. Když se Blazor klient dohlíží, že se SignalR připojení odpojilo, pokusí se znovu připojit k serveru pomocí nového SignalR připojení.

Každá obrazovka prohlížeče (karta prohlížeče nebo IFRAME), která je připojená k Blazor Server aplikaci, používá SignalR připojení. Toto je ještě další důležité rozdíly ve srovnání s typickými aplikacemi vygenerovanými serverem. V aplikaci vykreslené serverem se při otevření stejné aplikace v několika obrazovkách prohlížeče obvykle nepřevádí na další požadavky na prostředky na serveru. V Blazor Server aplikaci Každá obrazovka prohlížeče vyžaduje samostatný okruh a samostatné instance stavu součásti, které má server spravovat.

Blazor v důsledku toho se má zavřít karta prohlížeče nebo přejít na externí adresu URL s *řádným* ukončením. V případě řádného ukončení se okruh a přidružené prostředky ihned uvolňují. Klient se může také bez problémů odpojit, například kvůli přerušení sítě. Blazor Server ukládá odpojené okruhy pro Konfigurovatelný interval, aby se klient mohl znovu připojit.

Blazor Server umožňuje kódu definovat *obslužnou rutinu okruhu*, která umožňuje spuštění kódu při změnách stavu okruhu uživatele. Další informace naleznete v tématu <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latence uživatelského rozhraní

Latence uživatelského rozhraní je doba, kterou trvá od iniciované akce až do doby, kdy je uživatelské rozhraní aktualizováno. Menší hodnoty latence uživatelského rozhraní jsou pro aplikaci pro reakci na uživatele naprosto nezbytné. V Blazor Server aplikaci se každou akci pošle na server, zpracováno a rozdíl v uživatelském rozhraní se pošle zpátky. V důsledku toho je latence uživatelského rozhraní součtem latence sítě a latencí serveru při zpracování akce.

Pro podnikovou aplikaci, která je omezená jenom na soukromou podnikovou síť, se většinou neprojeví vlivu na vnímání uživatele latence kvůli latenci sítě. V případě aplikace nasazené přes Internet může latence znamenat uživatele, zejména v případě, že uživatelé jsou geograficky distribuováni podrobněji.

Využití paměti může také přispět k latenci aplikace. Zvýšení využití paměti vede k častému uvolňování paměti nebo paměti stránkování na disk, přičemž obě tyto čítače zvyšují výkon aplikace a následně zvyšují latenci uživatelského rozhraní.

Blazor Server aplikace by měly být optimalizované pro minimalizaci latence uživatelského rozhraní tím, že se sníží latence sítě a využití paměti. Přístup k měření latence sítě najdete v tématu <xref:blazor/host-and-deploy/server#measure-network-latency> . Další informace o systémech SignalR a Blazor najdete v těchto tématech:

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Připojení k serveru

Blazor Server aplikace vyžadují aktivní SignalR připojení k serveru. Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru. Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.

Blazor ServerAplikace se předem vykreslí jako odpověď na první požadavek klienta, který nastaví stav uživatelského rozhraní na serveru. Když se klient pokusí vytvořit SignalR připojení, klient se musí znovu připojit ke stejnému serveru. Blazor Server aplikace, které používají více než jeden back-end Server, by měly implementovat *rychlé relace* pro SignalR připojení.

Pro aplikace doporučujeme používat [ SignalR službu Azure](/azure/azure-signalr) Blazor Server . Služba umožňuje horizontální navýšení kapacity Blazor Server aplikace na velký počet souběžných SignalR připojení. Relace Sticky je pro službu Azure povolené nastavením SignalR `ServerStickyMode` Možnosti služby nebo hodnoty konfigurace na `Required` . Další informace naleznete v tématu <xref:blazor/host-and-deploy/server#signalr-configuration>.

Při použití služby IIS jsou v rámci směrování žádostí na aplikace povoleny rychlé relace. Další informace najdete v tématu [Vyrovnávání zatížení HTTP pomocí směrování žádostí na aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Další materiály

* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor-webassembly>
