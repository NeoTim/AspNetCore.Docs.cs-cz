---
title: Spolehlivé služby gRPC s konečnými termíny a zrušením
author: jamesnk
description: Naučte se vytvářet spolehlivé služby gRPC pomocí konečných termínů a zrušení v .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: 59b737a032ea37a554ad5ddd0f4d44e4e1602d88
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594425"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="9775f-103">Spolehlivé služby gRPC s konečnými termíny a zrušením</span><span class="sxs-lookup"><span data-stu-id="9775f-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="9775f-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="9775f-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="9775f-105">Termíny a zrušení jsou funkce používané klienty gRPC k přerušení probíhajících volání.</span><span class="sxs-lookup"><span data-stu-id="9775f-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="9775f-106">Tento článek popisuje, proč jsou termíny a zrušení důležité a jak je používat v aplikacích .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="9775f-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="9775f-107">Dodržování</span><span class="sxs-lookup"><span data-stu-id="9775f-107">Deadlines</span></span>

<span data-ttu-id="9775f-108">Konečný termín umožňuje klientovi gRPC určit, jak dlouho bude čekat na dokončení volání.</span><span class="sxs-lookup"><span data-stu-id="9775f-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="9775f-109">Pokud je překročen konečný termín, volání je zrušeno.</span><span class="sxs-lookup"><span data-stu-id="9775f-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="9775f-110">Nastavení konečného termínu je důležité, protože poskytuje horní limit toho, jak dlouho může volání běžet.</span><span class="sxs-lookup"><span data-stu-id="9775f-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="9775f-111">Zastaví se tak nesprávnému chování služeb, které neběží trvale a vyčerpání prostředků serveru.</span><span class="sxs-lookup"><span data-stu-id="9775f-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="9775f-112">Termíny jsou užitečným nástrojem pro vytváření spolehlivých aplikací a měly by být nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="9775f-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="9775f-113">Konfigurace konečného termínu:</span><span class="sxs-lookup"><span data-stu-id="9775f-113">Deadline configuration:</span></span>

* <span data-ttu-id="9775f-114">Konečný termín je nakonfigurován pomocí `CallOptions.Deadline` , když je provedeno volání.</span><span class="sxs-lookup"><span data-stu-id="9775f-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="9775f-115">Neexistuje žádná výchozí hodnota termínu.</span><span class="sxs-lookup"><span data-stu-id="9775f-115">There is no default deadline value.</span></span> <span data-ttu-id="9775f-116">volání gRPC nejsou časově omezená, pokud není zadaný konečný termín.</span><span class="sxs-lookup"><span data-stu-id="9775f-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="9775f-117">Konečný termín je čas UTC, kdy je překročen konečný termín.</span><span class="sxs-lookup"><span data-stu-id="9775f-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="9775f-118">Například `DateTime.UtcNow.AddSeconds(5)` je konečný termín 5 sekund od této chvíle.</span><span class="sxs-lookup"><span data-stu-id="9775f-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="9775f-119">Pokud se použije minulý nebo aktuální čas, volání okamžitě přesáhne konečný termín.</span><span class="sxs-lookup"><span data-stu-id="9775f-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="9775f-120">Konečný termín je odeslán s voláním gRPC do služby a je nezávisle sledován klientem i službou.</span><span class="sxs-lookup"><span data-stu-id="9775f-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="9775f-121">Je možné, že volání gRPC se dokončí na jednom počítači, ale až do doby, kdy byla odpověď vrácena klientovi, byl překročen konečný termín.</span><span class="sxs-lookup"><span data-stu-id="9775f-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="9775f-122">Pokud je překročen konečný termín, má klient a služba jiné chování:</span><span class="sxs-lookup"><span data-stu-id="9775f-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="9775f-123">Klient okamžitě přeruší příslušný požadavek HTTP a vyvolá `DeadlineExceeded` chybu.</span><span class="sxs-lookup"><span data-stu-id="9775f-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="9775f-124">Klientská aplikace se může rozhodnout zachytit chybu a zobrazit uživateli zprávu s časovým limitem.</span><span class="sxs-lookup"><span data-stu-id="9775f-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="9775f-125">Na serveru je spuštěn požadavek HTTP přerušen a je vyvolána [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) .</span><span class="sxs-lookup"><span data-stu-id="9775f-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="9775f-126">I když je požadavek HTTP přerušený, volání gRPC i nadále běží na serveru, dokud se metoda nedokončí.</span><span class="sxs-lookup"><span data-stu-id="9775f-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="9775f-127">Je důležité, aby byl token zrušení předán asynchronním metodám, aby byly zrušeny spolu s voláním.</span><span class="sxs-lookup"><span data-stu-id="9775f-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="9775f-128">Například předání tokenu zrušení do asynchronních databázových dotazů a požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="9775f-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="9775f-129">Předání tokenu zrušení umožňuje, aby zrušené volání bylo rychle dokončeno na serveru a uvolnilo prostředky pro jiné hovory.</span><span class="sxs-lookup"><span data-stu-id="9775f-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="9775f-130">Nakonfigurujte `CallOptions.Deadline` , aby se nastavil konečný termín pro gRPC volání:</span><span class="sxs-lookup"><span data-stu-id="9775f-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="9775f-131">Použití `ServerCallContext.CancellationToken` ve službě gRPC:</span><span class="sxs-lookup"><span data-stu-id="9775f-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="9775f-132">Rozšiřování konečných termínů</span><span class="sxs-lookup"><span data-stu-id="9775f-132">Propagating deadlines</span></span>

