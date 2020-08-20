---
title: Nahraďte ASP.NET machineKey v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nahradit parametr machineKey v ASP.NET, aby bylo možné používat nový a bezpečnější systém ochrany dat.
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 8cae0b8f1c4582e272061ff87868b32568dfe595
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625567"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="cbc3b-103">Nahraďte ASP.NET machineKey v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cbc3b-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="cbc3b-104">Implementaci `<machineKey>` elementu v ASP.NET [je nahraditelný](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="cbc3b-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="cbc3b-105">To umožňuje směrování většiny volání ASP.NET kryptografických rutin pomocí náhradního mechanismu ochrany dat, včetně nového systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="cbc3b-106">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="cbc3b-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="cbc3b-107">Nový systém ochrany dat lze nainstalovat pouze do existující aplikace ASP.NET cílené na rozhraní .NET 4.5.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="cbc3b-108">Instalace se nezdaří, pokud je aplikace cílena na rozhraní .NET 4,5 nebo nižší.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="cbc3b-109">Chcete-li nainstalovat nový systém ochrany dat do existujícího projektu ASP.NET 4.5.1 +, nainstalujte balíček Microsoft.AspNetCore.DataProtection.SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="cbc3b-110">Tím se vytvoří instance systému ochrany dat s použitím [výchozích nastavení konfigurace](xref:security/data-protection/configuration/default-settings) .</span><span class="sxs-lookup"><span data-stu-id="cbc3b-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="cbc3b-111">Při instalaci balíčku vloží řádek do *Web.config* , který dává ASP.NET pokyn, aby ho používal pro [většinu kryptografických operací](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), včetně ověřování pomocí formulářů, stavu zobrazení a volání machineKey. Protect.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="cbc3b-112">Vložená čára načte následující text.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="cbc3b-113">To, jestli je nový systém ochrany dat aktivní, můžete zjistit tak, že zkontrolujete pole jako `__VIEWSTATE` , který by měl začínat řetězcem "CfDJ8", jak je uvedeno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="cbc3b-114">"CfDJ8" je reprezentace Base64 pro hlavičku Magic "09 F0 C9 F0", která identifikuje datovou část chráněnou systémem ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="cbc3b-115">Konfigurace balíčku</span><span class="sxs-lookup"><span data-stu-id="cbc3b-115">Package configuration</span></span>

<span data-ttu-id="cbc3b-116">Systém ochrany dat je vytvořen s výchozí konfigurací s nulovou instalací.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="cbc3b-117">Vzhledem k tomu, že ve výchozím nastavení se klíče ukládají do místního systému souborů, nebude tato funkce fungovat u aplikací, které jsou nasazené ve farmě.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="cbc3b-118">Chcete-li tento problém vyřešit, můžete poskytnout konfiguraci vytvořením typu, který podtřídy DataProtectionStartup a přepíše její metodu ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="cbc3b-119">Níže je uveden příklad vlastního typu spuštění ochrany dat, který konfiguruje, kde jsou klíče uložené a jak jsou zašifrované v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="cbc3b-120">Přepíše taky výchozí zásadu izolace aplikací tím, že poskytne vlastní název aplikace.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

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
> <span data-ttu-id="cbc3b-121">Můžete také použít `<machineKey applicationName="my-app" ... />` místo explicitního volání SetApplicationName.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="cbc3b-122">Toto je praktický mechanismus, který vám umožní vynutit, aby vývojář vytvořil typ odvozený od DataProtectionStartup, pokud všechny, které chtěli nakonfigurovat, nastavoval název aplikace.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="cbc3b-123">Tuto vlastní konfiguraci povolíte tak, že přejdete zpátky na Web.config a vyhledáte `<appSettings>` element, který balíček do konfiguračního souboru přidal.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="cbc3b-124">Bude vypadat jako v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="cbc3b-124">It will look like the following markup:</span></span>

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

<span data-ttu-id="cbc3b-125">Zadejte prázdnou hodnotu se kvalifikovaným názvem sestavení DataProtectionStartup odvozeného typu, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="cbc3b-126">Pokud je název aplikace DataProtectionDemo, může to vypadat podobně jako na následujícím obrázku.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="cbc3b-127">Nově konfigurovaný systém ochrany dat je teď připravený k použití v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="cbc3b-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
