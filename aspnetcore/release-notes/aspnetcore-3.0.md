---
title: Co je nového v ASP.NET Core 3,0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 10741cfc701bf261484b9e6c73a2347e2dd7083d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019831"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="8a465-103">Co je nového v ASP.NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="8a465-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="8a465-104">Tento článek popisuje nejvýznamnější změny v ASP.NET Core 3,0 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="8a465-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

<span data-ttu-id="8a465-105">Blazorje nová architektura v ASP.NET Core pro vytváření interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET:</span><span class="sxs-lookup"><span data-stu-id="8a465-105">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="8a465-106">Vytvářejte bohatá interaktivní uživatelská rozhraní pomocí jazyka C# namísto JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8a465-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="8a465-107">Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.</span><span class="sxs-lookup"><span data-stu-id="8a465-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="8a465-108">Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="8a465-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="8a465-109">Blazorscénáře podporované rozhraním:</span><span class="sxs-lookup"><span data-stu-id="8a465-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="8a465-110">Opakovaně použitelné součásti uživatelského rozhraní ( Razor součásti)</span><span class="sxs-lookup"><span data-stu-id="8a465-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="8a465-111">Směrování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="8a465-111">Client-side routing</span></span>
* <span data-ttu-id="8a465-112">Rozložení komponent</span><span class="sxs-lookup"><span data-stu-id="8a465-112">Component layouts</span></span>
* <span data-ttu-id="8a465-113">Podpora pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="8a465-113">Support for dependency injection</span></span>
* <span data-ttu-id="8a465-114">Formuláře a ověřování</span><span class="sxs-lookup"><span data-stu-id="8a465-114">Forms and validation</span></span>
* <span data-ttu-id="8a465-115">Sestavení knihoven komponent pomocí Razor knihoven tříd</span><span class="sxs-lookup"><span data-stu-id="8a465-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="8a465-116">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="8a465-116">JavaScript interop</span></span>

<span data-ttu-id="8a465-117">Další informace naleznete v tématu <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="8a465-117">For more information, see <xref:blazor/index>.</span></span>

### Blazor Server

<span data-ttu-id="8a465-118">Blazorodpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8a465-118">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="8a465-119">Blazor Serverposkytuje podporu pro hostování Razor komponent na serveru aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a465-119">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="8a465-120">Aktualizace uživatelského rozhraní se zpracovávají přes SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="8a465-120">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="8a465-121">Blazor Serverje podporován v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="8a465-121">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="no-locblazor-webassembly-preview"></a><span data-ttu-id="8a465-122">Blazor WebAssemblyTisk</span><span class="sxs-lookup"><span data-stu-id="8a465-122">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="8a465-123">Blazoraplikace je také možné spustit přímo v prohlížeči pomocí rozhraní .NET runtime založeného na WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8a465-123">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="8a465-124">Blazor WebAssemblyje ve verzi Preview a *nepodporuje* se v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="8a465-124">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a465-125">Blazor WebAssemblybude podporován v budoucí verzi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a465-125">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="no-locrazor-components"></a><span data-ttu-id="8a465-126">Razorkonstrukční</span><span class="sxs-lookup"><span data-stu-id="8a465-126">Razor components</span></span>

<span data-ttu-id="8a465-127">Blazoraplikace jsou sestavené z komponent.</span><span class="sxs-lookup"><span data-stu-id="8a465-127">Blazor apps are built from components.</span></span> <span data-ttu-id="8a465-128">Komponenty jsou samostatné bloky uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="8a465-128">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="8a465-129">Komponenty jsou normální třídy .NET, které definují logiku vykreslování uživatelského rozhraní a obslužné rutiny událostí na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="8a465-129">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="8a465-130">Můžete vytvářet bohatě interaktivní webové aplikace bez JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8a465-130">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="8a465-131">Komponenty v Blazor jsou obvykle vytvořeny pomocí Razor syntaxe, přirozeného Blendu HTML a jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="8a465-131">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="8a465-132">Razorkomponenty jsou podobné Razor zobrazením stránky a MVC v tom, že obě používají Razor .</span><span class="sxs-lookup"><span data-stu-id="8a465-132">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="8a465-133">Na rozdíl od stránek a zobrazení, které jsou založeny na modelu požadavek-odpověď, se komponenty používají konkrétně pro zpracování kompozice uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8a465-133">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="8a465-134">gRPC</span><span class="sxs-lookup"><span data-stu-id="8a465-134">gRPC</span></span>

