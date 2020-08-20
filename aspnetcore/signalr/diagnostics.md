---
title: Protokolování a diagnostika v ASP.NET Core SignalR
author: anurse
description: Naučte se shromažďovat diagnostiku z vaší SignalR aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr
ms.date: 06/12/2020
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
uid: signalr/diagnostics
ms.openlocfilehash: 649398a3868117b2e7f3358aa25544c99cc625b3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631339"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a>Protokolování a diagnostika v ASP.NET Core SignalR

Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)

Tento článek poskytuje pokyny pro shromažďování diagnostických informací z vaší SignalR aplikace ASP.NET Core, které vám pomůžou při řešení problémů.

## <a name="server-side-logging"></a>Protokolování na straně serveru

> [!WARNING]
> Protokoly na straně serveru můžou obsahovat citlivé informace z vaší aplikace. **Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.

Vzhledem k SignalR tomu, že je součást ASP.NET Core, používá systém protokolování ASP.NET Core. Ve výchozí konfiguraci SignalR protokoluje velmi málo informací, ale je možné ho nakonfigurovat. Podrobnosti o konfiguraci ASP.NET Core protokolování najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index#configuration) .

SignalR používá dvě kategorie protokolovacího nástroje:

* `Microsoft.AspNetCore.SignalR`: Protokoly týkající se protokolů centrálního centra, aktivace rozbočovačů, volání metod a dalších aktivit souvisejících s centrem.
* `Microsoft.AspNetCore.Http.Connections`: Pro protokoly týkající se přenosů, jako jsou WebSockets, dlouhé cyklické dotazování, události odeslané serverem a infrastruktura nízké úrovně SignalR .

Chcete-li povolit podrobné protokoly SignalR , nakonfigurujte obě předchozí předpony na `Debug` úroveň v *appsettings.js* souboru přidáním následujících položek do `LogLevel` dílčí části v `Logging` :

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

