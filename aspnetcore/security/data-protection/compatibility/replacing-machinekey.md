---
title: Nahraďte machineKey ASP.NET do ASP.NET Core
author: rick-anderson
description: Objevte, jak nahradit machineKey v technologii ASP.NET k povolení používání systému ochrany dat nové a lepší zabezpečení.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705546"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Nahraďte machineKey ASP.NET do ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

Provádění `<machineKey>` element v technologii ASP.NET [je nahraditelné](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Většina volání kryptografické rutiny technologie ASP.NET pro ho směrovat pomocí ochranný mechanismus nahrazení data, včetně nového systému ochrany dat díky tomu.

## <a name="package-installation"></a>Instalace balíčku

> [!NOTE]
> Nový systém ochrany dat může být pouze nainstalována do stávající aplikace ASP.NET cílené na .NET 4.5.1 nebo novější. Instalace se nezdaří, pokud aplikace cílí na rozhraní .NET 4.5 nebo nižší.

Instalace nového systému ochrany dat do existujícího projektu 4.5.1+ technologie ASP.NET, nainstalujte balíček Microsoft.AspNetCore.DataProtection.SystemWeb. To vytvoří instanci pomocí systému ochrany dat [výchozí konfigurace](xref:security/data-protection/configuration/default-settings) nastavení.

Při instalaci balíčku vloží řádek do *Web.config* , které sděluje, ASP.NET, aby ji používat [nejvíce kryptografické operace](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), včetně ověřování pomocí formulářů, zobrazení stavu a volání MachineKey.Protect. Řádek, který je vložen přečte následujícím způsobem.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Poznáte, jestli je nový systém ochrany dat zkontrolováním pole, jako jsou aktivní `__VIEWSTATE`, který by měl začínat "CfDJ8" jako v následujícím příkladu. "CfDJ8" je reprezentace base64 hlavičky magic "09 F0 C9 F0", který identifikuje datové části je chráněn systémem ochrany dat.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Konfigurace balíčku

Systém ochrany dat je vytvořena instance s výchozí konfigurací nastavení nula. Ale vzhledem k tomu, že ve výchozím nastavení jsou trvalé klíče do místního systému souborů, to nebude fungovat pro aplikace, které jsou nasazené ve farmě. Chcete-li tento problém vyřešit, můžete zadat konfigurace, které podtřídy DataProtectionStartup vytvořením typu a přepisuje metodu jeho ConfigureServices.

Níže je příklad vlastního datového typu spuštění ochrany, který nakonfigurované, kde jsou trvalé klíče i jak se šifrují při nečinnosti. Přepíše výchozí zásady izolace aplikací také zadáním názvu aplikace.

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> Můžete také použít `<machineKey applicationName="my-app" ... />` místo explicitní volání konstruktoru SetApplicationName. Jedná se o pohodlí mechanismus, aby vynucení pro vývojáře k vytvoření DataProtectionStartup odvozený typ, pokud vše, co se snaží konfigurace byla nastavení názvu aplikace.

Pokud chcete povolit tuto vlastní konfiguraci, vraťte se do souboru Web.config a vyhledejte `<appSettings>` element, který balíček nainstalovat do konfiguračního souboru. Bude vypadat jako následující kód:

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

Zadejte prázdnou hodnotu s názvem kvalifikovaný pro sestavení DataProtectionStartup odvozený typ, který jste právě vytvořili. Pokud je název aplikace DataProtectionDemo, to bude vypadat níže.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Systém ochrany nově nakonfigurovaný dat je teď připravený k použití v aplikaci.