<span data-ttu-id="9775f-133">Při volání gRPC z vykonávající služby gRPC by se měl konečný termín rozšířit.</span><span class="sxs-lookup"><span data-stu-id="9775f-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="9775f-134">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9775f-134">For example:</span></span>

1. <span data-ttu-id="9775f-135">Klientská aplikace `FrontendService.GetUser` je volána s konečným termínem.</span><span class="sxs-lookup"><span data-stu-id="9775f-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="9775f-136">`FrontendService` volání `UserService.GetUser` .</span><span class="sxs-lookup"><span data-stu-id="9775f-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="9775f-137">Konečný termín určený klientem by měl být zadaný pomocí nového volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="9775f-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="9775f-138">`UserService.GetUser` přijme konečný termín.</span><span class="sxs-lookup"><span data-stu-id="9775f-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="9775f-139">Je-li překročen konečný termín klientské aplikace, správně vyprší časový limit.</span><span class="sxs-lookup"><span data-stu-id="9775f-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="9775f-140">Kontext volání poskytuje konečný termín s `ServerCallContext.Deadline` :</span><span class="sxs-lookup"><span data-stu-id="9775f-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="9775f-141">Ruční rozšiřování konečných termínů může mít nenáročný.</span><span class="sxs-lookup"><span data-stu-id="9775f-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="9775f-142">Konečný termín musí být předán každé volání a je snadno neúmyslně nechtěně neúspěšný.</span><span class="sxs-lookup"><span data-stu-id="9775f-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="9775f-143">Pro objekt pro vytváření klientů gRPC je k dispozici automatické řešení.</span><span class="sxs-lookup"><span data-stu-id="9775f-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="9775f-144">Zadání `EnableCallContextPropagation` :</span><span class="sxs-lookup"><span data-stu-id="9775f-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="9775f-145">Automaticky rozšíří konečný termín a token zrušení na podřízená volání.</span><span class="sxs-lookup"><span data-stu-id="9775f-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="9775f-146">Je vynikající způsob, jak zajistit, aby komplexní a vnořené gRPC scénáře vždy rozšířily konečný termín a zrušení.</span><span class="sxs-lookup"><span data-stu-id="9775f-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="9775f-147">Další informace naleznete v tématu <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="9775f-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="9775f-148">Zrušení</span><span class="sxs-lookup"><span data-stu-id="9775f-148">Cancellation</span></span>

<span data-ttu-id="9775f-149">Zrušení umožňuje klientovi gRPC zrušit dlouho běžící volání, která už nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="9775f-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="9775f-150">Například volání gRPC, které streamuje aktualizace v reálném čase, se spustí, když uživatel navštíví stránku na webu.</span><span class="sxs-lookup"><span data-stu-id="9775f-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="9775f-151">Datový proud by měl být zrušen, pokud uživatel přejde ze stránky jinam.</span><span class="sxs-lookup"><span data-stu-id="9775f-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="9775f-152">Volání gRPC lze v klientovi zrušit předáním tokenu zrušení pomocí [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) nebo voláním `Dispose` volání.</span><span class="sxs-lookup"><span data-stu-id="9775f-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="9775f-153">gRPC služby, které je možné zrušit, by měly:</span><span class="sxs-lookup"><span data-stu-id="9775f-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="9775f-154">Předejte `ServerCallContext.CancellationToken` do asynchronních metod.</span><span class="sxs-lookup"><span data-stu-id="9775f-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="9775f-155">Zrušení asynchronních metod umožňuje rychlé dokončení volání na serveru.</span><span class="sxs-lookup"><span data-stu-id="9775f-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="9775f-156">Rozšiřte token zrušení na podřízená volání.</span><span class="sxs-lookup"><span data-stu-id="9775f-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="9775f-157">Rozšiřování tokenu zrušení zajišťuje, aby se podřízená volání zrušila s jejich nadřazeným prvkem.</span><span class="sxs-lookup"><span data-stu-id="9775f-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="9775f-158">[gRPC klientskou továrnu](xref:grpc/clientfactory) a `EnableCallContextPropagation()` automaticky šíří token zrušení.</span><span class="sxs-lookup"><span data-stu-id="9775f-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9775f-159">Další materiály</span><span class="sxs-lookup"><span data-stu-id="9775f-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