<span data-ttu-id="8a465-135">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="8a465-135">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="8a465-136">Je oblíbeným a vysoce výkonným rozhraním vzdáleného volání procedur (RPC).</span><span class="sxs-lookup"><span data-stu-id="8a465-136">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="8a465-137">Nabízí dogmatickýmý přístup k vývoji rozhraní API, který je prvním kontraktem.</span><span class="sxs-lookup"><span data-stu-id="8a465-137">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="8a465-138">Používá moderní technologie, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="8a465-138">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="8a465-139">HTTP/2 pro přenos.</span><span class="sxs-lookup"><span data-stu-id="8a465-139">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="8a465-140">Vyrovnávací paměti protokolu jako jazyk popisu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8a465-140">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="8a465-141">Formát binární serializace.</span><span class="sxs-lookup"><span data-stu-id="8a465-141">Binary serialization format.</span></span>
* <span data-ttu-id="8a465-142">Poskytuje funkce, jako například:</span><span class="sxs-lookup"><span data-stu-id="8a465-142">Provides features such as:</span></span>

  * <span data-ttu-id="8a465-143">Ověřování</span><span class="sxs-lookup"><span data-stu-id="8a465-143">Authentication</span></span>
  * <span data-ttu-id="8a465-144">Obousměrné streamování a řízení toku.</span><span class="sxs-lookup"><span data-stu-id="8a465-144">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="8a465-145">Zrušení a vypršení časového limitu.</span><span class="sxs-lookup"><span data-stu-id="8a465-145">Cancellation and timeouts.</span></span>

<span data-ttu-id="8a465-146">funkce gRPC v ASP.NET Core 3,0 obsahuje:</span><span class="sxs-lookup"><span data-stu-id="8a465-146">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="8a465-147">[Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): ASP.NET Core Framework pro hostování služeb Grpc Services.</span><span class="sxs-lookup"><span data-stu-id="8a465-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="8a465-148">gRPC se na ASP.NET Core integruje se standardními ASP.NET Core funkcemi, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="8a465-148">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="8a465-149">[Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client): klient Grpc pro .NET Core, který sestaví na známém `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="8a465-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="8a465-150">[Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): klientská integrace Grpc s `HttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="8a465-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="8a465-151">Další informace naleznete v tématu <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="8a465-151">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="8a465-152">Pokyny k migraci najdete v tématu [aktualizace SignalR kódu](xref:migration/22-to-30#signalr) .</span><span class="sxs-lookup"><span data-stu-id="8a465-152">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="8a465-153">SignalRnyní používá `System.Text.Json` k serializaci nebo deserializaci zpráv JSON.</span><span class="sxs-lookup"><span data-stu-id="8a465-153">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="8a465-154">Pokyny pro obnovení serializátoru založeného na systému najdete v tématu [Přepnutí na Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="8a465-154">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="8a465-155">V klientech JavaScript a .NET pro SignalR se podpora přidala pro automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="8a465-155">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="8a465-156">Ve výchozím nastavení se klient pokusí znovu připojit hned a v případě potřeby opakovat po 2, 10 a 30 sekundách.</span><span class="sxs-lookup"><span data-stu-id="8a465-156">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="8a465-157">Pokud se klient úspěšně znovu připojí, obdrží nové ID připojení.</span><span class="sxs-lookup"><span data-stu-id="8a465-157">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="8a465-158">Automatické opětovné připojení je výslovný souhlas:</span><span class="sxs-lookup"><span data-stu-id="8a465-158">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="8a465-159">Intervaly opětovného připojení lze zadat předáním pole doby trvání založené na milisekundách:</span><span class="sxs-lookup"><span data-stu-id="8a465-159">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="8a465-160">Vlastní implementaci lze předat v rámci úplného řízení intervalů opětovného připojení.</span><span class="sxs-lookup"><span data-stu-id="8a465-160">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="8a465-161">Pokud se opětovné připojení nezdařilo po intervalu posledního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="8a465-161">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="8a465-162">Klient považuje připojení za offline.</span><span class="sxs-lookup"><span data-stu-id="8a465-162">The client considers the connection is offline.</span></span>
* <span data-ttu-id="8a465-163">Klient se ukončí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="8a465-163">The client stops trying to reconnect.</span></span>

<span data-ttu-id="8a465-164">Během opakovaného pokusu o připojení aktualizujte uživatelské rozhraní aplikace a upozorněte uživatele, že probíhá pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="8a465-164">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="8a465-165">Pokud chcete poskytnout zpětnou vazbu uživatelského rozhraní při přerušení připojení, SignalR rozhraní API klienta se rozšířilo tak, aby zahrnovalo následující obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="8a465-165">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="8a465-166">`onreconnecting`: Dává vývojářům možnost zakázat uživatelské rozhraní nebo upozornit uživatele na to, že je aplikace offline.</span><span class="sxs-lookup"><span data-stu-id="8a465-166">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="8a465-167">`onreconnected`: Dává vývojářům možnost aktualizovat uživatelské rozhraní po opětovném navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="8a465-167">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="8a465-168">Následující kód používá `onreconnecting` k aktualizaci uživatelského rozhraní při pokusu o připojení:</span><span class="sxs-lookup"><span data-stu-id="8a465-168">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="8a465-169">Následující kód používá `onreconnected` k aktualizaci uživatelského rozhraní při připojení:</span><span class="sxs-lookup"><span data-stu-id="8a465-169">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="8a465-170">SignalR3,0 a novější poskytuje vlastní prostředek pro obslužné rutiny autorizace, pokud metoda rozbočovače vyžaduje autorizaci.</span><span class="sxs-lookup"><span data-stu-id="8a465-170">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="8a465-171">Prostředek je instancí `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="8a465-171">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="8a465-172">`HubInvocationContext`Obsahuje:</span><span class="sxs-lookup"><span data-stu-id="8a465-172">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="8a465-173">Název vyvolané metody centra</span><span class="sxs-lookup"><span data-stu-id="8a465-173">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="8a465-174">Argumenty metody hub.</span><span class="sxs-lookup"><span data-stu-id="8a465-174">Arguments to the hub method.</span></span>

