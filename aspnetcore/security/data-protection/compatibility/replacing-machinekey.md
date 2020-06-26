---
title: Nahraďte ASP.NET machineKey v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nahradit parametr machineKey v ASP.NET, aby bylo možné používat nový a bezpečnější systém ochrany dat.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: db041ab4939fc7c39ac01cc02e350aca2fbee93e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400541"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Nahraďte ASP.NET machineKey v ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

Implementaci `<machineKey>` elementu v ASP.NET [je nahraditelný](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). To umožňuje směrování většiny volání ASP.NET kryptografických rutin pomocí náhradního mechanismu ochrany dat, včetně nového systému ochrany dat.

## <a name="package-installation"></a>Instalace balíčku

> [!NOTE]
> Nový systém ochrany dat lze nainstalovat pouze do existující aplikace ASP.NET cílené na rozhraní .NET 4.5.1 nebo novější. Instalace se nezdaří, pokud je aplikace cílena na rozhraní .NET 4,5 nebo nižší.

Chcete-li nainstalovat nový systém ochrany dat do existujícího projektu ASP.NET 4.5.1 +, nainstalujte balíček Microsoft.AspNetCore.DataProtection.SystemWeb. Tím se vytvoří instance systému ochrany dat s použitím [výchozích nastavení konfigurace](xref:security/data-protection/configuration/default-settings) .

Při instalaci balíčku vloží řádek do *Web.config* , který dává ASP.NET pokyn, aby ho používal pro [většinu kryptografických operací](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), včetně ověřování pomocí formulářů, stavu zobrazení a volání machineKey. Protect. Vložená čára načte následující text.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> To, jestli je nový systém ochrany dat aktivní, můžete zjistit tak, že zkontrolujete pole jako `__VIEWSTATE` , který by měl začínat řetězcem "CfDJ8", jak je uvedeno v následujícím příkladu. "CfDJ8" je reprezentace Base64 pro hlavičku Magic "09 F0 C9 F0", která identifikuje datovou část chráněnou systémem ochrany dat.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Konfigurace balíčku

Systém ochrany dat je vytvořen s výchozí konfigurací s nulovou instalací. Vzhledem k tomu, že ve výchozím nastavení se klíče ukládají do místního systému souborů, nebude tato funkce fungovat u aplikací, které jsou nasazené ve farmě. Chcete-li tento problém vyřešit, můžete poskytnout konfiguraci vytvořením typu, který podtřídy DataProtectionStartup a přepíše její metodu ConfigureServices.

Níže je uveden příklad vlastního typu spuštění ochrany dat, který konfiguruje, kde jsou klíče uložené a jak jsou zašifrované v klidovém stavu. Přepíše taky výchozí zásadu izolace aplikací tím, že poskytne vlastní název aplikace.

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
> Můžete také použít `<machineKey applicationName="my-app" ... />` místo explicitního volání SetApplicationName. Toto je praktický mechanismus, který vám umožní vynutit, aby vývojář vytvořil typ odvozený od DataProtectionStartup, pokud všechny, které chtěli nakonfigurovat, nastavoval název aplikace.

Tuto vlastní konfiguraci povolíte tak, že přejdete zpátky na Web.config a vyhledáte `<appSettings>` element, který balíček do konfiguračního souboru přidal. Bude vypadat jako v následujícím kódu:

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

Zadejte prázdnou hodnotu se kvalifikovaným názvem sestavení DataProtectionStartup odvozeného typu, který jste právě vytvořili. Pokud je název aplikace DataProtectionDemo, může to vypadat podobně jako na následujícím obrázku.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Nově konfigurovaný systém ochrany dat je teď připravený k použití v rámci aplikace.
