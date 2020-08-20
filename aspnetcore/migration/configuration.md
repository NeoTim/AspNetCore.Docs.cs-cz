---
title: Migrace konfigurace do ASP.NET Core
author: ardalis
description: Naučte se migrovat konfiguraci z projektu ASP.NET MVC do projektu MVC ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: 8cbb174539234039a05306ec5ab89026bc90da23
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631092"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="8f62d-103">Migrace konfigurace do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f62d-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="8f62d-104">[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="8f62d-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="8f62d-105">V předchozím článku jsme začali [migrovat projekt ASP.NET MVC na ASP.NET Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="8f62d-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="8f62d-106">V tomto článku migrujeme konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="8f62d-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="8f62d-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8f62d-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="8f62d-108">Konfigurace instalace</span><span class="sxs-lookup"><span data-stu-id="8f62d-108">Setup configuration</span></span>

<span data-ttu-id="8f62d-109">ASP.NET Core už nepoužívá soubory *Global. asax* a *web.config* , které využily předchozí verze ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="8f62d-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="8f62d-110">V dřívějších verzích ASP.NET byla logika spouštění aplikace umístěna v `Application_StartUp` metodě v rámci *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="8f62d-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="8f62d-111">Později v ASP.NET MVC byl do kořene projektu zahrnut soubor *Startup.cs* ; a bylo voláno při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8f62d-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="8f62d-112">ASP.NET Core úspěšně přijal tento přístup tím, že do souboru *Startup.cs* umístí veškerou spouštěcí logiku.</span><span class="sxs-lookup"><span data-stu-id="8f62d-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="8f62d-113">*web.config* soubor byl také v ASP.NET Core nahrazen.</span><span class="sxs-lookup"><span data-stu-id="8f62d-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="8f62d-114">Vlastní konfiguraci teď můžete nakonfigurovat jako součást postupu spuštění aplikace popsaného v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="8f62d-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="8f62d-115">Konfigurace může stále využívat soubory XML, ale obvykle ASP.NET Core projekty umístí konfigurační hodnoty do souboru ve formátu JSON, jako je například *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="8f62d-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="8f62d-116">Konfigurační systém ASP.NET Core může také snadno získat přístup k proměnným prostředí, což může poskytovat [bezpečnější a robustní umístění](xref:security/app-secrets) pro hodnoty specifické pro prostředí.</span><span class="sxs-lookup"><span data-stu-id="8f62d-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="8f62d-117">To platí zejména pro tajné klíče, jako jsou připojovací řetězce a klíče rozhraní API, které by neměly být vráceny do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="8f62d-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="8f62d-118">Další informace o konfiguraci v ASP.NET Core najdete v tématu o [konfiguraci](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="8f62d-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="8f62d-119">V tomto článku Začínáme s částečně migrovaným ASP.NET Core projektem z [předchozího článku](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="8f62d-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="8f62d-120">Chcete-li nastavit konfiguraci, přidejte do souboru *Startup.cs* , který se nachází v kořenovém adresáři projektu, následující konstruktor a vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8f62d-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="8f62d-121">Všimněte si, že v tomto okamžiku se soubor *Startup.cs* nebude kompilovat, protože stále potřebujete přidat následující `using` příkaz:</span><span class="sxs-lookup"><span data-stu-id="8f62d-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8f62d-122">Přidejte *appsettings.js* do souboru do kořenového adresáře projektu pomocí vhodné šablony položky:</span><span class="sxs-lookup"><span data-stu-id="8f62d-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Přidat JSON pro AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="8f62d-124">Migrovat nastavení konfigurace z web.config</span><span class="sxs-lookup"><span data-stu-id="8f62d-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="8f62d-125">Náš projekt ASP.NET MVC zahrnoval požadovaný připojovací řetězec databáze v *web.config* `<connectionStrings>` elementu.</span><span class="sxs-lookup"><span data-stu-id="8f62d-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="8f62d-126">V našem ASP.NET Corem projektu budeme tyto informace ukládat do *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="8f62d-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="8f62d-127">Otevřete *appsettings.jsna*a Všimněte si, že již obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="8f62d-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="8f62d-128">Na zvýrazněný řádek zobrazený výše změňte název databáze z **_CHANGE_ME** na název vaší databáze.</span><span class="sxs-lookup"><span data-stu-id="8f62d-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="8f62d-129">Shrnutí</span><span class="sxs-lookup"><span data-stu-id="8f62d-129">Summary</span></span>

<span data-ttu-id="8f62d-130">ASP.NET Core umístí veškerou spouštěcí logiku aplikace do jednoho souboru, ve kterém je možné definovat a konfigurovat potřebné služby a závislosti.</span><span class="sxs-lookup"><span data-stu-id="8f62d-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="8f62d-131">Nahrazuje soubor *web.config* pomocí flexibilní konfigurační funkce, která může využít celou řadu formátů souborů, jako je JSON a proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="8f62d-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
