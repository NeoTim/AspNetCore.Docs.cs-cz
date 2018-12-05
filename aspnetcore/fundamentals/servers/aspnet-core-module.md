---
title: Modul ASP.NET Core
author: guardrex
description: Zjistěte, jak modul ASP.NET Core umožňuje Kestrel webový server pro použití služby IIS nebo IIS Express.
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/30/2018
uid: fundamentals/servers/aspnet-core-module
ms.openlocfilehash: d3f3a42dd7aebc425905b865376a584bcf0e5153
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861456"
---
# <a name="aspnet-core-module"></a>Modul ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), a [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-2.2"

Modul ASP.NET Core umožňuje aplikacím pro spuštění v pracovním procesu služby IIS, ASP.NET Core (*vnitroprocesové*) nebo za služby IIS v konfigurace reverzního proxy serveru (*mimo proces*). Služba IIS poskytuje funkce zabezpečení a správy rozšířené webové aplikace.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Modul ASP.NET Core umožňuje spouštění za služby IIS v konfigurace reverzního proxy aplikací pro ASP.NET Core. Služba IIS poskytuje funkce zabezpečení a správy rozšířené webové aplikace.

::: moniker-end

Podporované verze Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

::: moniker range=">= aspnetcore-2.2"

Při hostování v procesu, modul používá implementaci v procesový server služby IIS, HTTP serveru služby IIS (`IISHttpServer`).

Při hostování mimo proces, modul funguje pouze s Kestrel. Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys) (dříve se označovaly jako [WebListener](xref:fundamentals/servers/weblistener)).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Modul funguje pouze s Kestrel. Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys) (dříve se označovaly jako [WebListener](xref:fundamentals/servers/weblistener)).

::: moniker-end

## <a name="aspnet-core-module-description"></a>Popis modulu jádra ASP.NET

::: moniker range=">= aspnetcore-2.2"

Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS buď:

* Hostování aplikace v ASP.NET Core v rámci pracovní proces služby IIS (`w3wp.exe`), označované jako [model hostingu v procesu](#in-process-hosting-model).

* Předání požadavku na webové aplikace ASP.NET Core s back-end systémem [Kestrel server](xref:fundamentals/servers/kestrel), označované jako [model hostingu mimo proces](#out-of-process-hosting-model).

### <a name="in-process-hosting-model"></a>Model hostingu v procesu

Proces hostování, ASP.NET Core pomocí app běží ve stejném procesu jako jeho pracovní proces služby IIS. Tím snížení výkonu v důsledku žádosti o připojení přes server proxy prostřednictvím adaptéru zpětné smyčky, při použití model hostingu mimo proces. Služba IIS zpracovává proces správy pomocí [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Modul ASP.NET Core:

* Provede inicializaci aplikace.
  * Načtení [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Volání `Program.Main`.
* Zpracovává životnost nativní požadavků služby IIS.

Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované v procesu:

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-inprocess.png)

Žádost o přijetí z webu pro ovladač HTTP.sys režimu jádra. Ovladač přesměruje požadavek na nativní služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul obdrží požadavek na nativní a předává je na serveru služby IIS protokolu HTTP (`IISHttpServer`). Server služby IIS protokolu HTTP je implementace v rámci procesu serveru služby IIS, která převede žádosti z nativní do spravované.

Po zpracování požadavku HTTP Server služby IIS požadavek odesílají do kanálu middleware ASP.NET Core. Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky. Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta, který inicioval žádost.

### <a name="out-of-process-hosting-model"></a>Model hostingu mimo proces

Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul Správa procesů. V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud se vypne nebo dojde k chybě. Toto je v podstatě stejné chování jako aplikace, které běží v procesu, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované na více instancí procesu:

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra. Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS). V modulu předává požadavky Kestrel na náhodný port pro aplikaci, která není port 80 a 443.

V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`. Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu. Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.

Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core. Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky. Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel. Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS, aby předával aplikace webové požadavky do back-endu ASP.NET Core.

Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul také procesu správy. V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud ho dojde k chybě. Toto je v podstatě stejné chování jako aplikace ASP.NET 4.x, na kterých běží v procesu ve službě IIS, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, že modul ASP.NET Core a aplikace:

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra. Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS). V modulu předává požadavky Kestrel na náhodný port pro aplikaci, která není port 80 a 443.

V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`. Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu. Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.

Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core. Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky. Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel. Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.

::: moniker-end

Množství nativních modulů, jako je například ověřování Windows, zůstanou aktivní. Další informace o moduly IIS aktivní s modulem, naleznete v tématu <xref:host-and-deploy/iis/modules>.

Modul ASP.NET Core má několik dalších funkcí. Modul můžete:

* Nastavení proměnných prostředí pro pracovní proces.
* Zaznamená stdout výstup do služby file storage pro řešení potíží při spuštění.
* Předávání ověřovacích tokenů Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat modul ASP.NET Core

Podrobné pokyny o tom, jak nainstalovat a používat modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/index>. Informace o konfiguraci modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* [Úložiště GitHub modulu jádra ASP.NET (zdrojového kódu)](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