Tuto možnost lze také nakonfigurovat v kódu v `CreateWebHostBuilder` metodě:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Pokud nepoužíváte konfiguraci založenou na formátu JSON, nastavte v konfiguračním systému následující konfigurační hodnoty:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Informace o tom, jak zadat hodnoty vnořených konfigurací, najdete v dokumentaci ke konfiguračnímu systému. Například při použití proměnných prostředí `_` jsou místo `:` (například) použity dva znaky (například `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).

Tuto úroveň doporučujeme používat `Debug` při shromažďování podrobnějších diagnostických nástrojů pro vaši aplikaci. `Trace`Úroveň přináší vysoce nízkou diagnostiku a je zřídka nutná pro diagnostiku problémů ve vaší aplikaci.

## <a name="access-server-side-logs"></a>Přístup k protokolům na straně serveru

Způsob přístupu ke protokolům na straně serveru závisí na prostředí, ve kterém používáte.

### <a name="as-a-console-app-outside-iis"></a>Jako Konzolová aplikace mimo IIS

Pokud používáte konzolovou aplikaci, měl by být ve výchozím nastavení povolený [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console) . SignalR protokoly se zobrazí v konzole nástroje.

### <a name="within-iis-express-from-visual-studio"></a>V rámci IIS Express ze sady Visual Studio

Visual Studio zobrazí výstup protokolu v okně **výstup** . Vyberte možnost rozevíracího seznamu **ASP.NET Core webového serveru** .

### <a name="azure-app-service"></a>Azure App Service

V části **diagnostické protokoly** na portálu Azure App Service povolte možnost **protokolování aplikace (systém souborů)** a nakonfigurujte **úroveň** na `Verbose` . Protokoly by měly být dostupné ze služby **streamování protokolů** a v protokolech v systému souborů App Service. Další informace najdete v tématu [streamování protokolů Azure](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Další prostředí

Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows), najdete <xref:fundamentals/logging/index> Další informace o tom, jak nakonfigurovat zprostředkovatele protokolování vhodné pro prostředí.

## <a name="javascript-client-logging"></a>Protokolování klienta JavaScript

> [!WARNING]
> Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace. **Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.

Při použití klienta JavaScriptu můžete nakonfigurovat možnosti protokolování pomocí `configureLogging` metody na `HubConnectionBuilder` :

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.

V následující tabulce jsou uvedeny úrovně protokolu dostupné pro klienta jazyka JavaScript. Nastavením úrovně protokolu na jednu z těchto hodnot povolíte protokolování na této úrovni a všechny úrovně nad ním v tabulce.

| Úroveň | Popis |
| ----- | ----------- |
| `None` | Nejsou protokolovány žádné zprávy. |
| `Critical` | Zprávy indikující selhání v celé aplikaci. |
| `Error` | Zprávy indikující selhání aktuální operace. |
| `Warning` | Zprávy, které indikují méně závažnou chybu. |
| `Information` | Informační zprávy. |
| `Debug` | Diagnostické zprávy užitečné pro ladění. |
| `Trace` | Velmi podrobné diagnostické zprávy navržené pro diagnostiku konkrétních problémů. |

Po nakonfigurování podrobností se protokoly zapíší do konzoly prohlížeče (nebo standardního výstupu v aplikaci NodeJS).

Pokud chcete odesílat protokoly do vlastního systému protokolování, můžete poskytnout JavaScriptový objekt implementující `ILogger` rozhraní. Jedinou metodou, kterou je třeba implementovat, je `log` , která přebírá úroveň události a zprávu spojenou s událostí. Příklad:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>Protokolování klienta .NET

> [!WARNING]
> Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace. **Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.

Chcete-li získat protokoly z klienta rozhraní .NET, můžete použít `ConfigureLogging` metodu na `HubConnectionBuilder` . To funguje stejným způsobem jako `ConfigureLogging` metoda na `WebHostBuilder` a `HostBuilder` . Můžete nakonfigurovat stejné poskytovatele protokolování, které používáte v ASP.NET Core. Je však nutné ručně nainstalovat a povolit balíčky NuGet pro jednotlivé zprostředkovatele protokolování.

Pokud chcete do aplikace přidat protokolování klienta .NET Blazor WebAssembly , přečtěte si téma <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> .

### <a name="console-logging"></a>Protokolování konzoly

Aby bylo možné povolit protokolování konzoly, přidejte balíček [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) . Pak použijte `AddConsole` metodu ke konfiguraci protokolovacího nástroje konzoly:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Protokolování výstupního okna ladění

Můžete také nakonfigurovat protokoly pro přechod do okna **výstup** v aplikaci Visual Studio. Nainstalujte balíček [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) a použijte `AddDebug` metodu:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Další zprostředkovatelé protokolování

SignalR podporuje jiné poskytovatele protokolování, jako je Serilog, SEQ, NLog nebo jakýkoli jiný systém protokolování, který se integruje s `Microsoft.Extensions.Logging` . Pokud váš systém protokolování poskytuje `ILoggerProvider` , můžete ho zaregistrovat pomocí `AddProvider` :

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Podrobnost ovládacího prvku

Pokud se přihlašujete z jiných míst v aplikaci, Změna výchozí úrovně na `Debug` může být příliš podrobná. Pomocí filtru můžete nakonfigurovat úroveň protokolování pro SignalR protokoly. To lze provést v kódu, podobně jako na serveru:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Trasování sítě

> [!WARNING]
> Trasování sítě obsahuje úplný obsah každé zprávy odesílané vaší aplikací. **Nikdy** nezveřejňujte nezpracované síťové trasování z produkčních aplikací do veřejných fór, jako je GitHub.

Pokud narazíte na problém, trasování sítě může někdy poskytnout spoustu užitečných informací. To je užitečné hlavně v případě, že se chystáte zaslat problém do souboru sledování problémů.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Shromáždění trasování sítě pomocí Fiddler (upřednostňovaná možnost)

Tato metoda se používá pro všechny aplikace.

Fiddler je vysoce výkonný nástroj pro shromažďování trasování HTTP. Nainstalujte si ho z [Telerik.com/Fiddler](https://www.telerik.com/fiddler), spusťte ho a pak spusťte aplikaci a pokuste se problém reprodukování. Fiddler je k dispozici pro Windows a pro macOS a Linux jsou k dispozici beta verze.

Pokud se připojujete pomocí protokolu HTTPS, je potřeba provést několik kroků navíc, abyste zajistili, že Fiddler dokáže dešifrovat přenosy HTTPS. Další podrobnosti najdete v [dokumentaci k Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Po shromáždění trasování můžete trasování exportovat výběrem možnosti **soubor**  >  **Uložit**  >  **všechny relace** z řádku nabídek.

![Exportují se všechny relace z Fiddler.](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Shromažďovat trasování sítě pomocí tcpdump (jenom macOS a Linux)

Tato metoda se používá pro všechny aplikace.

Nezpracované trasování TCP můžete shromažďovat pomocí tcpdump spuštěním následujícího příkazu z příkazového prostředí. Pokud se zobrazí chyba oprávnění, může být nutné, abyste museli být `root` nebo začínat příkazem `sudo` .

```console
tcpdump -i [interface] -w trace.pcap
```

Nahraďte `[interface]` síťovým rozhraním, na kterém chcete zachytit. Obvykle se jedná o něco podobného `/dev/eth0` (pro standardní rozhraní sítě Ethernet) nebo `/dev/lo0` (pro přenosy v localhost). Další informace najdete na `tcpdump` stránce muž v hostitelském systému.

## <a name="collect-a-network-trace-in-the-browser"></a>Shromáždění trasování sítě v prohlížeči

Tato metoda funguje pouze pro aplikace založené na prohlížeči.

Většina prohlížečů Vývojářské nástroje mít kartu síť, která umožňuje zachytit síťovou aktivitu mezi prohlížečem a serverem. Tato trasování ale neobsahují zprávy protokolu WebSocket a události odeslané serverem. Pokud používáte tyto přenosy, je lepší přístup pomocí nástroje, jako je například Fiddler nebo TcpDump (popsané níže).

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge a Internet Explorer

(Pokyny jsou pro aplikaci Edge i Internet Explorer stejné.)

1. Stisknutím klávesy F12 otevřete nástroje pro vývoj.
2. Klikněte na kartu síť.
3. Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.
4. Kliknutím na ikonu Uložit na panelu nástrojů exportujte trasování jako soubor "HAR":

![Ikona uložit na kartě síť nástrojů Microsoft Edge pro vývoj](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Stisknutím klávesy F12 otevřete nástroje pro vývoj.
2. Klikněte na kartu síť.
3. Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.
4. Klikněte pravým tlačítkem na libovolné místo v seznamu požadavků a vyberte Uložit jako HAR s obsahem:

![Možnost Uložit jako HAR s obsahem na kartě síť Google Chrome dev Tools](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Stisknutím klávesy F12 otevřete nástroje pro vývoj.
2. Klikněte na kartu síť.
3. Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.
4. Klikněte pravým tlačítkem na libovolné místo v seznamu požadavků a vyberte Uložit vše jako HAR.

![Možnost Uložit vše jako HAR na kartě síť Mozilla Firefox nástroje pro vývojáře](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Připojení diagnostických souborů k problémům GitHubu

Diagnostické soubory můžete k problémům s GitHubem připojit tak, že je přejmenujete, aby měly `.txt` rozšíření a pak je přetáhnete na problém.

> [!NOTE]
> Do problému GitHubu prosím nevložíme obsah souborů protokolu ani trasování sítě. Tyto protokoly a trasování můžou být poměrně velké a GitHub je obvykle ořízne.

![Přetahování souborů protokolů na problém GitHubu](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a>Metriky

Metrika je reprezentace datových měr v časových intervalech. Například požadavky za sekundu. Data metrik umožňují sledovat stav aplikace na vysoké úrovni. Metriky .NET gRPC jsou vydávány pomocí <xref:System.Diagnostics.Tracing.EventCounter> .

### <a name="no-locsignalr-server-metrics"></a>SignalR metriky serveru

SignalR metriky serveru jsou hlášeny ve <xref:Microsoft.AspNetCore.Http.Connections> zdroji událostí.

| Název                    | Popis                 |
|-------------------------|-----------------------------|
| `connections-started`   | Celkový počet spuštěných připojení   |
| `connections-stopped`   | Celkový počet zastavených připojení   |
| `connections-timed-out` | Celkový počet připojení vypršel. |
| `current-connections`   | Aktuální připojení         |
| `connections-duration`  | Průměrná doba trvání připojení |

### <a name="observe-metrics"></a>Sledovat metriky

[dotnet – čítače](/dotnet/core/diagnostics/dotnet-counters) jsou nástrojem pro monitorování výkonu, který slouží ke sledování stavu ad-hoc a prvotnímu šetření výkonu na nejvyšší úrovni. Monitorujte aplikaci .NET s `Microsoft.AspNetCore.Http.Connections` názvem poskytovatele. Příklad:

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a>Další materiály

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
