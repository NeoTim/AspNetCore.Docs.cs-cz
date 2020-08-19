---
title: Správa verzí služeb gRPC
author: jamesnk
description: Naučte se, jak verze služeb gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 079cca8a7d47897827c314b82136e9eb10b7a516
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633952"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="c7c50-103">Správa verzí služeb gRPC</span><span class="sxs-lookup"><span data-stu-id="c7c50-103">Versioning gRPC services</span></span>

<span data-ttu-id="c7c50-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c7c50-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c7c50-105">Nové funkce přidané do aplikace můžou vyžadovat, aby se služby gRPC mohly změnit, někdy v neočekávaných a rozlomených způsobech.</span><span class="sxs-lookup"><span data-stu-id="c7c50-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="c7c50-106">Když se změní služba gRPC Services:</span><span class="sxs-lookup"><span data-stu-id="c7c50-106">When gRPC services change:</span></span>

* <span data-ttu-id="c7c50-107">Je třeba věnovat pozornost vlivu změn na klienty.</span><span class="sxs-lookup"><span data-stu-id="c7c50-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="c7c50-108">Měla by se implementovat strategie správy verzí, která podporuje změny.</span><span class="sxs-lookup"><span data-stu-id="c7c50-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="c7c50-109">Zpětná kompatibilita</span><span class="sxs-lookup"><span data-stu-id="c7c50-109">Backwards compatibility</span></span>

<span data-ttu-id="c7c50-110">Protokol gRPC je navržený tak, aby podporoval služby, které se v průběhu času mění.</span><span class="sxs-lookup"><span data-stu-id="c7c50-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="c7c50-111">Obecně platí, že přidání do služeb a metod gRPC je nenáročné.</span><span class="sxs-lookup"><span data-stu-id="c7c50-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="c7c50-112">Neprůlomové změny umožňují stávajícím klientům pokračovat v práci bez změn.</span><span class="sxs-lookup"><span data-stu-id="c7c50-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="c7c50-113">Změna nebo odstranění služeb gRPC Services se mění.</span><span class="sxs-lookup"><span data-stu-id="c7c50-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="c7c50-114">Pokud gRPC služby přerušují změny, musí se klienti, kteří tuto službu používají, aktualizovat a znovu nasadit.</span><span class="sxs-lookup"><span data-stu-id="c7c50-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="c7c50-115">Provádění nerozhodujících změn služby má několik výhod:</span><span class="sxs-lookup"><span data-stu-id="c7c50-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="c7c50-116">Stávající klienti pokračují v běhu.</span><span class="sxs-lookup"><span data-stu-id="c7c50-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="c7c50-117">Zabrání práci, která je součástí upozorňování klientů na zásadní změny, a jejich aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="c7c50-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="c7c50-118">Musí být dokumentována a udržována pouze jedna verze služby.</span><span class="sxs-lookup"><span data-stu-id="c7c50-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="c7c50-119">Neprůlomové změny</span><span class="sxs-lookup"><span data-stu-id="c7c50-119">Non-breaking changes</span></span>

<span data-ttu-id="c7c50-120">Tyto změny jsou nerozdělitelné na úrovni protokolu gRPC a binární úrovni .NET.</span><span class="sxs-lookup"><span data-stu-id="c7c50-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="c7c50-121">**Přidává se nová služba.**</span><span class="sxs-lookup"><span data-stu-id="c7c50-121">**Adding a new service**</span></span>
* <span data-ttu-id="c7c50-122">**Přidání nové metody do služby**</span><span class="sxs-lookup"><span data-stu-id="c7c50-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="c7c50-123">**Přidání pole do zprávy žádosti** – pole přidaná do zprávy požadavku jsou deserializována s [výchozí hodnotou](https://developers.google.com/protocol-buffers/docs/proto3#default) na serveru, pokud není nastavena.</span><span class="sxs-lookup"><span data-stu-id="c7c50-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="c7c50-124">Aby byla nekoncová změna, služba musí být úspěšná, když je nové pole nenastavené staršími klienty.</span><span class="sxs-lookup"><span data-stu-id="c7c50-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="c7c50-125">**Přidání pole do zprávy odpovědi** – pole přidaná do zprávy odpovědi jsou deserializována do kolekce [neznámá pole](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) zprávy na klientovi.</span><span class="sxs-lookup"><span data-stu-id="c7c50-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="c7c50-126">**Přidání hodnoty do** výčtového výčtového typu je serializováno jako číselná hodnota.</span><span class="sxs-lookup"><span data-stu-id="c7c50-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="c7c50-127">Nové hodnoty výčtu jsou v klientovi deserializovatelné na hodnotu výčtu bez názvu výčtu.</span><span class="sxs-lookup"><span data-stu-id="c7c50-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="c7c50-128">Aby byla nekoncová změna, starší klienti musí při příjmu nové hodnoty výčtu běžet správně.</span><span class="sxs-lookup"><span data-stu-id="c7c50-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="c7c50-129">Binární přerušující změny</span><span class="sxs-lookup"><span data-stu-id="c7c50-129">Binary breaking changes</span></span>

