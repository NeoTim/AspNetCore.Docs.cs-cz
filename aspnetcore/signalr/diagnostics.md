---
title: Protokolování a Diagnostika v knihovně SignalR technologie ASP.NET Core
author: anurse
description: Zjistěte, jak shromažďovat diagnostické z vaší aplikace SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 02/27/2019
uid: signalr/diagnostics
ms.openlocfilehash: b6bd21314ed183488999bcff3553e53493537a11
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400976"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>Protokolování a Diagnostika v knihovně SignalR technologie ASP.NET Core

Podle [Andrew Stanton sestry](https://twitter.com/anurse)

Tento článek obsahuje pokyny pro shromažďování diagnostiky z vaší aplikace ASP.NET Core SignalR k řešení potíží.

## <a name="server-side-logging"></a>Protokolování na straně serveru

> [!WARNING]
> Protokoly na straně serveru může obsahovat citlivé informace z vaší aplikace. **Nikdy** odeslání nezpracovaných protokolů z produkčních aplikací na veřejných fórech jako GitHub.

Protože SignalR je součástí ASP.NET Core, používá ASP.NET Core protokolování systému. Ve výchozí konfiguraci SignalR protokoly velmi málo informací, ale to může nakonfigurovat. Naleznete v dokumentaci [ASP.NET Core protokolování](xref:fundamentals/logging/index#configuration) podrobné informace o konfiguraci protokolování ASP.NET Core.

Funkce SignalR používá dvě kategorie protokolovacího nástroje:

* `Microsoft.AspNetCore.SignalR` -související s protokoly centra protokolů, aktivace rozbočovače, volání metod a dalších aktivit související s centrem.
* `Microsoft.AspNetCore.Http.Connections` -protokolů související s přenosy, jako jsou protokoly Websocket, dlouhé dotazování a Server-Sent události a nízké úrovně infrastruktuře SignalR.

Pokud chcete povolit podrobné protokoly ze systému SignalR, nakonfigurujte oba předchozí předpon `Debug` úrovni v vaše `appsettings.json` souboru tak, že přidáte následující položky, které chcete `LogLevel` v dílčím oddílu `Logging`:

[!code-json[Configuring logging](diagnostics/logging-config.json?highlight=7-8)]

Můžete to taky nakonfigurovat v kódu ve vaší `CreateWebHostBuilder` metody:

[!code-csharp[Configuring logging in code](diagnostics/logging-config-code.cs?highlight=5-6)]

Pokud nepoužíváte konfiguraci na základě JSON, nastavte následující hodnoty konfigurace v konfiguraci systému:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Dokumentaci k systému konfigurace a určit, jak zadat vnořené konfigurační hodnoty. Při použití proměnných prostředí, například dvě `_` znaky se používají místo `:` (jako například: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).

Doporučujeme použít `Debug` úroveň při shromažďování podrobnější diagnostiku pro aplikaci. `Trace` Úroveň vytváří velmi nízké úrovně diagnostiky a je málokdy potřeba diagnostikovat problémy ve vaší aplikaci.

## <a name="access-server-side-logs"></a>Zobrazení protokolů na straně serveru

Jak získat přístup k serverové protokoly, závisí na prostředí, ve kterém spouštíte.

### <a name="as-a-console-app-outside-iis"></a>Jako konzolové aplikace mimo službu IIS

Pokud máte spuštěný v konzolové aplikaci, [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console-provider) by měl být ve výchozím nastavení povolené. Funkce SignalR protokoly se zobrazí v konzole.

### <a name="within-iis-express-from-visual-studio"></a>V rámci služby IIS Express ze sady Visual Studio

Visual Studio zobrazí ve výstupu protokolu v **výstup** okna. Vyberte **Webový Server ASP.NET Core** rozevírací seznam možností.

### <a name="azure-app-service"></a>Azure App Service

Povolte možnost "Protokolování aplikace (systém souborů)" v části "Protokoly diagnostiky" portál služby Azure App Service a nakonfigurovat úroveň tak `Verbose`. Protokoly by měl být k dispozici služby "Streamování protokolů", stejně jako v protokolech systému souborů služby App Service. Další informace najdete v dokumentaci na [streamování protokolů Azure](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Další prostředí

Pokud máte spuštěný v jiném prostředí (Docker, Kubernetes, služba Windows atd.), naleznete v úplné dokumentaci [ASP.NET Core protokolování](xref:fundamentals/logging/index) Další informace o tom, jak konfigurovat protokolování zprostředkovatele pro vaše prostředí vhodný.

## <a name="javascript-client-logging"></a>Protokolování javascriptový klient

> [!WARNING]
> Protokoly na straně klienta mohou obsahovat citlivé informace z vaší aplikace. **Nikdy** odeslání nezpracovaných protokolů z produkčních aplikací na veřejných fórech jako GitHub.

Při použití Javascriptového klienta, můžete nakonfigurovat pomocí možnosti protokolování `configureLogging` metodu na `HubConnectionBuilder`:

[!code-javascript[Configuring logging in the JavaScript client](diagnostics/logging-config-js.js?highlight=3)]

Chcete-li zakázat protokolování úplně, zadejte `signalR.LogLevel.None` v `configureLogging` metody.

Následující tabulka uvádí dostupné úrovně protokolu klientovi JavaScript. Nastavení úrovně protokolu na jednu z těchto hodnot povolí protokolování na této úrovni ve všech úrovních nad ním v tabulce.

| úroveň | Popis |
| ----- | ----------- |
| `None` | Jsou zaznamenány žádné zprávy. |
| `Critical` | Zprávy, které indikují chybu celé aplikace. |
| `Error` | Zprávy, které indikují chybu aktuální operaci. |
| `Warning` | Zprávy, které označují méně závažné potíže. |
| `Information` | Informační zprávy. |
| `Debug` | Diagnostické zprávy je užitečné pro ladění. |
| `Trace` | Velmi podrobné diagnostické zprávy, které jsou navržené pro diagnostiku specifické problémy. |

Jakmile nakonfigurujete úroveň podrobností, protokoly se zapíšou do konzoly prohlížeče (nebo standardní výstup v aplikaci NodeJS).

Pokud chcete odeslat protokoly do vlastního protokolování systému, můžete poskytnout implementaci objektu jazyka JavaScript `ILogger` rozhraní. Je jedinou metodou, kterou je potřeba implementovat `log`, který by úroveň události a zprávy přidružené k události. Příklad:

[!code-typescript[Creating a custom logger](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET client protokolování

> [!WARNING]
> Protokoly na straně klienta mohou obsahovat citlivé informace z vaší aplikace. **Nikdy** odeslání nezpracovaných protokolů z produkčních aplikací na veřejných fórech jako GitHub.

Pokud chcete získat protokoly z klienta .NET, můžete použít `ConfigureLogging` metodu na `HubConnectionBuilder`. Tento postup funguje stejným způsobem jako `ConfigureLogging` metoda `WebHostBuilder` a `HostBuilder`. Můžete nakonfigurovat stejní poskytovatelé protokolování, které můžete použít v ASP.NET Core. Ale musíte ručně nainstalovat a povolit protokolování jednotlivých poskytovatelů balíčky NuGet.

### <a name="console-logging"></a>Protokolování konzoly

Chcete-li povolit protokolování konzoly, přidejte [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) balíčku. Potom použijte `AddConsole` metoda konfigurace protokolovací nástroj konzoly:

[!code-csharp[Configuring console logging in .NET client](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Protokolování okna výstup ladění

Můžete taky nakonfigurovat protokoly a přejděte **výstup** okna v sadě Visual Studio. Instalace [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) balíček a použít `AddDebug` metody:

[!code-csharp[Configuring debug output window logging in .NET client](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Ostatní zprostředkovatelé protokolování

Funkce SignalR podporuje jiných poskytovatelů protokolování, jako je například Serilog, Seq, NLog nebo jakémkoli jiném systému protokolování, která se integruje s `Microsoft.Extensions.Logging`. Pokud váš systém protokolování poskytuje `ILoggerProvider`, můžete ho zaregistrovat `AddProvider`:

[!code-csharp[Configuring a custom logging provider in .NET client](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Podrobnosti ovládacího prvku

Pokud se přihlašujete z jiných míst ve své aplikaci, změna výchozí úrovně do `Debug` může být příliš podrobné. Filtr můžete nakonfigurovat úroveň protokolování pro protokoly SignalR. To můžete udělat v kódu, téměř stejným způsobem jako na serveru:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Trasování sítě

> [!WARNING]
> Trasování sítě obsahuje úplný obsah všechny zprávy odeslané aplikací. **Nikdy** odeslání nezpracovaná síťových trasování z produkčních aplikací na veřejných fórech jako GitHub.

Pokud narazíte na problém, trasování sítě někdy poskytnout velké množství užitečných informací. To je zvlášť užitečné, pokud se chystáte založit problém na našem sledování problémů.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Shromažďovat trasování pomocí fiddleru provedete (upřednostňovanou možnost ověřování) v síti

Tato metoda se dá použít pro všechny aplikace.

Fiddler je velmi výkonný nástroj pro shromažďování trasování protokolu HTTP. Nainstalujte ji z [telerik.com/fiddler](https://www.telerik.com/fiddler), spusťte jej a pak spusťte aplikaci a reprodukujte problém. Fiddler je k dispozici pro Windows a jsou beta verze pro macOS a Linux.

Pokud se připojíte pomocí protokolu HTTPS, existuje několik kroků navíc zajistěte, aby že Fiddler můžete dešifrování provozu HTTPS. Další podrobnosti najdete v tématu [dokumentace k aplikaci Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Jakmile jste shromážděné trasování, můžete to taky trasování výběrem **souboru** > **Uložit** > **všechny relace...**  z řádku nabídek.

![Export všech relacích z Fiddleru](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Shromažďovat trasování v síti s tcpdump (macOS a Linux jenom)

Tato metoda se dá použít pro všechny aplikace.

Můžete shromažďovat nezpracovaná TCP trasování pomocí tcpdump spuštěním následujícího příkazu z příkazového prostředí. Musíte být `root` nebo příkazu u předpony `sudo` Pokud dojde k chybě oprávnění:

```console
tcpdump -i [interface] -w trace.pcap
```

Nahraďte `[interface]` se síťovým rozhraním, kterou chcete zachytit na. To je obvykle něco jako `/dev/eth0` (pro standardní rozhraní sítě Ethernet) nebo `/dev/lo0` (pro přenos localhost). Další informace najdete v tématu `tcpdump` man stránku v systému hostitele.

## <a name="collect-a-network-trace-in-the-browser"></a>Shromažďovat trasování sítě v prohlížeči

Tato metoda funguje jenom pro aplikace založené na prohlížeči.

Většina nástrojů pro vývojáře prohlížeče mít kartu "Sítě", která umožňuje zaznamenat síťové aktivity mezi prohlížečem a serveru. Toto trasování však nezahrnují zprávy protokolu WebSocket a Server-Sent událostí. Pokud používáte tyto přenosy, pomocí nástroje, jako jsou nástroje Fiddler nebo TcpDump (popsaných níže) není lepším řešením.

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge a Internet Explorer

(Pokyny jsou stejné pro Edge a Internet Explorer)

1. Stisknutím klávesy F12 otevřete Nástroje pro vývojáře
2. Klikněte na kartu síť
3. Aktualizujte stránku (v případě potřeby) a reprodukujte problém
4. Klikněte na ikonu Uložit na panelu nástrojů pro export trasování jako soubor "HAR":

![Uložení ikonu na vývoj pro Microsoft Edge nástroje síťové karty](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Stisknutím klávesy F12 otevřete Nástroje pro vývojáře
2. Klikněte na kartu síť
3. Aktualizujte stránku (v případě potřeby) a reprodukujte problém
4. Klikněte pravým tlačítkem myši klikněte na libovolné místo v seznamu požadavků a zvolte možnost "Uložit jako HAR s obsahem":

![Možnost "Uložit jako HAR s obsahem" Google Chrome Dev Tools síťové kartě](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Stisknutím klávesy F12 otevřete Nástroje pro vývojáře
2. Klikněte na kartu síť
3. Aktualizujte stránku (v případě potřeby) a reprodukujte problém
4. Klikněte pravým tlačítkem myši klikněte na libovolné místo v seznamu požadavků a zvolte možnost "Uložit všechny jako HAR"

![Možnost "Uložit vše jako HAR" Mozilla Firefox Dev Tools síťové kartě](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Připojit soubory diagnostiky problémů Githubu

Soubory diagnostiky můžete připojit na problémy Githubu je přejmenováním mají `.txt` rozšíření a pak přetažení je k problému.

> [!NOTE]
> Prosím není vložte obsah souborů protokolu nebo trasování sítě v Githubu u problému. Tyto protokoly a trasování může mít poměrně značnou a Githubu obvykle zkrátí je.

![Přetahování souborů protokolů do problém na Githubu](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
