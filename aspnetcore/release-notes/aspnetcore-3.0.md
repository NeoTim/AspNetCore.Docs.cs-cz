---
title: Co je nového v ASP.NET Core 3,0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: f2588665c26887a6e3864866425b887e97e656d5
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755869"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="2457b-103">Co je nového v ASP.NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="2457b-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="2457b-104">Tento článek popisuje nejvýznamnější změny v ASP.NET Core 3,0 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="2457b-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="2457b-105">je nová architektura v ASP.NET Core pro vytváření interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET:</span><span class="sxs-lookup"><span data-stu-id="2457b-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="2457b-106">Vytvářejte bohatá interaktivní uživatelská rozhraní pomocí jazyka C# namísto JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="2457b-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="2457b-107">Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.</span><span class="sxs-lookup"><span data-stu-id="2457b-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="2457b-108">Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="2457b-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="2457b-109">scénáře podporované rozhraním:</span><span class="sxs-lookup"><span data-stu-id="2457b-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="2457b-110">Opakovaně použitelné součásti uživatelského rozhraní ( Razor součásti)</span><span class="sxs-lookup"><span data-stu-id="2457b-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="2457b-111">Směrování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="2457b-111">Client-side routing</span></span>
* <span data-ttu-id="2457b-112">Rozložení komponent</span><span class="sxs-lookup"><span data-stu-id="2457b-112">Component layouts</span></span>
* <span data-ttu-id="2457b-113">Podpora pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="2457b-113">Support for dependency injection</span></span>
* <span data-ttu-id="2457b-114">Formuláře a ověřování</span><span class="sxs-lookup"><span data-stu-id="2457b-114">Forms and validation</span></span>
* <span data-ttu-id="2457b-115">Sestavení knihoven komponent pomocí Razor knihoven tříd</span><span class="sxs-lookup"><span data-stu-id="2457b-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="2457b-116">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2457b-116">JavaScript interop</span></span>

<span data-ttu-id="2457b-117">Další informace naleznete v tématu <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="2457b-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="2457b-118">WebServer</span><span class="sxs-lookup"><span data-stu-id="2457b-118"> Server</span></span>

Blazor<span data-ttu-id="2457b-119">odpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2457b-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="2457b-120">Server poskytuje podporu pro hostování Razor komponent na serveru aplikace v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2457b-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="2457b-121">Aktualizace uživatelského rozhraní se zpracovávají přes SignalR připojení.</span><span class="sxs-lookup"><span data-stu-id="2457b-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="2457b-122">Server je podporován v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2457b-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a>Blazor<span data-ttu-id="2457b-123">WebAssembly (Preview)</span><span class="sxs-lookup"><span data-stu-id="2457b-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="2457b-124">aplikace je také možné spustit přímo v prohlížeči pomocí rozhraní .NET runtime založeného na WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="2457b-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="2457b-125">Sestavení WebAssembly je ve verzi Preview a *není* podporované v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2457b-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="2457b-126">WebAssembly bude podporováno v budoucí verzi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2457b-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a>Razor<span data-ttu-id="2457b-127">konstrukční</span><span class="sxs-lookup"><span data-stu-id="2457b-127"> components</span></span>

Blazor<span data-ttu-id="2457b-128">aplikace jsou sestavené z komponent.</span><span class="sxs-lookup"><span data-stu-id="2457b-128"> apps are built from components.</span></span> <span data-ttu-id="2457b-129">Komponenty jsou samostatné bloky uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="2457b-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="2457b-130">Komponenty jsou normální třídy .NET, které definují logiku vykreslování uživatelského rozhraní a obslužné rutiny událostí na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="2457b-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="2457b-131">Můžete vytvářet bohatě interaktivní webové aplikace bez JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="2457b-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="2457b-132">Komponenty v Blazor jsou obvykle vytvořeny pomocí Razor syntaxe, přirozeného Blendu HTML a jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2457b-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> Razor<span data-ttu-id="2457b-133">komponenty jsou podobné Razor zobrazením stránky a MVC v tom, že obě používají Razor .</span><span class="sxs-lookup"><span data-stu-id="2457b-133"> components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="2457b-134">Na rozdíl od stránek a zobrazení, které jsou založeny na modelu požadavek-odpověď, se komponenty používají konkrétně pro zpracování kompozice uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2457b-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="2457b-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="2457b-135">gRPC</span></span>

