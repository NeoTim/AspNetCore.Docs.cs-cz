---
title: Nahraďte machineKey ASP.NET do ASP.NET Core
author: rick-anderson
description: Objevte, jak nahradit machineKey v technologii ASP.NET k povolení používání systému ochrany dat nové a lepší zabezpečení.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468563"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="7df67-103">Nahraďte machineKey ASP.NET do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7df67-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="7df67-104">Provádění `<machineKey>` element v technologii ASP.NET [je nahraditelné](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="7df67-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="7df67-105">Většina volání kryptografické rutiny technologie ASP.NET pro ho směrovat pomocí ochranný mechanismus nahrazení data, včetně nového systému ochrany dat díky tomu.</span><span class="sxs-lookup"><span data-stu-id="7df67-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="7df67-106">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="7df67-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="7df67-107">Nový systém ochrany dat může být pouze nainstalována do stávající aplikace ASP.NET cílené na .NET 4.5.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="7df67-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="7df67-108">Instalace se nezdaří, pokud aplikace cílí na rozhraní .NET 4.5 nebo nižší.</span><span class="sxs-lookup"><span data-stu-id="7df67-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="7df67-109">Instalace nového systému ochrany dat do existujícího projektu 4.5.1+ technologie ASP.NET, nainstalujte balíček Microsoft.AspNetCore.DataProtection.SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="7df67-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="7df67-110">To vytvoří instanci pomocí systému ochrany dat [výchozí konfigurace](xref:security/data-protection/configuration/default-settings) nastavení.</span><span class="sxs-lookup"><span data-stu-id="7df67-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="7df67-111">Při instalaci balíčku vloží řádek do *Web.config* , které sděluje, ASP.NET, aby ji používat [nejvíce kryptografické operace](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), včetně ověřování pomocí formulářů, zobrazení stavu a volání MachineKey.Protect.</span><span class="sxs-lookup"><span data-stu-id="7df67-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="7df67-112">Řádek, který je vložen přečte následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="7df67-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="7df67-113">Poznáte, jestli je nový systém ochrany dat zkontrolováním pole, jako jsou aktivní `__VIEWSTATE`, který by měl začínat "CfDJ8" jako v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="7df67-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="7df67-114">"CfDJ8" je reprezentace base64 hlavičky magic "09 F0 C9 F0", který identifikuje datové části je chráněn systémem ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="7df67-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="7df67-115">Konfigurace balíčku</span><span class="sxs-lookup"><span data-stu-id="7df67-115">Package configuration</span></span>

<span data-ttu-id="7df67-116">Systém ochrany dat je vytvořena instance s výchozí konfigurací nastavení nula.</span><span class="sxs-lookup"><span data-stu-id="7df67-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="7df67-117">Ale vzhledem k tomu, že ve výchozím nastavení jsou trvalé klíče do místního systému souborů, to nebude fungovat pro aplikace, které jsou nasazené ve farmě.</span><span class="sxs-lookup"><span data-stu-id="7df67-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="7df67-118">Chcete-li tento problém vyřešit, můžete zadat konfigurace, které podtřídy DataProtectionStartup vytvořením typu a přepisuje metodu jeho ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="7df67-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="7df67-119">Níže je příklad vlastního datového typu spuštění ochrany, který nakonfigurované, kde jsou trvalé klíče i jak se šifrují při nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="7df67-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="7df67-120">Přepíše výchozí zásady izolace aplikací také zadáním názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="7df67-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

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
> <span data-ttu-id="7df67-121">Můžete také použít `<machineKey applicationName="my-app" ... />` místo explicitní volání konstruktoru SetApplicationName.</span><span class="sxs-lookup"><span data-stu-id="7df67-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="7df67-122">Jedná se o pohodlí mechanismus, aby vynucení pro vývojáře k vytvoření DataProtectionStartup odvozený typ, pokud vše, co se snaží konfigurace byla nastavení názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="7df67-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="7df67-123">Pokud chcete povolit tuto vlastní konfiguraci, vraťte se do souboru Web.config a vyhledejte `<appSettings>` element, který balíček nainstalovat do konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="7df67-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="7df67-124">Bude vypadat jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="7df67-124">It will look like the following markup:</span></span>

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

<span data-ttu-id="7df67-125">Zadejte prázdnou hodnotu s názvem kvalifikovaný pro sestavení DataProtectionStartup odvozený typ, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="7df67-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="7df67-126">Pokud je název aplikace DataProtectionDemo, to bude vypadat níže.</span><span class="sxs-lookup"><span data-stu-id="7df67-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="7df67-127">Systém ochrany nově nakonfigurovaný dat je teď připravený k použití v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7df67-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
