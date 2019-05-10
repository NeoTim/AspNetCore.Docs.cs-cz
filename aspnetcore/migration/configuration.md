---
title: Migrace konfigurace do ASP.NET Core
author: ardalis
description: Zjistěte, jak migrovat konfigurace z projektu aplikace ASP.NET MVC do projektu aplikace ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: c379f1f64dc5ab8aeb48055124e86e4e60d93785
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900993"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="fedb3-103">Migrace konfigurace do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fedb3-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="fedb3-104">Podle [Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="fedb3-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="fedb3-105">V předchozím článku jsme začali [migrace projektu aplikace ASP.NET MVC do ASP.NET Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="fedb3-105">In the previous article, we began [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="fedb3-106">V tomto článku budeme migrovat konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="fedb3-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="fedb3-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fedb3-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="fedb3-108">Nastavení konfigurace</span><span class="sxs-lookup"><span data-stu-id="fedb3-108">Setup configuration</span></span>

<span data-ttu-id="fedb3-109">ASP.NET Core se už používá *Global.asax* a *web.config* soubory, které používá předchozí verzí technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="fedb3-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="fedb3-110">V předchozích verzích technologie ASP.NET, aplikace logiky po spuštění byl umístěn v `Application_StartUp` metody v rámci *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="fedb3-110">In earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="fedb3-111">Později v ASP.NET MVC *Startup.cs* byl soubor zahrnut v kořenovém adresáři projektu; a byla volána při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="fedb3-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="fedb3-112">ASP.NET Core přijala tento přístup úplně tak, že celé spouštěcí logiky *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="fedb3-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="fedb3-113">*Web.config* souboru nahradila také v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fedb3-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="fedb3-114">Konfigurace samotného lze nyní nastavit, je popsáno v postupu při spuštění aplikace v rámci *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="fedb3-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="fedb3-115">Konfigurace můžete stále využít soubory XML, ale obvykle projekty ASP.NET Core, umístí hodnoty konfigurace v souboru ve formátu JSON, jako například *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fedb3-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="fedb3-116">Systém konfigurace ASP.NET Core můžete také snadno přistupovat k proměnné prostředí, které můžete zadat [zabezpečené a robustní umístění](xref:security/app-secrets) pro hodnoty v závislosti na prostředí.</span><span class="sxs-lookup"><span data-stu-id="fedb3-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="fedb3-117">To platí zejména pro tajné kódy jako jsou připojovací řetězce a klíče rozhraní API, které by neměly být zařazeno do správy zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="fedb3-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="fedb3-118">Zobrazit [konfigurace](xref:fundamentals/configuration/index) získat další informace o konfiguraci v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fedb3-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="fedb3-119">Pro účely tohoto článku jsme začínáte s částečně migrovaného projektu ASP.NET Core z [předchozím článku](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="fedb3-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="fedb3-120">Chcete-li nastavit konfiguraci, přidejte následující konstruktor a nastavte *Startup.cs* souboru umístěného v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="fedb3-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="fedb3-121">Všimněte si, který v tomto okamžiku *Startup.cs* soubor nebude kompilovat, protože stále je potřeba přidat následující `using` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="fedb3-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="fedb3-122">Přidat *appsettings.json* souboru do kořenového adresáře projektu pomocí šablony položky odpovídající:</span><span class="sxs-lookup"><span data-stu-id="fedb3-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Přidat AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="fedb3-124">Migrace nastavení konfigurace ze souboru web.config</span><span class="sxs-lookup"><span data-stu-id="fedb3-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="fedb3-125">Zahrnuté připojovací řetězec databáze v našem projektu ASP.NET MVC *web.config*v `<connectionStrings>` elementu.</span><span class="sxs-lookup"><span data-stu-id="fedb3-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="fedb3-126">V našem projektu ASP.NET Core, budeme k ukládání příslušných informací v *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="fedb3-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="fedb3-127">Otevřít *appsettings.json*a Všimněte si, že už obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="fedb3-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="fedb3-128">V zvýrazněný řádek uvedené výše, změňte název databáze z **_CHANGE_ME** na název vaší databáze.</span><span class="sxs-lookup"><span data-stu-id="fedb3-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="fedb3-129">Souhrn</span><span class="sxs-lookup"><span data-stu-id="fedb3-129">Summary</span></span>

<span data-ttu-id="fedb3-130">ASP.NET Core umístí všechny spouštěcí logiky aplikace v jediném souboru, ve kterém může být nezbytných služeb a závislosti definované a nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="fedb3-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="fedb3-131">Nahradí *web.config* soubor s flexibilní konfiguraci funkce, která můžete využít širokou škálu formátů souborů, jako je JSON, stejně jako proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="fedb3-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
