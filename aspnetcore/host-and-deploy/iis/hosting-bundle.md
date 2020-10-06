---
title: Hostující sada
author: rick-anderson
description: Naučte se konfigurovat hostující sadu .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755261"
---
# <a name="the-net-core-hosting-bundle"></a>Hostující sada .NET Core

Hostující sada .NET Core je instalační program pro modul runtime .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Tato sada umožňuje, aby aplikace ASP.NET Core běžely se službou IIS.

## <a name="install-the-net-core-hosting-bundle"></a>Instalace hostující sady .NET Core

> [!IMPORTANT]
> Pokud je hostující sada nainstalována před službou IIS, je nutné opravit instalaci sady prostředků. Spusťte znovu instalační program hostující sady po instalaci služby IIS.
>
> Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .

### <a name="direct-download-current-version"></a>Přímé stažení (aktuální verze)

Stáhněte instalační program pomocí následujícího odkazu:

[Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starší verze instalačního programu

Chcete-li získat starší verzi instalačního programu:

1. Přejděte na stránku [stáhnout jádro .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Vyberte požadovanou verzi .NET Core.
1. Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.
1. Stáhněte instalační program pomocí odkazu **hostující sada** .

> [!WARNING]
> Některé instalační programy obsahují verze vydaných verzí, které dosáhly svého konce životnosti (konce řádku) a které už nejsou podporovány společností Microsoft. Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Instalace hostitelského balíčku

1. Spusťte instalační program na serveru. Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:

   * `OPT_NO_ANCM=1`: Přeskočit instalaci modulu ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Přeskočí instalace modulu runtime .NET Core. Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Přeskočí instalace sdíleného rozhraní ASP.NET (ASP.NET Runtime). Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Přeskočí instalace běhových prostředí x86. Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace. Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Pokud je sdílená konfigurace ( `applicationHost.config` ) na stejném počítači jako instalace služby IIS, zakažte kontrolu použití sdílené konfigurace služby IIS. *K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.* Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:

   ```console
   net stop was /y
   net start w3svc
   ```
   Restartování služby IIS zabere změny v systémové cestě, což je proměnná prostředí vytvořená instalačním programem.

ASP.NET Core nesplňuje chování při přeposílání pro vydání oprav pro balíčky sdílených rozhraní. Po upgradu sdíleného rozhraní instalací nové hostitelské sady restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Verze modulu a instalační protokoly hostitelské sady

Postup určení verze nainstalovaného modulu ASP.NET Core:

1. V hostitelském systému přejděte na `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .
1. Vyhledejte `aspnetcorev2.dll` soubor.
1. Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .
1. Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.

V umístění se nacházejí protokoly instalačního balíčku pro modul `C:\Users\%UserName%\AppData\Local\Temp` . Soubor je pojmenován `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , kde zástupný symbol `{TIMESTAMP}` je časové razítko souboru.
