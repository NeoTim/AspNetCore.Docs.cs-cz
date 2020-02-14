---
title: Modely hostování ASP.NET Core Blazor
author: guardrex
description: Pochopení Blazor modelů hostování serverů a Blazor serveru.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 2314ba39e67fbf734807b96de6c54bc94283a67d
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213311"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>Modely hostování ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor je webová architektura navržená tak, aby na straně klienta běžela na klientském počítači .NET runtime založeném na webovém [sestavení](https://webassembly.org/)( *Blazor webové sestavení*) nebo na straně serveru v ASP.NET Core ( *ServerBlazor* ). Bez ohledu na model hostování jsou modely aplikací a komponent *stejné*.

Chcete-li vytvořit projekt pro modely hostování popsané v tomto článku, přečtěte si téma <xref:blazor/get-started>.

Pokročilou konfiguraci najdete v tématu <xref:blazor/hosting-model-configuration>.

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

Hlavní hostující model pro Blazor spouští klientskou stranu v prohlížeči na webovém sestavení. Aplikace Blazor, její závislosti a modul runtime .NET se stáhnou do prohlížeče. Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče. Aktualizace uživatelského rozhraní a zpracování událostí se vyskytují v rámci stejného procesu. Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu, která je schopná obsluhovat statický obsah klientům.

![[! Evřít. Rozhraní Blazor (NO-LOC)] WebAssembly: [! Evřít. Aplikace NO-LOC (Blazor)] běží ve vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/blazor-webassembly.png)

Chcete-li vytvořit aplikaci Blazor pomocí modelu hostování na straně klienta, použijte šablonu **aplikaceBlazor WebAssembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Po výběru šablony **aplikaceBlazor WebAssembly** máte možnost konfigurovat aplikaci tak, aby používala ASP.NET Core back-end, a to tak, že vyberete zaškrtávací políčko **ASP.NET Core hostované** ([dotnet New blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core obsluhuje aplikaci Blazor klientům. Aplikace Blazor WebAssembly může komunikovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>).

Šablony obsahují skript `blazor.webassembly.js`, který zpracovává:

* Stažení modulu runtime .NET, aplikace a závislostí aplikace.
* Inicializace modulu runtime pro spuštění aplikace.

Model hostování Blazor WebAssembly nabízí několik výhod:

* Neexistuje žádná závislost na straně serveru .NET. Aplikace po stažení do klienta plně funguje.
* Prostředky a možnosti klienta jsou plně využité.
* Práce je ze serveru převedena na klienta.
* Pro hostování aplikace není vyžadován ASP.NET Core webový server. Jsou možné scénáře nasazení bez serveru (například poskytování aplikace z CDN).

K Blazor hostování WebAssembly je downsides:

* Aplikace je omezená na možnosti prohlížeče.
* Je vyžadován klientský hardware a software (například podpora WebAssembly).
* Velikost ke stažení je větší a aplikace trvá déle, než se načtou.
* Podpora modulu runtime .NET a nástrojů je méně vyspělá. Například omezení existují v [.NET Standard](/dotnet/standard/net-standard) podpoře a ladění.

## <a name="opno-locblazor-server"></a>Server Blazor

S modelem hostování serveru Blazor se aplikace spouští na serveru z aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání JavaScriptu jsou zpracovávána prostřednictvím [SignalRho](xref:signalr/introduction) připojení.

![Prohlížeč komunikuje s aplikací (hostovanou v aplikaci ASP.NET Core) na serveru přes [! Evřít. Připojení NO-LOC (Signal)]](hosting-models/_static/blazor-server.png)

Pokud chcete vytvořit aplikaci Blazor pomocí modelu hostování Blazor serveru, použijte šablonu aplikace ASP.NET Core **Blazor Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core hostuje aplikaci Blazor serveru a vytvoří SignalR koncový bod, ke kterému se klienti připojují.

ASP.NET Core aplikace odkazuje na třídu `Startup` aplikace, která se má přidat:

* Služby na straně serveru.
* Aplikace do kanálu pro zpracování požadavků.

Skript `blazor.server.js`&dagger; vytvoří připojení klienta. Je zodpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztraceného síťového připojení).

Model hostování serveru Blazor nabízí několik výhod:

* Velikost ke stažení je výrazně menší než Blazor aplikace WebAssembly a aplikace se načte mnohem rychleji.
* Aplikace plně využívá možnosti serveru, včetně použití všech rozhraní API kompatibilních s rozhraním .NET Core.
* Rozhraní .NET Core na serveru se používá ke spuštění aplikace, takže stávající nástroje .NET, jako je ladění, fungují podle očekávání.
* Podporují se tenké klienty. Například Blazor serverové aplikace fungují s prohlížeči, které nepodporují WebAssembly a na zařízeních s omezením prostředků.
* Základ pro .NET/C# kód aplikace, včetně kódu komponenty aplikace, není obsluhován klientům.

K dispozici je downsides Blazor hostování serveru:

* Vyšší latence obvykle existuje. Každá interakce uživatele zahrnuje směrování sítě.
* Neexistuje žádná podpora offline. Pokud připojení klienta neproběhne úspěšně, aplikace přestane fungovat.
* Pro aplikace s mnoha uživateli je škálovatelnost náročná. Server musí spravovat více připojení klientů a zpracovávat stav klienta.
* Pro obsluhu aplikace je vyžadován ASP.NET Core Server. Scénáře nasazení bez serveru nejsou možné (například poskytování aplikace z CDN).

&dagger;`blazor.server.js` skript se obsluhuje z vloženého prostředku ve ASP.NET Core sdíleném rozhraní.

### <a name="comparison-to-server-rendered-ui"></a>Porovnání s uživatelským rozhraním vykresleným serverem

Jedním ze způsobů, jak pochopit Blazor serverových aplikací, je pochopit, jak se liší od tradičních modelů pro vykreslování uživatelského rozhraní v ASP.NET Core aplikacích, které používají zobrazení Razor nebo Razor Pages. Oba modely používají jazyk Razor k popisu obsahu HTML, ale významně se liší v tom, jak se vykreslují kód.

Když je vykreslena stránka nebo zobrazení Razor, každý řádek kódu Razor generuje kód HTML v textovém formátu. Po vykreslení Server uvolní instanci stránky nebo zobrazení, včetně veškerého stavu, který byl vytvořen. Pokud dojde k jiné žádosti na stránku, například když se ověření serveru nepovede a zobrazí se shrnutí ověření:

* Celá stránka se znovu vykreslí do textu HTML.
* Stránka je odeslána klientovi.

Blazor aplikace se skládá z opakovaně použitelných prvků uživatelského rozhraní s názvem *Components*. Komponenta obsahuje C# kód, značku a další komponenty. Když je vykreslena komponenta, Blazor vytvoří graf zahrnutých součástí, podobně jako HTML nebo XML model DOM (Document Object Model) (DOM). Tento graf obsahuje stav součásti uložený ve vlastnostech a polích. Blazor vyhodnotí graf komponent pro vytvoření binární reprezentace kódu. Binární formát může být:

* Byl převeden na text HTML (při předvykreslování&dagger;).
* Slouží k efektivní aktualizaci značek během pravidelného vykreslování.

&dagger;*předvykreslování* &ndash; požadovaná komponenta Razor je kompilována na serveru do statického kódu HTML a odeslána klientovi, kde je vykreslena uživateli. Po navázání spojení mezi klientem a serverem se statické předem vykreslené prvky součásti nahrazují pomocí interaktivních prvků. Předvykreslování umožňuje aplikaci lépe reagovat na uživatele.

Aktualizace uživatelského rozhraní v Blazor je aktivována pomocí:

* Interakce s uživatelem, například výběr tlačítka.
* Aktivační události aplikace, jako je například časovač.

Graf se znovu vykreslí a počítá se *rozdíl* v uživatelském rozhraní (rozdíl). Tento rozdíl je nejmenší sada úprav modelu DOM, která je nutná k aktualizaci uživatelského rozhraní na klientovi. Rozdíl se pošle klientovi v binárním formátu a použije ho prohlížeč.

Komponenta je uvolněna poté, co uživatel z něj přejde na klienta. I když uživatel pracuje s komponentou, musí se stav komponenty (služby, prostředky) uchovávat v paměti serveru. Vzhledem k tomu, že stav mnoha součástí může být serverem současně udržován, vyčerpání paměti je problém, který je třeba řešit. Pokyny k vytvoření aplikace Blazor serveru pro zajištění nejlepšího využití paměti serveru najdete v tématu <xref:security/blazor/server>.

### <a name="circuits"></a>Spoj