<span data-ttu-id="8a465-175">Vezměte v úvahu následující příklad aplikace chatovací místnosti umožňující přihlášení více organizací prostřednictvím Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8a465-175">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="8a465-176">Kdokoli s účet Microsoft se může přihlásit ke konverzaci, ale jenom členové vlastnící organizace můžou zakázat nebo zobrazit historie chatu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="8a465-176">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="8a465-177">Aplikace může omezit určité funkce pro konkrétní uživatele.</span><span class="sxs-lookup"><span data-stu-id="8a465-177">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="8a465-178">V předchozím kódu `DomainRestrictedRequirement` slouží jako vlastní `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="8a465-178">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="8a465-179">Vzhledem k tomu, že se `HubInvocationContext` parametr prostředku předává, interní logika může:</span><span class="sxs-lookup"><span data-stu-id="8a465-179">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="8a465-180">Zkontrolujte kontext, ve kterém se centrum volá.</span><span class="sxs-lookup"><span data-stu-id="8a465-180">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="8a465-181">Rozhodnutí, jak umožnit uživateli spouštět jednotlivé metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="8a465-181">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="8a465-182">Jednotlivé metody rozbočovače mohou být označeny názvem zásady, které kontrolují kód v době běhu.</span><span class="sxs-lookup"><span data-stu-id="8a465-182">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="8a465-183">Jelikož se klienti pokoušejí zavolat jednotlivé metody centra, `DomainRestrictedRequirement` obslužná rutina se spustí a řídí přístup k metodám.</span><span class="sxs-lookup"><span data-stu-id="8a465-183">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="8a465-184">Na základě způsobu přístupu k `DomainRestrictedRequirement` ovládacím prvkům:</span><span class="sxs-lookup"><span data-stu-id="8a465-184">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="8a465-185">Všichni přihlášení uživatelé mohou zavolat `SendMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="8a465-185">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="8a465-186">Historie uživatelů mohou zobrazit pouze uživatelé, kteří se přihlásili pomocí `@jabbr.net` e-mailové adresy.</span><span class="sxs-lookup"><span data-stu-id="8a465-186">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="8a465-187">`bob42@jabbr.net`Může zakázat jenom uživatele z konverzační místnosti.</span><span class="sxs-lookup"><span data-stu-id="8a465-187">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="8a465-188">Vytváření `DomainRestricted` zásad může zahrnovat:</span><span class="sxs-lookup"><span data-stu-id="8a465-188">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="8a465-189">V *Startup.cs*přidejte novou zásadu.</span><span class="sxs-lookup"><span data-stu-id="8a465-189">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="8a465-190">Zadejte vlastní `DomainRestrictedRequirement` požadavek jako parametr.</span><span class="sxs-lookup"><span data-stu-id="8a465-190">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="8a465-191">Probíhá registrace `DomainRestricted` pomocí middleware autorizace.</span><span class="sxs-lookup"><span data-stu-id="8a465-191">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="8a465-192">SignalRcentra používají [Směrování koncových bodů](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="8a465-192">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="8a465-193">SignalRpřipojení k rozbočovači bylo dříve provedeno explicitně:</span><span class="sxs-lookup"><span data-stu-id="8a465-193">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="8a465-194">V předchozí verzi potřebují vývojáři na nejrůznějších místech nastavovat řadiče, Razor stránky a centra.</span><span class="sxs-lookup"><span data-stu-id="8a465-194">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="8a465-195">Explicitní výsledky připojení v řadě téměř stejných segmentů směrování:</span><span class="sxs-lookup"><span data-stu-id="8a465-195">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="8a465-196">SignalRcentra 3,0 se dají směrovat přes směrování koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="8a465-196">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="8a465-197">Se směrováním koncových bodů se obvykle dá nakonfigurovat všechny směrování v nástroji `UseRouting` :</span><span class="sxs-lookup"><span data-stu-id="8a465-197">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="8a465-198">Bylo přidáno ASP.NET Core 3,0 SignalR :</span><span class="sxs-lookup"><span data-stu-id="8a465-198">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="8a465-199">Streamování mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="8a465-199">Client-to-server streaming.</span></span> <span data-ttu-id="8a465-200">U datových proudů mezi klientem a serverem můžou metody na straně serveru přebírat instance `IAsyncEnumerable<T>` nebo `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="8a465-200">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="8a465-201">V následující ukázce jazyka C# `UploadStream` bude metoda v centru přijímat proud řetězců z klienta:</span><span class="sxs-lookup"><span data-stu-id="8a465-201">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="8a465-202">Klientské aplikace .NET můžou předat `IAsyncEnumerable<T>` `ChannelReader<T>` instanci nebo jako `stream` argument `UploadStream` metody hub výše.</span><span class="sxs-lookup"><span data-stu-id="8a465-202">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="8a465-203">Až se `for` smyčka dokončí a místní funkce se ukončí, pošle se dokončování datového proudu:</span><span class="sxs-lookup"><span data-stu-id="8a465-203">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="8a465-204">Klientské aplikace JavaScriptu používají SignalR `Subject` pro [RxJS Subject](https://rxjs.dev/api/index/class/Subject) `stream` argument `UploadStream` výše uvedené metody hub (nebo předmět RxJS).</span><span class="sxs-lookup"><span data-stu-id="8a465-204">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="8a465-205">Kód jazyka JavaScript může použít `subject.next` metodu ke zpracování řetězců při jejich zachycení a připravení na odeslání na server.</span><span class="sxs-lookup"><span data-stu-id="8a465-205">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="8a465-206">Pomocí kódu, například dvou předchozích fragmentů, lze vytvořit prostředí streamování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="8a465-206">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="8a465-207">Nová serializace JSON</span><span class="sxs-lookup"><span data-stu-id="8a465-207">New JSON serialization</span></span>