<span data-ttu-id="2457b-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="2457b-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="2457b-137">Je oblíbeným a vysoce výkonným rozhraním vzdáleného volání procedur (RPC).</span><span class="sxs-lookup"><span data-stu-id="2457b-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="2457b-138">Nabízí dogmatickýmý přístup k vývoji rozhraní API, který je prvním kontraktem.</span><span class="sxs-lookup"><span data-stu-id="2457b-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="2457b-139">Používá moderní technologie, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="2457b-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="2457b-140">HTTP/2 pro přenos.</span><span class="sxs-lookup"><span data-stu-id="2457b-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="2457b-141">Vyrovnávací paměti protokolu jako jazyk popisu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2457b-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="2457b-142">Formát binární serializace.</span><span class="sxs-lookup"><span data-stu-id="2457b-142">Binary serialization format.</span></span>
* <span data-ttu-id="2457b-143">Poskytuje funkce, jako například:</span><span class="sxs-lookup"><span data-stu-id="2457b-143">Provides features such as:</span></span>

  * <span data-ttu-id="2457b-144">Authentication</span><span class="sxs-lookup"><span data-stu-id="2457b-144">Authentication</span></span>
  * <span data-ttu-id="2457b-145">Obousměrné streamování a řízení toku.</span><span class="sxs-lookup"><span data-stu-id="2457b-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="2457b-146">Zrušení a vypršení časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2457b-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="2457b-147">funkce gRPC v ASP.NET Core 3,0 obsahuje:</span><span class="sxs-lookup"><span data-stu-id="2457b-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="2457b-148">[Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): ASP.NET Core Framework pro hostování služeb Grpc Services.</span><span class="sxs-lookup"><span data-stu-id="2457b-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="2457b-149">gRPC se na ASP.NET Core integruje se standardními ASP.NET Core funkcemi, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="2457b-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="2457b-150">[Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client): klient Grpc pro .NET Core, který sestaví na známém `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2457b-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="2457b-151">[Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): klientská integrace Grpc s `HttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="2457b-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="2457b-152">Další informace naleznete v tématu <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="2457b-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="2457b-153">Pokyny k migraci najdete v tématu [aktualizace SignalR kódu](xref:migration/22-to-30#signalr) .</span><span class="sxs-lookup"><span data-stu-id="2457b-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="2457b-154">nyní používá `System.Text.Json` k serializaci nebo deserializaci zpráv JSON.</span><span class="sxs-lookup"><span data-stu-id="2457b-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="2457b-155">Pokyny pro obnovení serializátoru založeného na systému najdete v tématu [Přepnutí na Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="2457b-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="2457b-156">V klientech JavaScript a .NET pro SignalR se podpora přidala pro automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="2457b-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="2457b-157">Ve výchozím nastavení se klient pokusí znovu připojit hned a v případě potřeby opakovat po 2, 10 a 30 sekundách.</span><span class="sxs-lookup"><span data-stu-id="2457b-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="2457b-158">Pokud se klient úspěšně znovu připojí, obdrží nové ID připojení.</span><span class="sxs-lookup"><span data-stu-id="2457b-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="2457b-159">Automatické opětovné připojení je výslovný souhlas:</span><span class="sxs-lookup"><span data-stu-id="2457b-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="2457b-160">Intervaly opětovného připojení lze zadat předáním pole doby trvání založené na milisekundách:</span><span class="sxs-lookup"><span data-stu-id="2457b-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="2457b-161">Vlastní implementaci lze předat v rámci úplného řízení intervalů opětovného připojení.</span><span class="sxs-lookup"><span data-stu-id="2457b-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="2457b-162">Pokud se opětovné připojení nezdařilo po intervalu posledního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="2457b-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="2457b-163">Klient považuje připojení za offline.</span><span class="sxs-lookup"><span data-stu-id="2457b-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="2457b-164">Klient se ukončí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="2457b-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="2457b-165">Během opakovaného pokusu o připojení aktualizujte uživatelské rozhraní aplikace a upozorněte uživatele, že probíhá pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="2457b-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="2457b-166">Pokud chcete poskytnout zpětnou vazbu uživatelského rozhraní při přerušení připojení, SignalR rozhraní API klienta se rozšířilo tak, aby zahrnovalo následující obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="2457b-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="2457b-167">`onreconnecting`: Dává vývojářům možnost zakázat uživatelské rozhraní nebo upozornit uživatele na to, že je aplikace offline.</span><span class="sxs-lookup"><span data-stu-id="2457b-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="2457b-168">`onreconnected`: Dává vývojářům možnost aktualizovat uživatelské rozhraní po opětovném navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="2457b-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="2457b-169">Následující kód používá `onreconnecting` k aktualizaci uživatelského rozhraní při pokusu o připojení:</span><span class="sxs-lookup"><span data-stu-id="2457b-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="2457b-170">Následující kód používá `onreconnected` k aktualizaci uživatelského rozhraní při připojení:</span><span class="sxs-lookup"><span data-stu-id="2457b-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="2457b-171">3,0 a novější poskytuje vlastní prostředek pro obslužné rutiny autorizace, pokud metoda rozbočovače vyžaduje autorizaci.</span><span class="sxs-lookup"><span data-stu-id="2457b-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="2457b-172">Prostředek je instancí `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="2457b-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="2457b-173">`HubInvocationContext`Obsahuje:</span><span class="sxs-lookup"><span data-stu-id="2457b-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="2457b-174">Název vyvolané metody centra</span><span class="sxs-lookup"><span data-stu-id="2457b-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="2457b-175">Argumenty metody hub.</span><span class="sxs-lookup"><span data-stu-id="2457b-175">Arguments to the hub method.</span></span>

<span data-ttu-id="2457b-176">Vezměte v úvahu následující příklad aplikace chatovací místnosti umožňující přihlášení více organizací prostřednictvím Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2457b-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="2457b-177">Kdokoli s účet Microsoft se může přihlásit ke konverzaci, ale jenom členové vlastnící organizace můžou zakázat nebo zobrazit historie chatu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2457b-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="2457b-178">Aplikace může omezit určité funkce pro konkrétní uživatele.</span><span class="sxs-lookup"><span data-stu-id="2457b-178">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="2457b-179">V předchozím kódu `DomainRestrictedRequirement` slouží jako vlastní `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="2457b-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="2457b-180">Vzhledem k tomu, že se `HubInvocationContext` parametr prostředku předává, interní logika může:</span><span class="sxs-lookup"><span data-stu-id="2457b-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="2457b-181">Zkontrolujte kontext, ve kterém se centrum volá.</span><span class="sxs-lookup"><span data-stu-id="2457b-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="2457b-182">Rozhodnutí, jak umožnit uživateli spouštět jednotlivé metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="2457b-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="2457b-183">Jednotlivé metody rozbočovače mohou být označeny názvem zásady, které kontrolují kód v době běhu.</span><span class="sxs-lookup"><span data-stu-id="2457b-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="2457b-184">Jelikož se klienti pokoušejí zavolat jednotlivé metody centra, `DomainRestrictedRequirement` obslužná rutina se spustí a řídí přístup k metodám.</span><span class="sxs-lookup"><span data-stu-id="2457b-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="2457b-185">Na základě způsobu přístupu k `DomainRestrictedRequirement` ovládacím prvkům:</span><span class="sxs-lookup"><span data-stu-id="2457b-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="2457b-186">Všichni přihlášení uživatelé mohou zavolat `SendMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="2457b-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="2457b-187">Historie uživatelů mohou zobrazit pouze uživatelé, kteří se přihlásili pomocí `@jabbr.net` e-mailové adresy.</span><span class="sxs-lookup"><span data-stu-id="2457b-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="2457b-188">`bob42@jabbr.net`Může zakázat jenom uživatele z konverzační místnosti.</span><span class="sxs-lookup"><span data-stu-id="2457b-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="2457b-189">Vytváření `DomainRestricted` zásad může zahrnovat:</span><span class="sxs-lookup"><span data-stu-id="2457b-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="2457b-190">V *Startup.cs*přidejte novou zásadu.</span><span class="sxs-lookup"><span data-stu-id="2457b-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="2457b-191">Zadejte vlastní `DomainRestrictedRequirement` požadavek jako parametr.</span><span class="sxs-lookup"><span data-stu-id="2457b-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="2457b-192">Probíhá registrace `DomainRestricted` pomocí middleware autorizace.</span><span class="sxs-lookup"><span data-stu-id="2457b-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

SignalR<span data-ttu-id="2457b-193">centra používají [Směrování koncových bodů](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="2457b-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="2457b-194">připojení k rozbočovači bylo dříve provedeno explicitně:</span><span class="sxs-lookup"><span data-stu-id="2457b-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="2457b-195">V předchozí verzi potřebují vývojáři na nejrůznějších místech nastavovat řadiče, Razor stránky a centra.</span><span class="sxs-lookup"><span data-stu-id="2457b-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="2457b-196">Explicitní výsledky připojení v řadě téměř stejných segmentů směrování:</span><span class="sxs-lookup"><span data-stu-id="2457b-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

SignalR<span data-ttu-id="2457b-197">centra 3,0 se dají směrovat přes směrování koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="2457b-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="2457b-198">Se směrováním koncových bodů se obvykle dá nakonfigurovat všechny směrování v nástroji `UseRouting` :</span><span class="sxs-lookup"><span data-stu-id="2457b-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="2457b-199">Bylo přidáno ASP.NET Core 3,0 SignalR :</span><span class="sxs-lookup"><span data-stu-id="2457b-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="2457b-200">Streamování mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="2457b-200">Client-to-server streaming.</span></span> <span data-ttu-id="2457b-201">U datových proudů mezi klientem a serverem můžou metody na straně serveru přebírat instance `IAsyncEnumerable<T>` nebo `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="2457b-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="2457b-202">V následující ukázce jazyka C# `UploadStream` bude metoda v centru přijímat proud řetězců z klienta:</span><span class="sxs-lookup"><span data-stu-id="2457b-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="2457b-203">Klientské aplikace .NET můžou předat `IAsyncEnumerable<T>` `ChannelReader<T>` instanci nebo jako `stream` argument `UploadStream` metody hub výše.</span><span class="sxs-lookup"><span data-stu-id="2457b-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="2457b-204">Až se `for` smyčka dokončí a místní funkce se ukončí, pošle se dokončování datového proudu:</span><span class="sxs-lookup"><span data-stu-id="2457b-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="2457b-205">Klientské aplikace JavaScriptu používají SignalR `Subject` pro [RxJS Subject](https://rxjs.dev/api/index/class/Subject) `stream` argument `UploadStream` výše uvedené metody hub (nebo předmět RxJS).</span><span class="sxs-lookup"><span data-stu-id="2457b-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="2457b-206">Kód jazyka JavaScript může použít `subject.next` metodu ke zpracování řetězců při jejich zachycení a připravení na odeslání na server.</span><span class="sxs-lookup"><span data-stu-id="2457b-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="2457b-207">Pomocí kódu, například dvou předchozích fragmentů, lze vytvořit prostředí streamování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="2457b-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="2457b-208">Nová serializace JSON</span><span class="sxs-lookup"><span data-stu-id="2457b-208">New JSON serialization</span></span>

<span data-ttu-id="2457b-209">ASP.NET Core 3,0 teď používá standardně <xref:System.Text.Json> pro serializaci JSON:</span><span class="sxs-lookup"><span data-stu-id="2457b-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="2457b-210">Čte a zapisuje JSON asynchronně.</span><span class="sxs-lookup"><span data-stu-id="2457b-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="2457b-211">Je optimalizován pro text v kódování UTF-8.</span><span class="sxs-lookup"><span data-stu-id="2457b-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="2457b-212">Obvykle vyšší výkon než `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="2457b-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="2457b-213">Pokud chcete přidat Json.NET do ASP.NET Core 3,0, přečtěte si téma [Přidání podpory formátu JSON založeného na Newtonsoft.Js](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="2457b-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="2457b-214">Nové Razor direktivy</span><span class="sxs-lookup"><span data-stu-id="2457b-214">New Razor directives</span></span>

<span data-ttu-id="2457b-215">Následující seznam obsahuje nové Razor direktivy:</span><span class="sxs-lookup"><span data-stu-id="2457b-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="2457b-216">[`@attribute`](xref:mvc/views/razor#attribute): `@attribute` Direktiva aplikuje daný atribut na třídu generované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2457b-216">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="2457b-217">Například, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="2457b-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="2457b-218">[`@implements`](xref:mvc/views/razor#implements): `@implements` Direktiva implementuje rozhraní pro generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="2457b-218">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="2457b-219">Například, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="2457b-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="2457b-220">IdentityServer4 podporuje ověřování a autorizaci pro webová rozhraní API a jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="2457b-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="2457b-221">ASP.NET Core 3,0 nabízí ověřování v aplikacích s jednou stránkou (jednostránkové) s využitím podpory pro autorizaci webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2457b-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="2457b-222">ASP.NET Core Identity pro ověřování a ukládání uživatelů se v kombinaci s [IdentityServer4](https://identityserver.io/) pro implementaci otevřeného ID Connect.</span><span class="sxs-lookup"><span data-stu-id="2457b-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="2457b-223">IdentityServer4 je rozhraní OpenID Connect a OAuth 2,0 Framework pro ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2457b-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="2457b-224">Umožňuje následující funkce zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="2457b-224">It enables the following security features:</span></span>

* <span data-ttu-id="2457b-225">Ověřování jako služba (AaaS)</span><span class="sxs-lookup"><span data-stu-id="2457b-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="2457b-226">Jednotné přihlašování/vypínání (SSO) nad více typy aplikací</span><span class="sxs-lookup"><span data-stu-id="2457b-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="2457b-227">Řízení přístupu pro rozhraní API</span><span class="sxs-lookup"><span data-stu-id="2457b-227">Access control for APIs</span></span>
* <span data-ttu-id="2457b-228">Federační brána</span><span class="sxs-lookup"><span data-stu-id="2457b-228">Federation Gateway</span></span>

<span data-ttu-id="2457b-229">Další informace najdete v [dokumentaci k IdentityServer4 nebo v](http://docs.identityserver.io/en/latest/index.html) článku věnovaném [ověřování a autorizaci pro jednostránkové](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="2457b-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="2457b-230">Ověřování pomocí certifikátů a protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="2457b-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="2457b-231">Ověřování certifikátu vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="2457b-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="2457b-232">Konfigurace serveru pro příjem certifikátů.</span><span class="sxs-lookup"><span data-stu-id="2457b-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="2457b-233">Přidání middleware ověřování v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2457b-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="2457b-234">Přidání služby ověřování certifikátů v nástroji `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2457b-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2457b-235">Možnosti ověřování certifikátů zahrnují možnost:</span><span class="sxs-lookup"><span data-stu-id="2457b-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="2457b-236">Přijměte certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="2457b-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="2457b-237">Kontrolovat odvolání certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2457b-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="2457b-238">Ověřte, že certifikát proffered má v něm správné příznaky použití.</span><span class="sxs-lookup"><span data-stu-id="2457b-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="2457b-239">Výchozí objekt zabezpečení uživatele je vytvořen z vlastností certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2457b-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="2457b-240">Objekt zabezpečení uživatele obsahuje událost, která umožňuje doplňování nebo nahrazení objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="2457b-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="2457b-241">Další informace naleznete v tématu <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="2457b-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="2457b-242">[Ověřování systému Windows](/windows-server/security/windows-authentication/windows-authentication-overview) bylo rozšířeno na Linux a MacOS.</span><span class="sxs-lookup"><span data-stu-id="2457b-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="2457b-243">V předchozích verzích bylo ověřování systému Windows omezeno na [službu IIS](xref:host-and-deploy/iis/index) a [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2457b-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="2457b-244">V ASP.NET Core 3,0 může [Kestrel](xref:fundamentals/servers/kestrel) používat funkci Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)a [NTLM v systémech Windows](/windows-server/security/kerberos/ntlm-overview), Linux a MacOS pro hostitele připojené k doméně systému Windows.</span><span class="sxs-lookup"><span data-stu-id="2457b-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="2457b-245">Podporu Kestrel těchto schémat ověřování poskytuje balíček [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) .</span><span class="sxs-lookup"><span data-stu-id="2457b-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="2457b-246">Stejně jako u ostatních ověřovacích služeb nakonfigurujte aplikaci ověřování na šířku a pak nakonfigurujte službu:</span><span class="sxs-lookup"><span data-stu-id="2457b-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="2457b-247">Požadavky na hostitele:</span><span class="sxs-lookup"><span data-stu-id="2457b-247">Host requirements:</span></span>

* <span data-ttu-id="2457b-248">Hostitelé systému Windows musí mít k uživatelskému účtu, který je hostitelem aplikace, přidány [hlavní názvy služby](/windows/win32/ad/service-principal-names) (SPN).</span><span class="sxs-lookup"><span data-stu-id="2457b-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="2457b-249">Počítače se systémem Linux a macOS musí být připojeny k doméně.</span><span class="sxs-lookup"><span data-stu-id="2457b-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="2457b-250">Pro webový proces musí být vytvořeny hlavní názvy služby (SPN).</span><span class="sxs-lookup"><span data-stu-id="2457b-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="2457b-251">Na hostitelském počítači musí být vygenerovány a nakonfigurovány [soubory keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) .</span><span class="sxs-lookup"><span data-stu-id="2457b-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="2457b-252">Další informace naleznete v tématu <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="2457b-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="2457b-253">Změny šablony</span><span class="sxs-lookup"><span data-stu-id="2457b-253">Template changes</span></span>

<span data-ttu-id="2457b-254">Šablony uživatelského rozhraní webu ( Razor stránky, MVC s kontrolérem a zobrazeními) mají následující odebrané:</span><span class="sxs-lookup"><span data-stu-id="2457b-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="2457b-255">Uživatelské rozhraní pro vyjádření souhlasu souborů cookie již není zahrnuto.</span><span class="sxs-lookup"><span data-stu-id="2457b-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="2457b-256">Pokud chcete povolit funkci vyjádření souhlasu souborů cookie v aplikaci vygenerovanou šablonou ASP.NET Core 3,0, přečtěte si téma <xref:security/gdpr> .</span><span class="sxs-lookup"><span data-stu-id="2457b-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="2457b-257">Skripty a související statické prostředky jsou nyní odkazovány jako místní soubory namísto použití sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="2457b-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="2457b-258">Další informace najdete v tématu o [skriptech a souvisejících statických prostředcích se teď odkazuje jako na místní soubory namísto použití sítě CDN v závislosti na aktuálním prostředí (ASPNET/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="2457b-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="2457b-259">Úhlová šablona se aktualizovala tak, aby používala úhlové 8.</span><span class="sxs-lookup"><span data-stu-id="2457b-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="2457b-260">RazorŠablona knihovny tříd (RCL) se standardně Razor používá pro vývoj komponent.</span><span class="sxs-lookup"><span data-stu-id="2457b-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="2457b-261">Nová možnost šablony v aplikaci Visual Studio poskytuje podporu šablon pro stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2457b-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="2457b-262">Při vytváření RCL ze šablony v příkazovém prostředí, předejte `--support-pages-and-views` možnost ( `dotnet new razorclasslib --support-pages-and-views` ).</span><span class="sxs-lookup"><span data-stu-id="2457b-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="2457b-263">Obecný hostitel</span><span class="sxs-lookup"><span data-stu-id="2457b-263">Generic Host</span></span>

<span data-ttu-id="2457b-264">Šablony ASP.NET Core 3,0 používají <xref:fundamentals/host/generic-host> .</span><span class="sxs-lookup"><span data-stu-id="2457b-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="2457b-265">Používaly se předchozí verze <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2457b-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="2457b-266">Použití obecného hostitele .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ) poskytuje lepší integraci ASP.NET Corech aplikací s jinými serverovými scénáři, které nejsou specifické pro web.</span><span class="sxs-lookup"><span data-stu-id="2457b-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="2457b-267">Další informace najdete v tématu [HostBuilder nahrazuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="2457b-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="2457b-268">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="2457b-268">Host configuration</span></span>

<span data-ttu-id="2457b-269">Před vydáním verze ASP.NET Core 3,0 byly načteny proměnné prostředí s předponou `ASPNETCORE_` pro konfiguraci hostitele webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="2457b-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="2457b-270">V 3,0 `AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `DOTNET_` pro pro konfiguraci hostitele s `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2457b-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="2457b-271">Změny při vkládání spouštěcích konstruktorů</span><span class="sxs-lookup"><span data-stu-id="2457b-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="2457b-272">Obecný hostitel podporuje pouze následující typy pro `Startup` Injektáže konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="2457b-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="2457b-273">Všechny služby mohou být stále vloženy přímo jako argumenty `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="2457b-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="2457b-274">Další informace najdete v tématu [obecný hostitel omezuje úvodní vkládání spouštěcího konstruktoru (ASPNET/](https://github.com/aspnet/Announcements/issues/353)informers #353).</span><span class="sxs-lookup"><span data-stu-id="2457b-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="2457b-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="2457b-275">Kestrel</span></span>

* <span data-ttu-id="2457b-276">Konfigurace Kestrel byla aktualizována pro migraci na obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="2457b-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="2457b-277">V 3,0 je Kestrel nakonfigurovaný na tvůrci webového hostitele, který poskytuje `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="2457b-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="2457b-278">Připojovací adaptéry byly odebrány z Kestrel a nahrazeny middlewarem připojení, který je v kanálu ASP.NET Core podobný Middlewari HTTP, ale pro připojení nižší úrovně.</span><span class="sxs-lookup"><span data-stu-id="2457b-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="2457b-279">Přenosová vrstva Kestrel byla vystavena jako veřejné rozhraní v `Connections.Abstractions` .</span><span class="sxs-lookup"><span data-stu-id="2457b-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="2457b-280">Nejednoznačnost mezi záhlavími a přípojnou čárkou byla vyřešena přesunutím koncových hlaviček do nové kolekce.</span><span class="sxs-lookup"><span data-stu-id="2457b-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="2457b-281">Synchronní vstupně-výstupní rozhraní API, jako `HttpRequest.Body.Read` je například, jsou běžným zdrojem vyčerpání vláken, který vede k chybám aplikace.</span><span class="sxs-lookup"><span data-stu-id="2457b-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="2457b-282">V 3,0 `AllowSynchronousIO` je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="2457b-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="2457b-283">Další informace naleznete v tématu <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="2457b-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="2457b-284">HTTP/2 povoleno ve výchozím nastavení</span><span class="sxs-lookup"><span data-stu-id="2457b-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="2457b-285">Protokol HTTP/2 je ve výchozím nastavení povolený pro koncové body HTTPS v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2457b-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="2457b-286">Podpora HTTP/2 pro službu IIS nebo HTTP.sys je povolená, když operační systém podporuje.</span><span class="sxs-lookup"><span data-stu-id="2457b-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="2457b-287">EventCounters na žádost</span><span class="sxs-lookup"><span data-stu-id="2457b-287">EventCounters on request</span></span>

<span data-ttu-id="2457b-288">Hostování EventSource, `Microsoft.AspNetCore.Hosting` vygeneruje následující nové <xref:System.Diagnostics.Tracing.EventCounter> typy týkající se příchozích požadavků:</span><span class="sxs-lookup"><span data-stu-id="2457b-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="2457b-289">Směrování koncových bodů</span><span class="sxs-lookup"><span data-stu-id="2457b-289">Endpoint routing</span></span>

<span data-ttu-id="2457b-290">Směrování koncového bodu, které umožňuje architekturám (například MVC) pracovat dobře s middlewarem, je vylepšeno:</span><span class="sxs-lookup"><span data-stu-id="2457b-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="2457b-291">Pořadí middlewaru a koncových bodů je možné nakonfigurovat v kanálu zpracování požadavků `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2457b-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="2457b-292">Koncové body a middleware se dobře zakládají s dalšími ASP.NET Core technologiemi, jako jsou například kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="2457b-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="2457b-293">Koncové body můžou implementovat zásadu, třeba CORS nebo autorizaci, a to v middlewaru i MVC.</span><span class="sxs-lookup"><span data-stu-id="2457b-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="2457b-294">Filtry a atributy lze umístit na metody v řadičích.</span><span class="sxs-lookup"><span data-stu-id="2457b-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="2457b-295">Další informace naleznete v tématu <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="2457b-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="2457b-296">Kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="2457b-296">Health Checks</span></span>

<span data-ttu-id="2457b-297">Kontroly stavu používají směrování koncových bodů u obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="2457b-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="2457b-298">V `Startup.Configure` nástroji zavolejte `MapHealthChecks` na tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="2457b-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="2457b-299">Koncové body kontrol stavu můžou:</span><span class="sxs-lookup"><span data-stu-id="2457b-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="2457b-300">Zadejte minimálně jednoho povoleného hostitele nebo portů.</span><span class="sxs-lookup"><span data-stu-id="2457b-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="2457b-301">Vyžadovat autorizaci.</span><span class="sxs-lookup"><span data-stu-id="2457b-301">Require authorization.</span></span>
* <span data-ttu-id="2457b-302">Vyžadovat CORS</span><span class="sxs-lookup"><span data-stu-id="2457b-302">Require CORS.</span></span>

<span data-ttu-id="2457b-303">Další informace najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="2457b-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="2457b-304">Kanály na HttpContext</span><span class="sxs-lookup"><span data-stu-id="2457b-304">Pipes on HttpContext</span></span>

<span data-ttu-id="2457b-305">Nyní je možné číst text žádosti a zapsat tělo odpovědi pomocí <xref:System.IO.Pipelines> rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2457b-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="2457b-306">Rozhraní</span><span class="sxs-lookup"><span data-stu-id="2457b-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="2457b-307">`HttpRequest.BodyReader`vlastnost poskytuje objekt <xref:System.IO.Pipelines.PipeReader> , který lze použít ke čtení textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="2457b-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="2457b-308">Rozhraní</span><span class="sxs-lookup"><span data-stu-id="2457b-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="2457b-309">`HttpResponse.BodyWriter`vlastnost poskytuje <xref:System.IO.Pipelines.PipeWriter> , kterou lze použít k zápisu textu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2457b-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="2457b-310">`HttpRequest.BodyReader`je analogkou `HttpRequest.Body` datového proudu.</span><span class="sxs-lookup"><span data-stu-id="2457b-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="2457b-311">`HttpResponse.BodyWriter`je analogkou `HttpResponse.Body` datového proudu.</span><span class="sxs-lookup"><span data-stu-id="2457b-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="2457b-312">Vylepšené zasílání zpráv o chybách ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="2457b-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="2457b-313">Chyby při spuštění při hostování ASP.NET Corech aplikací ve službě IIS teď vytváří rozsáhlejší diagnostická data.</span><span class="sxs-lookup"><span data-stu-id="2457b-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="2457b-314">Tyto chyby jsou hlášeny do protokolu událostí systému Windows s trasováním zásobníku, kdykoli je to možné.</span><span class="sxs-lookup"><span data-stu-id="2457b-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="2457b-315">Kromě toho jsou všechna upozornění, chyby a neošetřené výjimky protokolovány do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="2457b-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="2457b-316">Služba pracovních procesů a sada SDK pracovních procesů</span><span class="sxs-lookup"><span data-stu-id="2457b-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="2457b-317">.NET Core 3,0 zavádí novou šablonu aplikace služby pracovního procesu.</span><span class="sxs-lookup"><span data-stu-id="2457b-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="2457b-318">Tato šablona poskytuje výchozí bod pro psaní dlouhých služeb, které běží v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2457b-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="2457b-319">Další informace:</span><span class="sxs-lookup"><span data-stu-id="2457b-319">For more information, see:</span></span>

* [<span data-ttu-id="2457b-320">Pracovní procesy .NET Core jako služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="2457b-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="2457b-321">Vylepšení middlewaru u předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="2457b-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="2457b-322">V předchozích verzích ASP.NET Core volání <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> bylo problematické při nasazení do Azure Linux nebo za libovolný reverzní proxy server jiný než IIS.</span><span class="sxs-lookup"><span data-stu-id="2457b-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="2457b-323">Oprava pro předchozí verze je popsána v [části dopředného schématu pro reverzní proxy servery se systémy Linux a non-IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="2457b-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="2457b-324">Tento scénář je opravený v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2457b-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2457b-325">Hostitel povoluje [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) , pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true` .</span><span class="sxs-lookup"><span data-stu-id="2457b-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="2457b-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`je nastavené na `true` obrázky kontejnerů.</span><span class="sxs-lookup"><span data-stu-id="2457b-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="2457b-327">Vylepšení výkonu</span><span class="sxs-lookup"><span data-stu-id="2457b-327">Performance improvements</span></span>

<span data-ttu-id="2457b-328">ASP.NET Core 3,0 obsahuje mnoho vylepšení, která omezují využití paměti a zvyšují propustnost:</span><span class="sxs-lookup"><span data-stu-id="2457b-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="2457b-329">Snížení využití paměti při použití integrovaného kontejneru vkládání závislostí pro vymezené služby.</span><span class="sxs-lookup"><span data-stu-id="2457b-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="2457b-330">Snížení počtu přidělení napříč rámec, včetně scénářů middlewaru a směrování.</span><span class="sxs-lookup"><span data-stu-id="2457b-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="2457b-331">Snížení využití paměti pro připojení protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2457b-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="2457b-332">Snižování paměti a vylepšení propustnosti pro připojení HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2457b-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="2457b-333">Nový optimalizovaný a plně asynchronní serializátor JSON.</span><span class="sxs-lookup"><span data-stu-id="2457b-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="2457b-334">Snížení využití paměti a vylepšení propustnosti při analýze formuláře.</span><span class="sxs-lookup"><span data-stu-id="2457b-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="2457b-335">ASP.NET Core 3,0 běží pouze na .NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="2457b-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="2457b-336">Od ASP.NET Core 3,0 .NET Framework již není podporovanou cílovou architekturou.</span><span class="sxs-lookup"><span data-stu-id="2457b-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="2457b-337">Projekty cílené na .NET Framework můžou v plně podporovaném způsobem dál používat [LTS verzi pro .NET Core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="2457b-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="2457b-338">Většina balíčků s podporou ASP.NET Core 2.1. x bude po dobu tří let LTS pro .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="2457b-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="2457b-339">Informace o migraci najdete v tématu [portování kódu z .NET Framework do .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="2457b-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="2457b-340">Použití sdíleného rozhraní ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2457b-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="2457b-341">Sdílené rozhraní ASP.NET Core 3,0, obsažené v souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), již nevyžaduje explicitní `<PackageReference />` prvek v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="2457b-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="2457b-342">Sdílené rozhraní je automaticky odkazováno při použití `Microsoft.NET.Sdk.Web` sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="2457b-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="2457b-343">Sestavení odebraná z ASP.NET Core sdílené rozhraní</span><span class="sxs-lookup"><span data-stu-id="2457b-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="2457b-344">Nejvýznamnější sestavení odebraný ze sdílené architektury ASP.NET Core 3,0 jsou:</span><span class="sxs-lookup"><span data-stu-id="2457b-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="2457b-345">[Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.NET).</span><span class="sxs-lookup"><span data-stu-id="2457b-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="2457b-346">Pokud chcete přidat Json.NET do ASP.NET Core 3,0, přečtěte si téma [Přidání podpory formátu JSON založeného na Newtonsoft.Js](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="2457b-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="2457b-347">ASP.NET Core 3,0 zavádí `System.Text.Json` pro čtení a zápis formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="2457b-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="2457b-348">Další informace najdete v tématu [Nová serializace JSON](#new-json-serialization) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="2457b-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="2457b-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="2457b-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="2457b-350">Úplný seznam sestavení odebraných ze sdíleného rozhraní najdete v tématu sestavení, [která jsou odebírána z Microsoft. AspNetCore. App 3,0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="2457b-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="2457b-351">Další informace o motivaci této změny najdete v tématu přemístění [změn do Microsoft. AspNetCore. app v 3,0](https://github.com/aspnet/Announcements/issues/325) a [první pohled na změny, které přicházejí v ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="2457b-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 