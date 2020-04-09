---
title: ASP.NET Blazor hostingové modely Core
author: guardrex
description: Seznamte se s Blazor modely hostování webassembly a Blazor serveru.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 0dfc991f76acb227ce9ea27a07fbae50571f0117
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471826"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>ASP.NET Blazor hostingové modely Core

Podle [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazorje webový framework určený ke spuštění klientské strany v prohlížeči na rozhraní .NET (WebAssembly) založeném na [webové sestavě](https://webassembly.org/)* Blazor (WebAssembly)* nebo na straně serveru v ASP.NET Core (* Blazor Server*). Bez ohledu na hostování modelu jsou modely aplikací a komponent *stejné*.

Chcete-li vytvořit projekt pro hostování modelů popsaných v tomto článku, naleznete v tématu <xref:blazor/get-started>.

Pokročilou konfiguraci <xref:blazor/hosting-model-configuration>naleznete v tématu .

## <a name="opno-locblazor-webassembly"></a>BlazorWebová sestava

Hlavní hostitelský Blazor model pro je spuštěn na straně klienta v prohlížeči na WebAssembly. Aplikace, Blazor její závislosti a za běhu .NET jsou staženy do prohlížeče. Aplikace se spouští přímo ve vlákně uživatelského rozhraní prohlížeče. Aktualizace ui a zpracování událostí dojít v rámci stejného procesu. Prostředky aplikace se nasazují jako statické soubory na webový server nebo službu schopnou obsluhovat statický obsah klientům.

![BlazorWebAssembly: Blazor Aplikace běží na vlákno uživatelského rozhraní uvnitř prohlížeče.](hosting-models/_static/blazor-webassembly.png)

Chcete-li Blazor vytvořit aplikaci pomocí hostitelského modelu na straně ** Blazor klienta,** použijte šablonu aplikace WebAssembly App[(dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).

Po výběru ** Blazor ** šablony aplikace WebAssembly App máte možnost nakonfigurovat aplikaci tak, aby používala back-end ASP.NET Core zaškrtnutím **políčka ASP.NET Core hostovaného** [políčka (dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core Blazor slouží aplikaci klientům. Aplikace Blazor WebAssembly může pracovat se serverem v síti [SignalR](xref:signalr/introduction) <xref:tutorials/signalr-blazor-webassembly>pomocí volání webového rozhraní API nebo ( ).

Šablony obsahují skript, `blazor.webassembly.js` který zpracovává:

* Stahování za běhu .NET, aplikace a závislostí aplikace.
* Inicializace runtime pro spuštění aplikace.

Hostingový Blazor model WebAssembly nabízí několik výhod:

* Neexistuje žádná závislost na straně serveru .NET. Aplikace je plně funkční po stažení do klienta.
* Klientské prostředky a možnosti jsou plně využity.
* Práce je převedena ze serveru na klienta.
* K hostování aplikace není vyžadován ASP.NET webový server Core. Scénáře nasazení bez serveru jsou možné (například obsluha aplikace z CDN).

Hosting Blazor webových sestavení má nevýhody:

* Aplikace je omezena na možnosti prohlížeče.
* Je vyžadován schopný hardware a software klienta (například podpora webassemblyu).
* Velikost stahování je větší a načítání aplikací trvá déle.
* Doba běhu .NET a podpora nástrojů je méně vyspělá. Omezení existují například v podpoře a ladění [standardu .NET.](/dotnet/standard/net-standard)

Model Blazor hostované aplikace podporuje [kontejnery Dockeru](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Klikněte pravým tlačítkem myši na serverový projekt v sadě Visual Studio a vyberte **přidat** > **podporu Dockeru**.

## <a name="opno-locblazor-server"></a>BlazorServer

S Blazor modelem hostování serveru se aplikace spouští na serveru z aplikace ASP.NET Core. Aktualizace ui, zpracování událostí a volání JavaScriptu jsou zpracovávány prostředpou [SignalR](xref:signalr/introduction) připojení.

![Prohlížeč spolupracuje s aplikací (hostovanou uvnitř aplikace ASP.NET Core) na serveru přes SignalR připojení.](hosting-models/_static/blazor-server.png)

Chcete-li Blazor vytvořit Blazor aplikaci pomocí modelu hosting serveru, použijte šablonu aplikace ASP.NET Core ** Blazor Server** [(dotnet new blazorserver](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core Blazor je hostitelem SignalR aplikace Server a vytvoří koncový bod, ve kterém se klienti připojují.

Aplikace ASP.NET Core odkazuje na `Startup` třídu aplikace, která má přidat:

* Služby na straně serveru.
* Aplikace do kanálu zpracování požadavků.

Skript `blazor.server.js` naváže připojení klienta. Je odpovědností aplikace zachovat a obnovit stav aplikace podle potřeby (například v případě ztráty síťového připojení). Skript `blazor.server.js` je obsluhován z vloženého prostředku v ASP.NET sdíleném rámci Core.

Model Blazor hostování serveru nabízí několik výhod:

* Velikost stahování je výrazně Blazor menší než aplikace WebAssembly a aplikace se načítá mnohem rychleji.
* Aplikace plně využívá možnosti serveru, včetně použití libovolných rozhraní API kompatibilních s rozhraním .NET Core.
* Jádro .NET na serveru se používá ke spuštění aplikace, takže existující nástroje .NET, jako je ladění, fungují podle očekávání.
* Tenké klienti jsou podporovány. Serverové Blazor aplikace například pracují s prohlížeči, které nepodporují webovou sestavu, a na zařízeních s omezenými prostředky.
* Základ kódu aplikace .NET/C#, včetně kódu komponenty aplikace, se klientům nezobrazuje.

Tam jsou nevýhody Blazor server hosting:

* Vyšší latence obvykle existuje. Každá interakce uživatele zahrnuje síťový směrování.
* Neexistuje žádná offline podpora. Pokud se připojení klienta nezdaří, aplikace přestane fungovat.
* Škálovatelnost je náročná pro aplikace s mnoha uživateli. Server musí spravovat více klientských připojení a zpracovávat stav klienta.
* K poskytování aplikace je vyžadován ASP.NET základní server. Scénáře nasazení bez serveru nejsou možné (například obsluha aplikace z CDN).

Model Blazor aplikace Server podporuje [kontejnery Dockeru](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Klikněte pravým tlačítkem myši na projekt v sadě Visual Studio a vyberte **přidat** > **podporu Dockeru**.

### <a name="comparison-to-server-rendered-ui"></a>Porovnání s klientem vykresleným serverem

Jedním ze Blazor způsobů, jak porozumět serverovým aplikacím, je pochopit, jak se liší od tradičních modelů pro vykreslování uznatcí uASP.NET aplikace Core pomocí zobrazení Razor nebo Razor Pages. Oba modely používají jazyk Razor k popisu obsahu HTML, ale výrazně se liší v tom, jak jsou vykreslovány značky.

Při vykreslení stránky razor nebo zobrazení každý řádek kódu Razor vyzařuje HTML v textové podobě. Po vykreslování server naložit stránku nebo zobrazení instance, včetně všech stavů, které byly vytvořeny. Dojde-li k jinému požadavku na stránku, například když se ověření serveru nezdaří a zobrazí se souhrn ověření:

* Celá stránka je znovu vykreslena na text HTML.
* Stránka je odeslána klientovi.

Aplikace Blazor se skládá z opakovaně použitelných prvků uživatelského rozhraní *nazývaných komponenty*. Komponenta obsahuje kód Jazyka C#, značky a další součásti. Při vykreslení komponenty Blazor vytvoří graf zahrnutých součástí podobný objektovému modelu dokumentu HTML nebo XML (DOM). Tento graf zahrnuje stav součásti udržovaných ve vlastnostech a polích. Blazorvyhodnotí graf komponenty k vytvoření binární reprezentace značky. Binární formát může být:

* Přetaženo na&dagger;text HTML (během předběžného vykreslování).
* Používá se k efektivní aktualizaci značek během pravidelného vykreslování.

&dagger;*Předběžné vykreslování* &ndash; Požadovaná komponenta Razor je zkompilována na serveru do statického HTML a odeslána klientovi, kde je vykreslena uživateli. Po nastoupení spojení mezi klientem a serverem jsou statické předvykreslované prvky komponenty nahrazeny interaktivními prvky. Předběžné vykreslování umožňuje, aby aplikace lépe reagovala na uživatele.

Aktualizace ui Blazor v aplikaci se aktivuje:

* Interakce s uživatelem, například výběr tlačítka.
* Aktivační události aplikace, například časovač.

Graf je překreslován a vypočítá se *rozdíl* (rozdíl) ui. Tento rozdíl je nejmenší sada úprav DOM potřebné k aktualizaci ui na straně klienta. Rozdíl je odeslán klientovi v binárním formátu a použit prohlížečem.

Součást je uvolněna poté, co uživatel přejde od ní na straně klienta. Zatímco uživatel pracuje s komponentou, stav komponenty (služby, prostředky) musí být uložen v paměti serveru. Vzhledem k tomu, že stav mnoha součástí může být serverem udržován souběžně, vyčerpání paměti je problém, který je třeba řešit. Pokyny k vytvoření serverové Blazor aplikace, aby bylo zajištěno co <xref:security/blazor/server>nejlepší využití paměti serveru, naleznete v tématu .

### <a name="circuits"></a>Obvody

Serverová Blazor aplikace je postavená na [ASP.NET Core SignalR ](xref:signalr/introduction). Každý klient komunikuje se serverem SignalR prostředpou jednoho nebo více připojení nazývaných *okruh*. Okruh je Blazorodběr přes SignalR připojení, které mohou tolerovat přerušení dočasné sítě. Když Blazor klient zjistí, SignalR že je připojení odpojeno, pokusí se znovu připojit SignalR k serveru pomocí nového připojení.

Každá obrazovka prohlížeče (karta prohlížeče nebo iframe), která je připojena Blazor k aplikaci Server, SignalR používá připojení. To je další důležitý rozdíl ve srovnání s typickými servery vykreslené aplikace. V aplikaci vykreslené serverem otevření stejné aplikace na více obrazovkách prohlížeče obvykle nepřekládá do dalších požadavků na prostředky na serveru. V Blazor aplikaci Server každá obrazovka prohlížeče vyžaduje samostatný okruh a samostatné instance stavu součásti, které mají být spravovány serverem.

Blazorpovažuje zavření karty prohlížeče nebo přechod na externí adresu URL *za bezproblémové* ukončení. V případě řádnéukončení okruhu a přidružené prostředky jsou okamžitě uvolněny. Klient může také odpojit non-elegantně, například z důvodu přerušení sítě. BlazorServer ukládá odpojené obvody pro konfigurovatelný interval, aby se klient mohl znovu připojit.

BlazorServer umožňuje kódu definovat *obslužnou rutinu okruhu*, která umožňuje spuštění kódu při změnách stavu okruhu uživatele. Další informace naleznete v tématu <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latence ui

Latence ui je čas potřebný od iniciované akce do doby, kdy je aktualizováno ui. Menší hodnoty latence uživatelského rozhraní jsou nezbytné pro aplikaci, aby reagovala na uživatele. V Blazor aplikaci Server je každá akce odeslána na server, zpracována a rozdíl ui je odeslán zpět. V důsledku toho latence ui je součet latence sítě a latence serveru při zpracování akce.

U obchodní aplikace, která je omezena na soukromou podnikovou síť, je obvykle nepostřehnutelný vliv na vnímání latence uživatelem kvůli latenci sítě. U aplikace nasazené přes Internet může být latence pro uživatele znatelná, zejména pokud jsou uživatelé geograficky široce distribuováni.

Využití paměti může také přispět k latenci aplikace. Zvýšené využití paměti má za následek časté uvolňování paměti nebo stránkování paměti na disk, které snižují výkon aplikace a následně zvyšují latenci uživatelského prostředí. Další informace naleznete v tématu <xref:security/blazor/server>.

BlazorServerové aplikace by měly být optimalizovány tak, aby minimalizovaly latenci uznatých dat snížením latence sítě a využití paměti. Přístup k měření latence sítě <xref:host-and-deploy/blazor/server#measure-network-latency>naleznete v tématu . Další informace SignalR o Blazora například:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Připojení k serveru

BlazorServerové aplikace SignalR vyžadují aktivní připojení k serveru. Pokud dojde ke ztrátě připojení, aplikace se pokusí znovu připojit k serveru. Tak dlouho, dokud je stav klienta stále v paměti, relace klienta pokračuje bez ztráty stavu.

Aplikace Blazor Server se předvykresluje v reakci na první požadavek klienta, který nastaví stav ui na serveru. Pokud se klient pokusí SignalR vytvořit připojení, musí se znovu připojit ke stejnému serveru. BlazorServerové aplikace, které používají více než jeden SignalR back-endový server, by *měly* implementovat rychlé relace pro připojení.

Doporučujeme používat aplikace Blazor [Azure SignalR Service](/azure/azure-signalr) for Server. Služba umožňuje škálování aplikace Blazor server na velký počet SignalR souběžných připojení. Rychlé relace jsou povolené SignalR pro službu Azure `ServerStickyMode` nastavením možnosti `Required`služby nebo hodnoty konfigurace na . Další informace naleznete v tématu <xref:host-and-deploy/blazor/server#signalr-configuration>.

Při použití služby IIS jsou s směrováním požadavků na aplikace povoleny rychlé relace. Další informace naleznete v [tématu HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