<span data-ttu-id="8a465-208">ASP.NET Core 3,0 teď používá standardně <xref:System.Text.Json> pro serializaci JSON:</span><span class="sxs-lookup"><span data-stu-id="8a465-208">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="8a465-209">Čte a zapisuje JSON asynchronně.</span><span class="sxs-lookup"><span data-stu-id="8a465-209">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="8a465-210">Je optimalizován pro text v kódování UTF-8.</span><span class="sxs-lookup"><span data-stu-id="8a465-210">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="8a465-211">Obvykle vyšší výkon než `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="8a465-211">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="8a465-212">Pokud chcete přidat Json.NET do ASP.NET Core 3,0, přečtěte si téma [Přidání podpory formátu JSON založeného na Newtonsoft.Js](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="8a465-212">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-no-locrazor-directives"></a><span data-ttu-id="8a465-213">Nové Razor direktivy</span><span class="sxs-lookup"><span data-stu-id="8a465-213">New Razor directives</span></span>

<span data-ttu-id="8a465-214">Následující seznam obsahuje nové Razor direktivy:</span><span class="sxs-lookup"><span data-stu-id="8a465-214">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="8a465-215">[`@attribute`](xref:mvc/views/razor#attribute): `@attribute` Direktiva aplikuje daný atribut na třídu generované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8a465-215">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="8a465-216">Například, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="8a465-216">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="8a465-217">[`@implements`](xref:mvc/views/razor#implements): `@implements` Direktiva implementuje rozhraní pro generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="8a465-217">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="8a465-218">Například, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="8a465-218">For example, `@implements IDisposable`.</span></span>

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="8a465-219">IdentityServer4 podporuje ověřování a autorizaci pro webová rozhraní API a jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="8a465-219">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="8a465-220">ASP.NET Core 3,0 nabízí ověřování v aplikacích s jednou stránkou (jednostránkové) s využitím podpory pro autorizaci webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8a465-220">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="8a465-221">ASP.NET Core Identity pro ověřování a ukládání uživatelů se v kombinaci s [ Identity Server4](https://identityserver.io/) pro implementaci OpenID připojení.</span><span class="sxs-lookup"><span data-stu-id="8a465-221">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="8a465-222">IdentityServer4 je rozhraní OpenID Connect a OAuth 2,0 Framework pro ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="8a465-222">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a465-223">Umožňuje následující funkce zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="8a465-223">It enables the following security features:</span></span>

* <span data-ttu-id="8a465-224">Ověřování jako služba (AaaS)</span><span class="sxs-lookup"><span data-stu-id="8a465-224">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="8a465-225">Jednotné přihlašování/vypínání (SSO) nad více typy aplikací</span><span class="sxs-lookup"><span data-stu-id="8a465-225">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="8a465-226">Řízení přístupu pro rozhraní API</span><span class="sxs-lookup"><span data-stu-id="8a465-226">Access control for APIs</span></span>
* <span data-ttu-id="8a465-227">Federační brána</span><span class="sxs-lookup"><span data-stu-id="8a465-227">Federation Gateway</span></span>

<span data-ttu-id="8a465-228">Další informace najdete v [dokumentaci k Identity Server4 nebo v](http://docs.identityserver.io/en/latest/index.html) článku věnovaném [ověřování a autorizaci pro jednostránkové](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="8a465-228">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="8a465-229">Ověřování pomocí certifikátů a protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="8a465-229">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="8a465-230">Ověřování certifikátu vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="8a465-230">Certificate authentication requires:</span></span>

* <span data-ttu-id="8a465-231">Konfigurace serveru pro příjem certifikátů.</span><span class="sxs-lookup"><span data-stu-id="8a465-231">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="8a465-232">Přidání middleware ověřování v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8a465-232">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="8a465-233">Přidání služby ověřování certifikátů v nástroji `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8a465-233">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="8a465-234">Možnosti ověřování certifikátů zahrnují možnost:</span><span class="sxs-lookup"><span data-stu-id="8a465-234">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="8a465-235">Přijměte certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="8a465-235">Accept self-signed certificates.</span></span>
* <span data-ttu-id="8a465-236">Kontrolovat odvolání certifikátu.</span><span class="sxs-lookup"><span data-stu-id="8a465-236">Check for certificate revocation.</span></span>
* <span data-ttu-id="8a465-237">Ověřte, že certifikát proffered má v něm správné příznaky použití.</span><span class="sxs-lookup"><span data-stu-id="8a465-237">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="8a465-238">Výchozí objekt zabezpečení uživatele je vytvořen z vlastností certifikátu.</span><span class="sxs-lookup"><span data-stu-id="8a465-238">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="8a465-239">Objekt zabezpečení uživatele obsahuje událost, která umožňuje doplňování nebo nahrazení objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="8a465-239">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="8a465-240">Další informace naleznete v tématu <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="8a465-240">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="8a465-241">[Ověřování systému Windows](/windows-server/security/windows-authentication/windows-authentication-overview) bylo rozšířeno na Linux a MacOS.</span><span class="sxs-lookup"><span data-stu-id="8a465-241">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="8a465-242">V předchozích verzích bylo ověřování systému Windows omezeno na [službu IIS](xref:host-and-deploy/iis/index) a [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="8a465-242">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="8a465-243">V ASP.NET Core 3,0 může [Kestrel](xref:fundamentals/servers/kestrel) používat funkci Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)a [NTLM v systémech Windows](/windows-server/security/kerberos/ntlm-overview), Linux a MacOS pro hostitele připojené k doméně systému Windows.</span><span class="sxs-lookup"><span data-stu-id="8a465-243">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="8a465-244">Podporu Kestrel těchto schémat ověřování poskytuje balíček [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) .</span><span class="sxs-lookup"><span data-stu-id="8a465-244">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="8a465-245">Stejně jako u ostatních ověřovacích služeb nakonfigurujte aplikaci ověřování na šířku a pak nakonfigurujte službu:</span><span class="sxs-lookup"><span data-stu-id="8a465-245">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="8a465-246">Požadavky na hostitele:</span><span class="sxs-lookup"><span data-stu-id="8a465-246">Host requirements:</span></span>