Aplikace Blazor serveru je postavená nad [ASP.NET Core SignalR](xref:signalr/introduction). Každý klient komunikuje se serverem přes jedno nebo více SignalR připojení s názvem *okruh*. Okruh je Blazorabstrakce prostřednictvím SignalR připojení, která mohou tolerovat dočasné síťové přerušení. Když klient Blazor uvidí, že připojení SignalR je odpojené, pokusí se znovu připojit k serveru pomocí nového SignalR připojení.

Každá obrazovka prohlížeče (karta prohlížeče nebo IFRAME), která je připojená k aplikaci Blazor serveru, používá SignalR připojení. Toto je ještě další důležité rozdíly ve srovnání s typickými aplikacemi vygenerovanými serverem. V aplikaci vykreslené serverem se při otevření stejné aplikace v několika obrazovkách prohlížeče obvykle nepřevádí na další požadavky na prostředky na serveru. V aplikaci Blazor serveru vyžaduje Každá obrazovka prohlížeče samostatný okruh a samostatné instance stavu součásti, které má server spravovat.

Blazor se domnívá zavřít kartu prohlížeče nebo přejít na externí adresu URL s *řádným* ukončením. V případě řádného ukončení se okruh a přidružené prostředky ihned uvolňují. Klient se může také bez problémů odpojit, například kvůli přerušení sítě. Blazor server ukládá odpojené okruhy po Konfigurovatelný interval, aby se klient mohl znovu připojit. Další informace najdete v tématu [opětovné připojení ke stejnému serveru](xref:blazor/hosting-model-configuration#reconnection-to-the-same-server).

### <a name="ui-latency"></a>Latence uživatelského rozhraní

Latence uživatelského rozhraní je doba, kterou trvá od iniciované akce až do doby, kdy je uživatelské rozhraní aktualizováno. Menší hodnoty latence uživatelského rozhraní jsou pro aplikaci pro reakci na uživatele naprosto nezbytné. V aplikaci Blazor serveru se všechny akce odesílají na server, zpracováno a rozdíl v uživatelském rozhraní se pošle zpátky. V důsledku toho je latence uživatelského rozhraní součtem latence sítě a latencí serveru při zpracování akce.

Pro podnikovou aplikaci, která je omezená jenom na soukromou podnikovou síť, se většinou neprojeví vlivu na vnímání uživatele latence kvůli latenci sítě. V případě aplikace nasazené přes Internet může latence znamenat uživatele, zejména v případě, že uživatelé jsou geograficky distribuováni podrobněji.

Využití paměti může také přispět k latenci aplikace. Zvýšení využití paměti vede k častému uvolňování paměti nebo paměti stránkování na disk, přičemž obě tyto čítače zvyšují výkon aplikace a následně zvyšují latenci uživatelského rozhraní. Další informace najdete v tématu <xref:security/blazor/server>.

aplikace Blazor Server by měly být optimalizované pro minimalizaci latence uživatelského rozhraní tím, že se sníží latence sítě a využití paměti. Přístup k měření latence sítě najdete v tématu <xref:host-and-deploy/blazor/server#measure-network-latency>. Další informace o SignalR a Blazornajdete v těchto tématech:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Připojení k serveru

aplikace Blazor serveru vyžadují připojení k serveru aktivní SignalR. Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru. Pokud je stav klienta stále v paměti, klientská relace pokračuje bez ztráty stavu.

Aplikace Blazor serveru se předem vykreslí v reakci na první požadavek klienta, který nastaví stav uživatelského rozhraní na serveru. Když se klient pokusí vytvořit připojení SignalR, klient se musí znovu připojit ke stejnému serveru. aplikace Blazor serveru, které používají více než jeden back-end Server, by měly implementovat *rychlé relace* pro SignalR připojení.

Pro Blazor serverových aplikací doporučujeme používat [službu Azure SignalR](/azure/azure-signalr) . Služba umožňuje horizontální navýšení kapacity aplikace Blazor serveru na velký počet souběžných připojení SignalR. Rychlé relace jsou povolené pro službu Azure SignalR tím, že nastavíte možnost `ServerStickyMode` služby nebo hodnotu konfigurace na `Required`. Další informace najdete v tématu <xref:host-and-deploy/blazor/server#signalr-configuration>.

Při použití služby IIS jsou v rámci směrování žádostí na aplikace povoleny rychlé relace. Další informace najdete v tématu [Vyrovnávání zatížení HTTP pomocí směrování žádostí na aplikace](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
