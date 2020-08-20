---
title: Řetězce účelu v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se v rozhraních API ochrany ASP.NET Corech dat používají řetězce účelu.
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 1119c45570338f629a3ab7adbd43361529aa23e7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626919"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="f07a0-103">Řetězce účelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f07a0-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="f07a0-104">Komponenty, které spotřebovávají, `IDataProtectionProvider` musí do metody předat jedinečný parametr pro *účely* `CreateProtector` .</span><span class="sxs-lookup"><span data-stu-id="f07a0-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="f07a0-105">*Parametr* pro účely je podstatou zabezpečení systému ochrany dat, protože poskytuje izolaci mezi spotřebiteli šifrování, a to i v případě, že jsou kořenové kryptografické klíče stejné.</span><span class="sxs-lookup"><span data-stu-id="f07a0-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="f07a0-106">Když příjemce zadá účel, použije se spolu s kořenovými šifrovacími klíči k odvození kryptografických podklíčů, které jsou pro tohoto příjemce jedinečné.</span><span class="sxs-lookup"><span data-stu-id="f07a0-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="f07a0-107">Tím se uživatel izoluje od všech ostatních zprostředkovatelů kryptografie v aplikaci: žádná jiná komponenta nemůže číst své datové části a nemůže číst žádné další datové části součásti.</span><span class="sxs-lookup"><span data-stu-id="f07a0-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="f07a0-108">Tato izolace také vykresluje neproveditelné celé kategorie útoků proti komponentě.</span><span class="sxs-lookup"><span data-stu-id="f07a0-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Příklad diagramu účelu](purpose-strings/_static/purposes.png)

<span data-ttu-id="f07a0-110">V diagramu výše `IDataProtector` instance a a B **nemohou** číst jiné datové části, a to pouze vlastní.</span><span class="sxs-lookup"><span data-stu-id="f07a0-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="f07a0-111">Řetězec účelu nemusí být tajný.</span><span class="sxs-lookup"><span data-stu-id="f07a0-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="f07a0-112">Měl by být jednoduše jedinečný v tom smyslu, že žádná jiná dobře fungující součást nikdy neposkytne stejný řetězec účelu.</span><span class="sxs-lookup"><span data-stu-id="f07a0-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="f07a0-113">Použití oboru názvů a názvu typu součásti, která využívá rozhraní API pro ochranu dat, je dobré pravidlo, protože v praxi nebudou tyto informace nikdy kolidovat.</span><span class="sxs-lookup"><span data-stu-id="f07a0-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="f07a0-114">Komponenta vytvořená společností Contoso, která je zodpovědná za tokeny nosiče minting, může jako svůj účel použít contoso. Security. BearerToken.</span><span class="sxs-lookup"><span data-stu-id="f07a0-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="f07a0-115">Nebo dokonce lepší – může jako řetězec účelu použít contoso. Security. BearerToken. v1.</span><span class="sxs-lookup"><span data-stu-id="f07a0-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="f07a0-116">Připojení čísla verze umožňuje budoucí verzi použít contoso. Security. BearerToken. v2 jako svůj účel a různé verze by se od sebe od sebe od sebe od té chvíle zcela odlišily jako datové části.</span><span class="sxs-lookup"><span data-stu-id="f07a0-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="f07a0-117">Vzhledem k tomu, že parametr pro účely `CreateProtector` je pole řetězců, výše uvedený je možné místo toho zadat jako `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="f07a0-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="f07a0-118">To umožňuje vytvořit hierarchii účelů a otevřít možnost scénářů víceklientské architektury se systémem ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="f07a0-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="f07a0-119">Součásti by neumožňovaly, aby nedůvěryhodný vstup uživatele byl jediným zdrojem vstupu pro řetěz účelu.</span><span class="sxs-lookup"><span data-stu-id="f07a0-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="f07a0-120">Představte si třeba komponentu contoso. Messaging. SecureMessage, která zodpovídá za ukládání zabezpečených zpráv.</span><span class="sxs-lookup"><span data-stu-id="f07a0-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="f07a0-121">Pokud by komponenta zabezpečeného zasílání zpráv volala `CreateProtector([ username ])` , pak uživatel se zlými úmysly může vytvořit účet s uživatelským jménem "contoso. Security. BearerToken" při pokusu o získání komponenty `CreateProtector([ "Contoso.Security.BearerToken" ])` , což neúmyslně způsobovat zabezpečený systém zasílání zpráv, aby mentolová datové části, které by mohly být vnímané jako ověřovací tokeny.</span><span class="sxs-lookup"><span data-stu-id="f07a0-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="f07a0-122">Vhodným řetězcem pro komponentu zasílání zpráv je `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , což zajišťuje správnou izolaci.</span><span class="sxs-lookup"><span data-stu-id="f07a0-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="f07a0-123">K izolaci poskytuje a chování a `IDataProtectionProvider` `IDataProtector` slouží následující:</span><span class="sxs-lookup"><span data-stu-id="f07a0-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="f07a0-124">Pro daný `IDataProtectionProvider` objekt `CreateProtector` Metoda vytvoří `IDataProtector` objekt jednoznačně vázaný na `IDataProtectionProvider` objekt, který jej vytvořil, a parametr pro účely, který byl předán metodě.</span><span class="sxs-lookup"><span data-stu-id="f07a0-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="f07a0-125">Parametr účelu nesmí mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f07a0-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="f07a0-126">(Pokud je účel zadán jako pole, znamená to, že pole nesmí mít nulovou délku a všechny elementy pole nesmí mít hodnotu null.) Účel prázdného řetězce je technicky povolený, ale nedoporučuje se.</span><span class="sxs-lookup"><span data-stu-id="f07a0-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="f07a0-127">Dva argumenty pro účely jsou ekvivalentní pouze v případě, že obsahují stejné řetězce (pomocí ordinálního porovnávání) ve stejném pořadí.</span><span class="sxs-lookup"><span data-stu-id="f07a0-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="f07a0-128">Jeden argument účelu je ekvivalentní k odpovídajícímu poli účelu s jedním prvkem.</span><span class="sxs-lookup"><span data-stu-id="f07a0-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="f07a0-129">Dva `IDataProtector` objekty jsou ekvivalentní pouze v případě, že jsou vytvořeny z ekvivalentních `IDataProtectionProvider` objektů se stejnými parametry pro účely.</span><span class="sxs-lookup"><span data-stu-id="f07a0-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="f07a0-130">Pro daný `IDataProtector` objekt volání `Unprotect(protectedData)` vrátí původní, `unprotectedData` Pokud a pouze `protectedData := Protect(unprotectedData)` v případě ekvivalentního `IDataProtector` objektu.</span><span class="sxs-lookup"><span data-stu-id="f07a0-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="f07a0-131">Nezvažujeme případ, kdy některá součást úmyslně zvolí řetězec účelu, u kterého se ví, že je v konfliktu s jinou komponentou.</span><span class="sxs-lookup"><span data-stu-id="f07a0-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="f07a0-132">Taková součást by v podstatě byla považována za škodlivou a tento systém nebude poskytovat záruky zabezpečení v případě, že je v rámci pracovního procesu již spuštěn škodlivý kód.</span><span class="sxs-lookup"><span data-stu-id="f07a0-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
