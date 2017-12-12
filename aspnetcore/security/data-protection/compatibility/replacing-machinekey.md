---
title: "Nahrazení < machineKey > technologie ASP.NET"
author: rick-anderson
description: "Nahrazení < machineKey > technologie ASP.NET"
keywords: "ASP.NET Core, zabezpečení, < machineKey > machineKey"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5ac13589-3837-4b4d-8abe-81f843942120
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: b5a1be5fee7489f266e8a676956f68b499c6f14f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
# <a name="replacing-machinekey-in-aspnet"></a><span data-ttu-id="0e2c8-104">Nahrazení `<machineKey>` technologie ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0e2c8-104">Replacing `<machineKey>` in ASP.NET</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="0e2c8-105">Implementace `<machineKey>` element technologie ASP.NET [je replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="0e2c8-105">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="0e2c8-106">To umožňuje většinu volání kryptografické rutiny ASP.NET prostřednictvím ochranný mechanismus nahrazení data, včetně nového systému ochrany dat směrování.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-106">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="0e2c8-107">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="0e2c8-107">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="0e2c8-108">Nový systém ochrany dat pouze lze nainstalovat do stávající aplikaci ASP.NET cílení na rozhraní .NET 4.5.1 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-108">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or higher.</span></span> <span data-ttu-id="0e2c8-109">Instalace se nezdaří, pokud aplikace cílí .NET 4.5 nebo nižší.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-109">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="0e2c8-110">Instalace nového systému ochrany dat do existujícího projektu ASP.NET 4.5.1+, nainstalujte balíček Microsoft.AspNetCore.DataProtection.SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-110">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="0e2c8-111">To vytvoří instanci systému ochrany dat pomocí [výchozí konfigurace](xref:security/data-protection/configuration/default-settings) nastavení.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-111">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="0e2c8-112">Při instalaci balíčku se vloží řádek do *Web.config* použít pro technologii ASP.NET, která oznamuje [nejvíce kryptografické operace](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), včetně ověřování pomocí formulářů, zobrazení stavu a volání MachineKey.Protect.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-112">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="0e2c8-113">Řádek, který je vložen přečte následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-113">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="0e2c8-114">Můžete zjistit, pokud je nový systém ochrany dat active zkontrolováním pole jako `__VIEWSTATE`, který by měl začínat obráceným "CfDJ8" jako v příkladu níže.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-114">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="0e2c8-115">"CfDJ8" je base64 reprezentace záhlaví magic "09 F0 C9 F0", který identifikuje chráněného systémem ochrany dat datové části.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-115">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk..." />
```

## <a name="package-configuration"></a><span data-ttu-id="0e2c8-116">Konfigurace balíčku</span><span class="sxs-lookup"><span data-stu-id="0e2c8-116">Package configuration</span></span>

<span data-ttu-id="0e2c8-117">Vytvoření instance systému ochrany dat s výchozí konfigurací nastavení nula.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-117">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="0e2c8-118">Ale vzhledem k tomu, že ve výchozím nastavení jsou trvalé klíče do místního systému souborů, to nebude fungovat pro aplikace, které jsou nasazené ve farmě.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-118">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="0e2c8-119">To vyřešit, můžete zadat konfiguraci vytvořením typu které podtřídy DataProtectionStartup a přepíše jeho ConfigureServices metoda.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-119">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="0e2c8-120">Dole je příklad typ spuštění ochrany vlastních dat, který nakonfigurovaný, kde jsou klíče nastavené jako trvalé i jak jste zašifrovaná přinejmenším.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-120">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="0e2c8-121">Potlačí výchozí zásady izolace aplikací také tím, že poskytuje svůj vlastní název aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-121">It also overrides the default app isolation policy by providing its own application name.</span></span>

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
> <span data-ttu-id="0e2c8-122">Můžete také použít `<machineKey applicationName="my-app" ... />` místo explicitní volání SetApplicationName.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-122">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="0e2c8-123">Jedná se o usnadnění práce mechanismus, aby se zabránilo vynucení vývojáře pro vytvoření typu odvozeného DataProtectionStartup, pokud se všechny chtěli nakonfigurovat nastavení název aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-123">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="0e2c8-124">Pokud chcete povolit tento vlastní konfigurace, přejděte zpět do souboru Web.config a vyhledejte `<appSettings>` element, který nainstalujte balíček přidat do konfiguračního souboru.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-124">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="0e2c8-125">Ho bude vypadat jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="0e2c8-125">It will look like the following markup:</span></span>

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

<span data-ttu-id="0e2c8-126">Zadejte prázdnou hodnotu s názvem kvalifikovaný sestavení DataProtectionStartup odvozeného typu, který jste právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-126">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="0e2c8-127">Pokud název aplikace je DataProtectionDemo, to bude vypadat níže.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-127">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="0e2c8-128">Nově nakonfigurovaný data ochrany systému je nyní připravena k použití uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e2c8-128">The newly-configured data protection system is now ready for use inside the application.</span></span>