<span data-ttu-id="c7c50-130">Následující změny nejsou rozstupné na úrovni protokolu gRPC, ale pokud se upgradují na nejnovější verzi, je potřeba, aby byl klient aktualizovaný *. proto* se jedná o kontrakt nebo klientské sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="c7c50-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="c7c50-131">Binární kompatibilita je důležitá, pokud plánujete publikovat knihovnu gRPC do NuGet.</span><span class="sxs-lookup"><span data-stu-id="c7c50-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="c7c50-132">**Odebrání hodnot pole** z odebraného pole je deserializovatelné na [neznámá pole](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)zprávy.</span><span class="sxs-lookup"><span data-stu-id="c7c50-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="c7c50-133">Nejedná se o změnu gRPC protokolu, ale pokud se upgraduje na nejnovější verzi, je potřeba aktualizovat klienta.</span><span class="sxs-lookup"><span data-stu-id="c7c50-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="c7c50-134">Je důležité, aby odebrané číslo pole v budoucnu nechtěně znovu nepoužívalo.</span><span class="sxs-lookup"><span data-stu-id="c7c50-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="c7c50-135">Chcete-li zajistit, aby k tomu nedocházelo, zadejte ve zprávě pomocí [rezervovaného](https://developers.google.com/protocol-buffers/docs/proto3#reserved) klíčového slova Protobuf Odstraněná čísla a názvy polí.</span><span class="sxs-lookup"><span data-stu-id="c7c50-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="c7c50-136">**Přejmenování** zpráv – názvy zpráv se obvykle neodesílají v síti, takže to není gRPCá Změna protokolu.</span><span class="sxs-lookup"><span data-stu-id="c7c50-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="c7c50-137">Klient bude muset být aktualizován, pokud se upgraduje na nejnovější kontrakt.</span><span class="sxs-lookup"><span data-stu-id="c7c50-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="c7c50-138">Jedna situace, kdy se **názvy zpráv** odesílají v síti, jsou v [jakémkoli](https://developers.google.com/protocol-buffers/docs/proto3#any) poli, když se k identifikaci typu zprávy použije název zprávy.</span><span class="sxs-lookup"><span data-stu-id="c7c50-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="c7c50-139">**Změna csharp_namespace** změny změní `csharp_namespace` obor názvů vygenerovaných typů .NET.</span><span class="sxs-lookup"><span data-stu-id="c7c50-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="c7c50-140">Nejedná se o změnu gRPC protokolu, ale pokud se upgraduje na nejnovější verzi, je potřeba aktualizovat klienta.</span><span class="sxs-lookup"><span data-stu-id="c7c50-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="c7c50-141">Přerušující se změny protokolu</span><span class="sxs-lookup"><span data-stu-id="c7c50-141">Protocol breaking changes</span></span>

<span data-ttu-id="c7c50-142">Následující položky jsou protokoly a binární změny:</span><span class="sxs-lookup"><span data-stu-id="c7c50-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="c7c50-143">**Přejmenování pole** – pomocí Protobuf obsahu se názvy polí používají pouze ve vygenerovaném kódu.</span><span class="sxs-lookup"><span data-stu-id="c7c50-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="c7c50-144">Číslo pole se používá k identifikaci polí v síti.</span><span class="sxs-lookup"><span data-stu-id="c7c50-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="c7c50-145">Přejmenování pole není Protobuf Změna protokolu pro.</span><span class="sxs-lookup"><span data-stu-id="c7c50-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="c7c50-146">Pokud ale server používá obsah JSON, přejmenovávání pole je zásadní změna.</span><span class="sxs-lookup"><span data-stu-id="c7c50-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="c7c50-147">**Změna datového typu pole** – Změna datového typu pole na [nekompatibilní typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) způsobí chyby při deserializaci zprávy.</span><span class="sxs-lookup"><span data-stu-id="c7c50-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="c7c50-148">I v případě, že je nový datový typ kompatibilní, je pravděpodobně nutné aktualizovat klienta, aby podporoval nový typ, pokud se upgraduje na nejnovější kontrakt.</span><span class="sxs-lookup"><span data-stu-id="c7c50-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="c7c50-149">**Změna čísla pole** – pomocí datových částí Protobuf se číslo pole používá k identifikaci polí v síti.</span><span class="sxs-lookup"><span data-stu-id="c7c50-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="c7c50-150">**Přejmenování balíčku, služby nebo metody** – gRPC používá název balíčku, název služby a název metody k sestavení adresy URL.</span><span class="sxs-lookup"><span data-stu-id="c7c50-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="c7c50-151">Klient získá *Neimplementovaný* stav ze serveru.</span><span class="sxs-lookup"><span data-stu-id="c7c50-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="c7c50-152">**Odebrání služby nebo metody** – klient získá *Neimplementovaný* stav ze serveru při volání odebrané metody.</span><span class="sxs-lookup"><span data-stu-id="c7c50-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="c7c50-153">Chování při ukončování změn</span><span class="sxs-lookup"><span data-stu-id="c7c50-153">Behavior breaking changes</span></span>

<span data-ttu-id="c7c50-154">Při provádění nerozhodujících změn musíte taky zvážit, jestli můžou starší klienti pokračovat v práci s novým chováním služby.</span><span class="sxs-lookup"><span data-stu-id="c7c50-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="c7c50-155">Například přidání nového pole do zprávy žádosti:</span><span class="sxs-lookup"><span data-stu-id="c7c50-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="c7c50-156">Nejedná se o změnu v důsledku přerušení protokolu.</span><span class="sxs-lookup"><span data-stu-id="c7c50-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="c7c50-157">Vrátí stav chyby na serveru, pokud není nové pole nastavené, což pro staré klienty způsobuje zásadní změnu.</span><span class="sxs-lookup"><span data-stu-id="c7c50-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="c7c50-158">Kompatibilita chování je určena kódem specifickým pro danou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c7c50-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="c7c50-159">Číslo verze služeb</span><span class="sxs-lookup"><span data-stu-id="c7c50-159">Version number services</span></span>

<span data-ttu-id="c7c50-160">Služby by se měly snažit, aby zůstaly zpětně kompatibilní se starými klienty.</span><span class="sxs-lookup"><span data-stu-id="c7c50-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="c7c50-161">Nakonec změny vaší aplikace mohou vyžadovat zásadní změny.</span><span class="sxs-lookup"><span data-stu-id="c7c50-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="c7c50-162">Poškození starých klientů a jejich vynucování spolu s vaší službou není dobrým uživatelským prostředím.</span><span class="sxs-lookup"><span data-stu-id="c7c50-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="c7c50-163">Způsob, jak udržet zpětnou kompatibilitu při provádění zásadních změn, je publikování více verzí služby.</span><span class="sxs-lookup"><span data-stu-id="c7c50-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="c7c50-164">gRPC podporuje volitelný specifikátor [balíčku](https://developers.google.com/protocol-buffers/docs/proto3#packages) , který funguje podobně jako obor názvů .NET.</span><span class="sxs-lookup"><span data-stu-id="c7c50-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="c7c50-165">Ve skutečnosti `package` bude použit jako obor názvů .NET pro vygenerované typy .NET, pokud `option csharp_namespace` není nastaven v souboru *..* .</span><span class="sxs-lookup"><span data-stu-id="c7c50-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="c7c50-166">Balíček lze použít k zadání čísla verze vaší služby a jejích zpráv:</span><span class="sxs-lookup"><span data-stu-id="c7c50-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="c7c50-167">Název balíčku je v kombinaci s názvem služby k identifikaci adresy služby.</span><span class="sxs-lookup"><span data-stu-id="c7c50-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="c7c50-168">Adresa služby umožňuje souběžně hostovat více verzí služby:</span><span class="sxs-lookup"><span data-stu-id="c7c50-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="c7c50-169">Implementace služby se správou verzí jsou zaregistrované v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c7c50-169">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="c7c50-170">Zahrnutím čísla verze do názvu balíčku získáte možnost publikovat verzi *v2* vaší služby s nezměněnými změnami, a to i nadále podporovat starší klienty, kteří volají verzi *v1* .</span><span class="sxs-lookup"><span data-stu-id="c7c50-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="c7c50-171">Jakmile se klienti aktualizují, aby používali službu *v2* , můžete si odebrat starou verzi.</span><span class="sxs-lookup"><span data-stu-id="c7c50-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="c7c50-172">Při plánování publikování více verzí služby:</span><span class="sxs-lookup"><span data-stu-id="c7c50-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="c7c50-173">Nepoužívejte závažné změny, pokud je to rozumné.</span><span class="sxs-lookup"><span data-stu-id="c7c50-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="c7c50-174">Neaktualizujte číslo verze, pokud neprovádíte zásadní změny.</span><span class="sxs-lookup"><span data-stu-id="c7c50-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="c7c50-175">Aktualizujte číslo verze, když provedete průlomové změny.</span><span class="sxs-lookup"><span data-stu-id="c7c50-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="c7c50-176">Publikování více verzí služby je duplicitní.</span><span class="sxs-lookup"><span data-stu-id="c7c50-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="c7c50-177">Pokud chcete omezit duplicity, zvažte přesunutí obchodní logiky ze implementací služby do centralizovaného umístění, které je možné použít starými a novými implementacemi:</span><span class="sxs-lookup"><span data-stu-id="c7c50-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="c7c50-178">Služby a zprávy vygenerované s různými názvy balíčků jsou **různé typy rozhraní .NET**.</span><span class="sxs-lookup"><span data-stu-id="c7c50-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="c7c50-179">Přesun obchodní logiky do centralizovaného umístění vyžaduje mapování zpráv na společné typy.</span><span class="sxs-lookup"><span data-stu-id="c7c50-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>