* <span data-ttu-id="8a465-247">Hostitelé systému Windows musí mít k uživatelskému účtu, který je hostitelem aplikace, přidány [hlavní názvy služby](/windows/win32/ad/service-principal-names) (SPN).</span><span class="sxs-lookup"><span data-stu-id="8a465-247">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="8a465-248">Počítače se systémem Linux a macOS musí být připojeny k doméně.</span><span class="sxs-lookup"><span data-stu-id="8a465-248">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="8a465-249">Pro webový proces musí být vytvořeny hlavní názvy služby (SPN).</span><span class="sxs-lookup"><span data-stu-id="8a465-249">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="8a465-250">Na hostitelském počítači musí být vygenerovány a nakonfigurovány [soubory keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) .</span><span class="sxs-lookup"><span data-stu-id="8a465-250">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="8a465-251">Další informace naleznete v tématu <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="8a465-251">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="8a465-252">Změny šablony</span><span class="sxs-lookup"><span data-stu-id="8a465-252">Template changes</span></span>

<span data-ttu-id="8a465-253">Šablony uživatelského rozhraní webu ( Razor stránky, MVC s kontrolérem a zobrazeními) mají následující odebrané:</span><span class="sxs-lookup"><span data-stu-id="8a465-253">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="8a465-254">cookieUživatelské rozhraní pro vyjádření souhlasu již není zahrnuto.</span><span class="sxs-lookup"><span data-stu-id="8a465-254">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="8a465-255">Pokud chcete povolit cookie funkci souhlasu v aplikaci vygenerovanou šablonou ASP.NET Core 3,0, přečtěte si téma <xref:security/gdpr> .</span><span class="sxs-lookup"><span data-stu-id="8a465-255">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="8a465-256">Skripty a související statické prostředky jsou nyní odkazovány jako místní soubory namísto použití sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="8a465-256">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="8a465-257">Další informace najdete v tématu o [skriptech a souvisejících statických prostředcích se teď odkazuje jako na místní soubory namísto použití sítě CDN v závislosti na aktuálním prostředí (ASPNET/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="8a465-257">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="8a465-258">Úhlová šablona se aktualizovala tak, aby používala úhlové 8.</span><span class="sxs-lookup"><span data-stu-id="8a465-258">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="8a465-259">RazorŠablona knihovny tříd (RCL) se standardně Razor používá pro vývoj komponent.</span><span class="sxs-lookup"><span data-stu-id="8a465-259">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="8a465-260">Nová možnost šablony v aplikaci Visual Studio poskytuje podporu šablon pro stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8a465-260">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="8a465-261">Při vytváření RCL ze šablony v příkazovém prostředí, předejte `--support-pages-and-views` možnost ( `dotnet new razorclasslib --support-pages-and-views` ).</span><span class="sxs-lookup"><span data-stu-id="8a465-261">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="8a465-262">Obecný hostitel</span><span class="sxs-lookup"><span data-stu-id="8a465-262">Generic Host</span></span>

<span data-ttu-id="8a465-263">Šablony ASP.NET Core 3,0 používají <xref:fundamentals/host/generic-host> .</span><span class="sxs-lookup"><span data-stu-id="8a465-263">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="8a465-264">Používaly se předchozí verze <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8a465-264">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="8a465-265">Použití obecného hostitele .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ) poskytuje lepší integraci ASP.NET Corech aplikací s jinými serverovými scénáři, které nejsou specifické pro web.</span><span class="sxs-lookup"><span data-stu-id="8a465-265">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="8a465-266">Další informace najdete v tématu [HostBuilder nahrazuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="8a465-266">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="8a465-267">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="8a465-267">Host configuration</span></span>

<span data-ttu-id="8a465-268">Před vydáním verze ASP.NET Core 3,0 byly načteny proměnné prostředí s předponou `ASPNETCORE_` pro konfiguraci hostitele webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="8a465-268">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="8a465-269">V 3,0 `AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `DOTNET_` pro pro konfiguraci hostitele s `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8a465-269">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="8a465-270">Změny při vkládání spouštěcích konstruktorů</span><span class="sxs-lookup"><span data-stu-id="8a465-270">Changes to Startup constructor injection</span></span>

<span data-ttu-id="8a465-271">Obecný hostitel podporuje pouze následující typy pro `Startup` Injektáže konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="8a465-271">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8a465-272">Všechny služby mohou být stále vloženy přímo jako argumenty `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="8a465-272">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="8a465-273">Další informace najdete v tématu [obecný hostitel omezuje úvodní vkládání spouštěcího konstruktoru (ASPNET/](https://github.com/aspnet/Announcements/issues/353)informers #353).</span><span class="sxs-lookup"><span data-stu-id="8a465-273">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="8a465-274">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a465-274">Kestrel</span></span>

* <span data-ttu-id="8a465-275">Konfigurace Kestrel byla aktualizována pro migraci na obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="8a465-275">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="8a465-276">V 3,0 je Kestrel nakonfigurovaný na tvůrci webového hostitele, který poskytuje `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="8a465-276">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="8a465-277">Připojovací adaptéry byly odebrány z Kestrel a nahrazeny middlewarem připojení, který je v kanálu ASP.NET Core podobný Middlewari HTTP, ale pro připojení nižší úrovně.</span><span class="sxs-lookup"><span data-stu-id="8a465-277">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="8a465-278">Přenosová vrstva Kestrel byla vystavena jako veřejné rozhraní v `Connections.Abstractions` .</span><span class="sxs-lookup"><span data-stu-id="8a465-278">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="8a465-279">Nejednoznačnost mezi záhlavími a přípojnou čárkou byla vyřešena přesunutím koncových hlaviček do nové kolekce.</span><span class="sxs-lookup"><span data-stu-id="8a465-279">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="8a465-280">Synchronní vstupně-výstupní rozhraní API, jako `HttpRequest.Body.Read` je například, jsou běžným zdrojem vyčerpání vláken, který vede k chybám aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a465-280">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="8a465-281">V 3,0 `AllowSynchronousIO` je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="8a465-281">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="8a465-282">Další informace naleznete v tématu <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="8a465-282">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="8a465-283">HTTP/2 povoleno ve výchozím nastavení</span><span class="sxs-lookup"><span data-stu-id="8a465-283">HTTP/2 enabled by default</span></span>

<span data-ttu-id="8a465-284">Protokol HTTP/2 je ve výchozím nastavení povolený pro koncové body HTTPS v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a465-284">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="8a465-285">Podpora HTTP/2 pro službu IIS nebo HTTP.sys je povolená, když operační systém podporuje.</span><span class="sxs-lookup"><span data-stu-id="8a465-285">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="8a465-286">EventCounters na žádost</span><span class="sxs-lookup"><span data-stu-id="8a465-286">EventCounters on request</span></span>

<span data-ttu-id="8a465-287">Hostování EventSource, `Microsoft.AspNetCore.Hosting` vygeneruje následující nové <xref:System.Diagnostics.Tracing.EventCounter> typy týkající se příchozích požadavků:</span><span class="sxs-lookup"><span data-stu-id="8a465-287">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="8a465-288">Směrování koncových bodů</span><span class="sxs-lookup"><span data-stu-id="8a465-288">Endpoint routing</span></span>

<span data-ttu-id="8a465-289">Směrování koncového bodu, které umožňuje architekturám (například MVC) pracovat dobře s middlewarem, je vylepšeno:</span><span class="sxs-lookup"><span data-stu-id="8a465-289">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="8a465-290">Pořadí middlewaru a koncových bodů je možné nakonfigurovat v kanálu zpracování požadavků `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8a465-290">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="8a465-291">Koncové body a middleware se dobře zakládají s dalšími ASP.NET Core technologiemi, jako jsou například kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="8a465-291">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="8a465-292">Koncové body můžou implementovat zásadu, třeba CORS nebo autorizaci, a to v middlewaru i MVC.</span><span class="sxs-lookup"><span data-stu-id="8a465-292">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="8a465-293">Filtry a atributy lze umístit na metody v řadičích.</span><span class="sxs-lookup"><span data-stu-id="8a465-293">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="8a465-294">Další informace naleznete v tématu <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="8a465-294">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="8a465-295">Kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="8a465-295">Health Checks</span></span>

<span data-ttu-id="8a465-296">Kontroly stavu používají směrování koncových bodů u obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="8a465-296">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="8a465-297">V `Startup.Configure` nástroji zavolejte `MapHealthChecks` na tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="8a465-297">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="8a465-298">Koncové body kontrol stavu můžou:</span><span class="sxs-lookup"><span data-stu-id="8a465-298">Health Checks endpoints can:</span></span>

* <span data-ttu-id="8a465-299">Zadejte minimálně jednoho povoleného hostitele nebo portů.</span><span class="sxs-lookup"><span data-stu-id="8a465-299">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="8a465-300">Vyžadovat autorizaci.</span><span class="sxs-lookup"><span data-stu-id="8a465-300">Require authorization.</span></span>
* <span data-ttu-id="8a465-301">Vyžadovat CORS</span><span class="sxs-lookup"><span data-stu-id="8a465-301">Require CORS.</span></span>

<span data-ttu-id="8a465-302">Další informace najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="8a465-302">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="8a465-303">Kanály na HttpContext</span><span class="sxs-lookup"><span data-stu-id="8a465-303">Pipes on HttpContext</span></span>

<span data-ttu-id="8a465-304">Nyní je možné číst text žádosti a zapsat tělo odpovědi pomocí <xref:System.IO.Pipelines> rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8a465-304">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="8a465-305">Prostředek</span><span class="sxs-lookup"><span data-stu-id="8a465-305">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="8a465-306">`HttpRequest.BodyReader`vlastnost poskytuje objekt <xref:System.IO.Pipelines.PipeReader> , který lze použít ke čtení textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="8a465-306">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="8a465-307">Prostředek</span><span class="sxs-lookup"><span data-stu-id="8a465-307">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="8a465-308">`HttpResponse.BodyWriter`vlastnost poskytuje <xref:System.IO.Pipelines.PipeWriter> , kterou lze použít k zápisu textu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="8a465-308">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="8a465-309">`HttpRequest.BodyReader`je analogkou `HttpRequest.Body` datového proudu.</span><span class="sxs-lookup"><span data-stu-id="8a465-309">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="8a465-310">`HttpResponse.BodyWriter`je analogkou `HttpResponse.Body` datového proudu.</span><span class="sxs-lookup"><span data-stu-id="8a465-310">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="8a465-311">Vylepšené zasílání zpráv o chybách ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="8a465-311">Improved error reporting in IIS</span></span>

<span data-ttu-id="8a465-312">Chyby při spuštění při hostování ASP.NET Corech aplikací ve službě IIS teď vytváří rozsáhlejší diagnostická data.</span><span class="sxs-lookup"><span data-stu-id="8a465-312">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="8a465-313">Tyto chyby jsou hlášeny do protokolu událostí systému Windows s trasováním zásobníku, kdykoli je to možné.</span><span class="sxs-lookup"><span data-stu-id="8a465-313">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="8a465-314">Kromě toho jsou všechna upozornění, chyby a neošetřené výjimky protokolovány do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="8a465-314">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="8a465-315">Služba pracovních procesů a sada SDK pracovních procesů</span><span class="sxs-lookup"><span data-stu-id="8a465-315">Worker Service and Worker SDK</span></span>

<span data-ttu-id="8a465-316">.NET Core 3,0 zavádí novou šablonu aplikace služby pracovního procesu.</span><span class="sxs-lookup"><span data-stu-id="8a465-316">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="8a465-317">Tato šablona poskytuje výchozí bod pro psaní dlouhých služeb, které běží v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a465-317">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="8a465-318">Další informace:</span><span class="sxs-lookup"><span data-stu-id="8a465-318">For more information, see:</span></span>

* [<span data-ttu-id="8a465-319">Pracovní procesy .NET Core jako služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="8a465-319">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="8a465-320">Vylepšení middlewaru u předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="8a465-320">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="8a465-321">V předchozích verzích ASP.NET Core volání <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> bylo problematické při nasazení do Azure Linux nebo za libovolný reverzní proxy server jiný než IIS.</span><span class="sxs-lookup"><span data-stu-id="8a465-321">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="8a465-322">Oprava pro předchozí verze je popsána v [části dopředného schématu pro reverzní proxy servery se systémy Linux a non-IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="8a465-322">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="8a465-323">Tento scénář je opravený v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="8a465-323">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a465-324">Hostitel povoluje [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) , pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="8a465-324">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="8a465-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`je nastavené na `true` obrázky kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="8a465-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="8a465-326">Vylepšení výkonu</span><span class="sxs-lookup"><span data-stu-id="8a465-326">Performance improvements</span></span>

<span data-ttu-id="8a465-327">ASP.NET Core 3,0 obsahuje mnoho vylepšení, která omezují využití paměti a zvyšují propustnost:</span><span class="sxs-lookup"><span data-stu-id="8a465-327">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="8a465-328">Snížení využití paměti při použití integrovaného kontejneru vkládání závislostí pro vymezené služby.</span><span class="sxs-lookup"><span data-stu-id="8a465-328">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="8a465-329">Snížení počtu přidělení napříč rámec, včetně scénářů middlewaru a směrování.</span><span class="sxs-lookup"><span data-stu-id="8a465-329">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="8a465-330">Snížení využití paměti pro připojení protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="8a465-330">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="8a465-331">Snižování paměti a vylepšení propustnosti pro připojení HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8a465-331">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="8a465-332">Nový optimalizovaný a plně asynchronní serializátor JSON.</span><span class="sxs-lookup"><span data-stu-id="8a465-332">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="8a465-333">Snížení využití paměti a vylepšení propustnosti při analýze formuláře.</span><span class="sxs-lookup"><span data-stu-id="8a465-333">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="8a465-334">ASP.NET Core 3,0 běží pouze na .NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="8a465-334">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="8a465-335">Od ASP.NET Core 3,0 .NET Framework již není podporovanou cílovou architekturou.</span><span class="sxs-lookup"><span data-stu-id="8a465-335">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="8a465-336">Projekty cílené na .NET Framework můžou v plně podporovaném způsobem dál používat [LTS verzi pro .NET Core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="8a465-336">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="8a465-337">Většina balíčků s podporou ASP.NET Core 2.1. x bude po dobu tří let LTS pro .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="8a465-337">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="8a465-338">Informace o migraci najdete v tématu [portování kódu z .NET Framework do .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="8a465-338">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="8a465-339">Použití sdíleného rozhraní ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a465-339">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8a465-340">Sdílené rozhraní ASP.NET Core 3,0, obsažené v souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), již nevyžaduje explicitní `<PackageReference />` prvek v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="8a465-340">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="8a465-341">Sdílené rozhraní je automaticky odkazováno při použití `Microsoft.NET.Sdk.Web` sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="8a465-341">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="8a465-342">Sestavení odebraná z ASP.NET Core sdílené rozhraní</span><span class="sxs-lookup"><span data-stu-id="8a465-342">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8a465-343">Nejvýznamnější sestavení odebraný ze sdílené architektury ASP.NET Core 3,0 jsou:</span><span class="sxs-lookup"><span data-stu-id="8a465-343">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="8a465-344">[Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.NET).</span><span class="sxs-lookup"><span data-stu-id="8a465-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="8a465-345">Pokud chcete přidat Json.NET do ASP.NET Core 3,0, přečtěte si téma [Přidání podpory formátu JSON založeného na Newtonsoft.Js](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="8a465-345">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="8a465-346">ASP.NET Core 3,0 zavádí `System.Text.Json` pro čtení a zápis formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="8a465-346">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="8a465-347">Další informace najdete v tématu [Nová serializace JSON](#new-json-serialization) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="8a465-347">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="8a465-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8a465-348">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="8a465-349">Úplný seznam sestavení odebraných ze sdíleného rozhraní najdete v tématu sestavení, [která jsou odebírána z Microsoft. AspNetCore. App 3,0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="8a465-349">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="8a465-350">Další informace o motivaci této změny najdete v tématu přemístění [změn do Microsoft. AspNetCore. app v 3,0](https://github.com/aspnet/Announcements/issues/325) a [první pohled na změny, které přicházejí v ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="8a465-350">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 