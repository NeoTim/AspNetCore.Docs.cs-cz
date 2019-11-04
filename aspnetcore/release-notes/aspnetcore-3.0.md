---
title: Co je nového v ASP.NET Core 3,0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: aspnetcore-3.0
ms.openlocfilehash: 8c53d8a9fa222ca40f26dc713ec3b70ddde76539
ms.sourcegitcommit: eb2fe5ad2e82fab86ca952463af8d017ba659b25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416127"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="c7a6d-103">Co je nového v ASP.NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="c7a6d-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="c7a6d-104">Tento článek popisuje nejvýznamnější změny v ASP.NET Core 3,0 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## <a name="blazor"></a><span data-ttu-id="c7a6d-105">Blazor</span><span class="sxs-lookup"><span data-stu-id="c7a6d-105">Blazor</span></span>

<span data-ttu-id="c7a6d-106">Blazor je nová architektura v ASP.NET Core pro vytváření interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-106">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="c7a6d-107">Vytvářejte bohatě interaktivní uživatelská rozhraní C# pomocí místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="c7a6d-108">Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="c7a6d-109">Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="c7a6d-110">Scénáře podporované Blazor Framework:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-110">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="c7a6d-111">Opakovaně použitelné součásti uživatelského rozhraní (komponenty Razor)</span><span class="sxs-lookup"><span data-stu-id="c7a6d-111">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="c7a6d-112">Směrování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="c7a6d-112">Client-side routing</span></span>
* <span data-ttu-id="c7a6d-113">Rozložení komponent</span><span class="sxs-lookup"><span data-stu-id="c7a6d-113">Component layouts</span></span>
* <span data-ttu-id="c7a6d-114">Podpora pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="c7a6d-114">Support for dependency injection</span></span>
* <span data-ttu-id="c7a6d-115">Formuláře a ověřování</span><span class="sxs-lookup"><span data-stu-id="c7a6d-115">Forms and validation</span></span>
* <span data-ttu-id="c7a6d-116">Sestavení knihoven součástí s knihovnami tříd Razor</span><span class="sxs-lookup"><span data-stu-id="c7a6d-116">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="c7a6d-117">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="c7a6d-117">JavaScript interop</span></span>

<span data-ttu-id="c7a6d-118">Další informace najdete v tématu <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-118">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a><span data-ttu-id="c7a6d-119">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="c7a6d-119">Blazor Server</span></span>

<span data-ttu-id="c7a6d-120">Blazor odpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-120">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="c7a6d-121">Blazor Server poskytuje podporu pro hostování komponent Razor na serveru v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-121">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="c7a6d-122">Aktualizace uživatelského rozhraní se zpracovávají přes připojení k signalizaci.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-122">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="c7a6d-123">Blazor Server je podporován v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-123">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a><span data-ttu-id="c7a6d-124">Blazor WebAssembly (Preview)</span><span class="sxs-lookup"><span data-stu-id="c7a6d-124">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="c7a6d-125">Aplikace Blazor se dají spustit také přímo v prohlížeči pomocí technologie .NET runtime založené na WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-125">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="c7a6d-126">Blazor WebAssembly je ve verzi Preview a *není* podporované v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-126">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="c7a6d-127">Blazor WebAssembly bude podporováno v budoucí verzi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-127">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="c7a6d-128">Komponenty Razor</span><span class="sxs-lookup"><span data-stu-id="c7a6d-128">Razor components</span></span>

<span data-ttu-id="c7a6d-129">Aplikace Blazor jsou sestavené z komponent.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-129">Blazor apps are built from components.</span></span> <span data-ttu-id="c7a6d-130">Komponenty jsou samostatné bloky uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-130">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="c7a6d-131">Komponenty jsou normální třídy .NET, které definují logiku vykreslování uživatelského rozhraní a obslužné rutiny událostí na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-131">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="c7a6d-132">Můžete vytvářet bohatě interaktivní webové aplikace bez JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-132">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="c7a6d-133">Komponenty v Blazor jsou obvykle vytvářeny pomocí syntaxe Razor, což je přirozená směs HTML C#a.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-133">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="c7a6d-134">Komponenty Razor jsou podobné zobrazením Razor Pages a MVC v tom, že používají syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-134">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="c7a6d-135">Na rozdíl od stránek a zobrazení, které jsou založeny na modelu požadavek-odpověď, se komponenty používají konkrétně pro zpracování kompozice uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-135">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="c7a6d-136">gRPC</span><span class="sxs-lookup"><span data-stu-id="c7a6d-136">gRPC</span></span>

<span data-ttu-id="c7a6d-137">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="c7a6d-137">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="c7a6d-138">Je oblíbeným a vysoce výkonným rozhraním vzdáleného volání procedur (RPC).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-138">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="c7a6d-139">Nabízí dogmatickýmý přístup k vývoji rozhraní API, který je prvním kontraktem.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-139">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="c7a6d-140">Používá moderní technologie, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-140">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="c7a6d-141">HTTP/2 pro přenos.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-141">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="c7a6d-142">Vyrovnávací paměti protokolu jako jazyk popisu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-142">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="c7a6d-143">Formát binární serializace.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-143">Binary serialization format.</span></span>
* <span data-ttu-id="c7a6d-144">Poskytuje funkce, jako například:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-144">Provides features such as:</span></span>

  * <span data-ttu-id="c7a6d-145">Ověřování</span><span class="sxs-lookup"><span data-stu-id="c7a6d-145">Authentication</span></span>
  * <span data-ttu-id="c7a6d-146">Obousměrné streamování a řízení toku.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-146">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="c7a6d-147">Zrušení a vypršení časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-147">Cancellation and timeouts.</span></span>

<span data-ttu-id="c7a6d-148">funkce gRPC v ASP.NET Core 3,0 obsahuje:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-148">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="c7a6d-149">[Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; ASP.NET Core Framework pro hostování služeb Grpc Services.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-149">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="c7a6d-150">gRPC se na ASP.NET Core integruje se standardními ASP.NET Core funkcemi, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-150">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="c7a6d-151">[Grpc .NET. client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; klienta Grpc pro .NET Core, který se vytváří na známých `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-151">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="c7a6d-152">[Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; integrace klienta Grpc s `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-152">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="c7a6d-153">Další informace najdete v tématu <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-153">For more information, see <xref:grpc/index>.</span></span>

## <a name="signalr"></a><span data-ttu-id="c7a6d-154">SignalR</span><span class="sxs-lookup"><span data-stu-id="c7a6d-154">SignalR</span></span>

<span data-ttu-id="c7a6d-155">Pokyny k migraci najdete v tématu [aktualizace kódu signálu](xref:migration/22-to-30#signalr) .</span><span class="sxs-lookup"><span data-stu-id="c7a6d-155">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="c7a6d-156">Nástroj Signal nyní používá `System.Text.Json` k serializaci nebo deserializaci zpráv JSON.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-156">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="c7a6d-157">Pokyny pro obnovení serializátoru založeného na `Newtonsoft.Json`najdete v tématu [Přepnutí do Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) .</span><span class="sxs-lookup"><span data-stu-id="c7a6d-157">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="c7a6d-158">V jazyce JavaScript a v klientech rozhraní .NET pro signalizaci byla přidána podpora pro automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-158">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="c7a6d-159">Ve výchozím nastavení se klient pokusí znovu připojit hned a v případě potřeby opakovat po 2, 10 a 30 sekundách.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-159">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="c7a6d-160">Pokud se klient úspěšně znovu připojí, obdrží nové ID připojení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-160">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="c7a6d-161">Automatické opětovné připojení je výslovný souhlas:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-161">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="c7a6d-162">Intervaly opětovného připojení lze zadat předáním pole doby trvání založené na milisekundách:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-162">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="c7a6d-163">Vlastní implementaci lze předat v rámci úplného řízení intervalů opětovného připojení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-163">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="c7a6d-164">Pokud se opětovné připojení nezdařilo po intervalu posledního opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-164">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="c7a6d-165">Klient považuje připojení za offline.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-165">The client considers the connection is offline.</span></span>
* <span data-ttu-id="c7a6d-166">Klient se ukončí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-166">The client stops trying to reconnect.</span></span>

<span data-ttu-id="c7a6d-167">Během opakovaného pokusu o připojení aktualizujte uživatelské rozhraní aplikace a upozorněte uživatele, že probíhá pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-167">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="c7a6d-168">Pokud chcete poskytnout zpětnou vazbu uživatelského rozhraní při přerušení připojení, rozhraní API pro klienta signalizace bylo rozbaleno, aby zahrnovalo následující obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-168">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="c7a6d-169">`onreconnecting`: dává vývojářům možnost zakázat uživatelské rozhraní nebo upozornit uživatele na to, že aplikace je offline.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-169">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="c7a6d-170">`onreconnected`: dává vývojářům možnost aktualizovat uživatelské rozhraní po opětovném vytvoření připojení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-170">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="c7a6d-171">Následující kód používá `onreconnecting` k aktualizaci uživatelského rozhraní při pokusu o připojení:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-171">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="c7a6d-172">Následující kód používá `onreconnected` k aktualizaci uživatelského rozhraní při připojení:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-172">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="c7a6d-173">Návěstí 3,0 a novější poskytuje vlastní prostředek pro obslužné rutiny autorizace, pokud metoda rozbočovače vyžaduje autorizaci.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-173">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="c7a6d-174">Prostředek je instance `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-174">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="c7a6d-175">`HubInvocationContext` obsahuje:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-175">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="c7a6d-176">Název vyvolané metody centra</span><span class="sxs-lookup"><span data-stu-id="c7a6d-176">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="c7a6d-177">Argumenty metody hub.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-177">Arguments to the hub method.</span></span>

<span data-ttu-id="c7a6d-178">Vezměte v úvahu následující příklad aplikace chatovací místnosti umožňující přihlášení více organizací prostřednictvím Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-178">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="c7a6d-179">Kdokoli s účet Microsoft se může přihlásit ke konverzaci, ale jenom členové vlastnící organizace můžou zakázat nebo zobrazit historie chatu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users’ chat histories.</span></span> <span data-ttu-id="c7a6d-180">Aplikace může omezit určité funkce pro konkrétní uživatele.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-180">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="c7a6d-181">V předchozím kódu `DomainRestrictedRequirement` slouží jako vlastní `IAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-181">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="c7a6d-182">Vzhledem k tomu, že se předává parametr prostředku `HubInvocationContext`, interní logika může:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-182">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="c7a6d-183">Zkontrolujte kontext, ve kterém se centrum volá.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-183">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="c7a6d-184">Rozhodnutí, jak umožnit uživateli spouštět jednotlivé metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-184">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="c7a6d-185">Jednotlivé metody rozbočovače je možné dekorovat pomocí názvů zásad, které kontrolují kód v době běhu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-185">Individual Hub methods can be decorated with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="c7a6d-186">Když se klienti pokusí zavolat jednotlivé metody rozbočovače, spustí se obslužná rutina `DomainRestrictedRequirement` a řídí přístup k metodám.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-186">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="c7a6d-187">Na základě toho, jak `DomainRestrictedRequirement` řídí přístup:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-187">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="c7a6d-188">Všichni přihlášení uživatelé mohou volat metodu `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-188">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="c7a6d-189">Historie uživatelů můžou zobrazit jenom uživatelé, kteří se přihlásili pomocí `@jabbr.net` e-mailové adresy.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-189">Only users who have logged in with a `@jabbr.net` email address can view users’ histories.</span></span>
* <span data-ttu-id="c7a6d-190">Jenom `bob42@jabbr.net` můžou zakázat uživatele z konverzační místnosti.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-190">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="c7a6d-191">Vytváření zásad `DomainRestricted` může zahrnovat:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-191">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="c7a6d-192">V *Startup.cs*přidejte novou zásadu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-192">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="c7a6d-193">Zadejte vlastní požadavek `DomainRestrictedRequirement` jako parametr.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-193">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="c7a6d-194">Probíhá registrace `DomainRestricted` pomocí middleware autorizace.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-194">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

<span data-ttu-id="c7a6d-195">Centra signalizace používají [Směrování koncového bodu](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-195">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="c7a6d-196">Připojení ke službě Signal hub bylo dříve provedeno explicitně:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-196">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="c7a6d-197">V předchozí verzi si vývojáři potřebovali k nastavování řadičů na různých místech vytvořit řadiče, stránky Razor a centra.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-197">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="c7a6d-198">Explicitní výsledky připojení v řadě téměř stejných segmentů směrování:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-198">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

<span data-ttu-id="c7a6d-199">Centra signálu 3,0 se dají směrovat přes směrování koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-199">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="c7a6d-200">Se směrováním koncových bodů se obvykle dá nakonfigurovat všechny směrování v `UseRouting`:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-200">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="c7a6d-201">Byl přidán signál ASP.NET Core 3,0:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-201">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="c7a6d-202">Streamování mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-202">Client-to-server streaming.</span></span> <span data-ttu-id="c7a6d-203">Pomocí služby streamování klienta na server mohou metody na straně serveru přebírat instance `IAsyncEnumerable<T>` nebo `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-203">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="c7a6d-204">V následující C# ukázce obdrží metoda `UploadStream` v centru datový proud řetězců z klienta:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-204">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="c7a6d-205">Klientské aplikace .NET můžou předat instanci `IAsyncEnumerable<T>` nebo `ChannelReader<T>` jako argument `stream` metody `UploadStream` hub výše.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-205">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="c7a6d-206">Po dokončení smyčky `for` a ukončení místní funkce se pošle dokončování datového proudu:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-206">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="c7a6d-207">Klientské aplikace JavaScriptu používají `Subject`er (nebo [Předmět RxJS](https://rxjs.dev/api/index/class/Subject)) pro `stream` argumentu metody centra `UploadStream` výše.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-207">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="c7a6d-208">Kód jazyka JavaScript může použít metodu `subject.next` ke zpracování řetězců při jejich zachycení a připravení na odeslání na server.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-208">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="c7a6d-209">Pomocí kódu, například dvou předchozích fragmentů, lze vytvořit prostředí streamování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-209">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="c7a6d-210">Nová serializace JSON</span><span class="sxs-lookup"><span data-stu-id="c7a6d-210">New JSON serialization</span></span>

<span data-ttu-id="c7a6d-211">ASP.NET Core 3,0 teď ve výchozím nastavení používá <xref:System.Text.Json> pro serializaci JSON:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-211">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="c7a6d-212">Čte a zapisuje JSON asynchronně.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-212">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="c7a6d-213">Je optimalizován pro text v kódování UTF-8.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-213">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="c7a6d-214">Obvykle vyšší výkon než `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-214">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="c7a6d-215">Pokud chcete přidat Json.NET do ASP.NET Core 3,0, přečtěte si téma [Přidání podpory formátu JSON založeného na Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-215">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="c7a6d-216">Nové direktivy Razor</span><span class="sxs-lookup"><span data-stu-id="c7a6d-216">New Razor directives</span></span>

<span data-ttu-id="c7a6d-217">Následující seznam obsahuje nové direktivy Razor:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-217">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="c7a6d-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; direktiva `@attribute` aplikuje daný atribut na třídu generované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-218">[@attribute](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="c7a6d-219">Například `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-219">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="c7a6d-220">[@implements](xref:mvc/views/razor#implements) &ndash; direktiva `@implements` implementuje rozhraní pro generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-220">[@implements](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="c7a6d-221">Například `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-221">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="c7a6d-222">IdentityServer4 podporuje ověřování a autorizaci pro webová rozhraní API a jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-222">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="c7a6d-223">ASP.NET Core 3,0 nabízí ověřování v aplikacích s jednou stránkou (jednostránkové) s využitím podpory pro autorizaci webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-223">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="c7a6d-224">ASP.NET Coreá identita pro ověřování a ukládání uživatelů je kombinována s [IdentityServer4](https://identityserver.io/) pro implementaci otevřeného ID Connect.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-224">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="c7a6d-225">IdentityServer4 je rozhraní OpenID Connect a OAuth 2,0 Framework pro ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-225">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="c7a6d-226">Umožňuje následující funkce zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-226">It enables the following security features:</span></span>

* <span data-ttu-id="c7a6d-227">Ověřování jako služba (AaaS)</span><span class="sxs-lookup"><span data-stu-id="c7a6d-227">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="c7a6d-228">Jednotné přihlašování/vypínání (SSO) nad více typy aplikací</span><span class="sxs-lookup"><span data-stu-id="c7a6d-228">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="c7a6d-229">Řízení přístupu pro rozhraní API</span><span class="sxs-lookup"><span data-stu-id="c7a6d-229">Access control for APIs</span></span>
* <span data-ttu-id="c7a6d-230">Federační brána</span><span class="sxs-lookup"><span data-stu-id="c7a6d-230">Federation Gateway</span></span>

<span data-ttu-id="c7a6d-231">Další informace najdete v [dokumentaci k IdentityServer4 nebo v](http://docs.identityserver.io/en/latest/index.html) článku věnovaném [ověřování a autorizaci pro jednostránkové](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-231">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="c7a6d-232">Ověřování pomocí certifikátů a protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="c7a6d-232">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="c7a6d-233">Ověřování certifikátu vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-233">Certificate authentication requires:</span></span>

* <span data-ttu-id="c7a6d-234">Konfigurace serveru pro příjem certifikátů.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-234">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="c7a6d-235">Přidání middleware ověřování v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-235">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="c7a6d-236">Přidání služby ověřování certifikátů v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-236">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c7a6d-237">Možnosti ověřování certifikátů zahrnují možnost:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-237">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="c7a6d-238">Přijměte certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-238">Accept self-signed certificates.</span></span>
* <span data-ttu-id="c7a6d-239">Kontrolovat odvolání certifikátu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-239">Check for certificate revocation.</span></span>
* <span data-ttu-id="c7a6d-240">Ověřte, že certifikát proffered má v něm správné příznaky použití.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-240">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="c7a6d-241">Výchozí objekt zabezpečení uživatele je vytvořen z vlastností certifikátu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-241">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="c7a6d-242">Objekt zabezpečení uživatele obsahuje událost, která umožňuje doplňování nebo nahrazení objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-242">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="c7a6d-243">Další informace najdete v tématu <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-243">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="c7a6d-244">[Ověřování systému Windows](/windows-server/security/windows-authentication/windows-authentication-overview) bylo rozšířeno na Linux a MacOS.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-244">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="c7a6d-245">V předchozích verzích bylo ověřování systému Windows omezeno na [službu IIS](xref:host-and-deploy/iis/index) a [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-245">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="c7a6d-246">V ASP.NET Core 3,0 může [Kestrel](xref:fundamentals/servers/kestrel) používat funkci Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)a [NTLM v systémech Windows](/windows-server/security/kerberos/ntlm-overview), Linux a MacOS pro hostitele připojené k doméně systému Windows.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-246">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="c7a6d-247">Podporu Kestrel těchto schémat ověřování poskytuje balíček [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) .</span><span class="sxs-lookup"><span data-stu-id="c7a6d-247">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="c7a6d-248">Stejně jako u ostatních ověřovacích služeb nakonfigurujte aplikaci ověřování na šířku a pak nakonfigurujte službu:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-248">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="c7a6d-249">Požadavky na hostitele:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-249">Host requirements:</span></span>

* <span data-ttu-id="c7a6d-250">Hostitelé systému Windows musí mít k uživatelskému účtu, který je hostitelem aplikace, přidány [hlavní názvy služby](/windows/win32/ad/service-principal-names) (SPN).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-250">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="c7a6d-251">Počítače se systémem Linux a macOS musí být připojeny k doméně.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-251">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="c7a6d-252">Pro webový proces musí být vytvořeny hlavní názvy služby (SPN).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-252">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="c7a6d-253">Na hostitelském počítači musí být vygenerovány a nakonfigurovány [soubory keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) .</span><span class="sxs-lookup"><span data-stu-id="c7a6d-253">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="c7a6d-254">Další informace najdete v tématu <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-254">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="c7a6d-255">Změny šablony</span><span class="sxs-lookup"><span data-stu-id="c7a6d-255">Template changes</span></span>

<span data-ttu-id="c7a6d-256">Šablony webového uživatelského rozhraní (Razor Pages, MVC s kontrolérem a zobrazeními) mají odebraný následující:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-256">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="c7a6d-257">Uživatelské rozhraní pro vyjádření souhlasu souborů cookie již není zahrnuto.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-257">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="c7a6d-258">Pokud chcete povolit funkci vyjádření souhlasu souborů cookie v aplikaci vygenerovanou šablonou ASP.NET Core 3,0, přečtěte si téma <xref:security/gdpr>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-258">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="c7a6d-259">Skripty a související statické prostředky jsou nyní odkazovány jako místní soubory namísto použití sítě CDN.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-259">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="c7a6d-260">Další informace najdete v tématu [skripty a související statické prostředky jsou nyní odkazovány jako místní soubory namísto použití sítě CDN v závislosti na aktuálním prostředí (ASPNET/AspNetCore. Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-260">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="c7a6d-261">Úhlová šablona se aktualizovala tak, aby používala úhlové 8.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-261">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="c7a6d-262">Šablona knihovny tříd Razor (RCL) je ve výchozím nastavení standardně pro vývoj komponent Razor.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-262">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="c7a6d-263">Nová možnost šablony v aplikaci Visual Studio poskytuje podporu šablon pro stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-263">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="c7a6d-264">Při vytváření RCL ze šablony v příkazovém prostředí, předejte možnost `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-264">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="c7a6d-265">Obecný hostitel</span><span class="sxs-lookup"><span data-stu-id="c7a6d-265">Generic Host</span></span>

<span data-ttu-id="c7a6d-266">Šablony ASP.NET Core 3,0 používají <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-266">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="c7a6d-267">Předchozí verze použité <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-267">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="c7a6d-268">Použití obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) zajišťuje lepší integraci ASP.NET Corech aplikací s jinými serverovými scénáři, které nejsou specifické pro web.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-268">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="c7a6d-269">Další informace najdete v tématu [HostBuilder nahrazuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-269">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="c7a6d-270">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="c7a6d-270">Host configuration</span></span>

<span data-ttu-id="c7a6d-271">Před vydáním ASP.NET Core 3,0 byly načteny proměnné prostředí s předponou `ASPNETCORE_` pro konfiguraci hostitele webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-271">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="c7a6d-272">V 3,0 se `AddEnvironmentVariables` používá k načtení proměnných prostředí s předponou `DOTNET_` pro konfiguraci hostitele s `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-272">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="c7a6d-273">Změny při vkládání spouštěcích konstruktorů</span><span class="sxs-lookup"><span data-stu-id="c7a6d-273">Changes to Startup constructor injection</span></span>

<span data-ttu-id="c7a6d-274">Obecný hostitel podporuje pouze následující typy pro vkládání konstruktoru `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-274">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c7a6d-275">Všechny služby mohou být vloženy přímo jako argumenty `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-275">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="c7a6d-276">Další informace najdete v tématu [obecný hostitel omezuje úvodní vkládání spouštěcího konstruktoru (ASPNET/](https://github.com/aspnet/Announcements/issues/353)informers #353).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-276">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="c7a6d-277">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c7a6d-277">Kestrel</span></span>

* <span data-ttu-id="c7a6d-278">Konfigurace Kestrel byla aktualizována pro migraci na obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-278">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="c7a6d-279">V 3,0 je Kestrel nakonfigurovaný na tvůrci webového hostitele, který poskytuje `ConfigureWebHostDefaults`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-279">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="c7a6d-280">Připojovací adaptéry byly odebrány z Kestrel a nahrazeny middlewarem připojení, který je v kanálu ASP.NET Core podobný Middlewari HTTP, ale pro připojení nižší úrovně.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-280">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="c7a6d-281">Transportní vrstva Kestrel byla vystavena jako veřejné rozhraní v `Connections.Abstractions`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-281">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="c7a6d-282">Nejednoznačnost mezi záhlavími a přípojnou čárkou byla vyřešena přesunutím koncových hlaviček do nové kolekce.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-282">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="c7a6d-283">Synchronní vstupně-výstupní rozhraní API, jako je například `HttpRequest.Body.Read`, jsou běžným zdrojem vyčerpání vláken, který vede k chybám aplikace.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-283">Synchronous IO APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="c7a6d-284">V 3,0 je ve výchozím nastavení zakázáno `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-284">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="c7a6d-285">Další informace najdete v tématu <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-285">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="c7a6d-286">HTTP/2 povoleno ve výchozím nastavení</span><span class="sxs-lookup"><span data-stu-id="c7a6d-286">HTTP/2 enabled by default</span></span>

<span data-ttu-id="c7a6d-287">Protokol HTTP/2 je ve výchozím nastavení povolený pro koncové body HTTPS v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-287">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="c7a6d-288">Podpora HTTP/2 pro IIS nebo HTTP. sys je povolená, když operační systém podporuje.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-288">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="c7a6d-289">EventCounters na žádost</span><span class="sxs-lookup"><span data-stu-id="c7a6d-289">EventCounters on request</span></span>

<span data-ttu-id="c7a6d-290">Hostování EventSource, `Microsoft.AspNetCore.Hosting`, emituje následující nové typy <xref:System.Diagnostics.Tracing.EventCounter> související se vstupními požadavky:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-290">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="c7a6d-291">Směrování koncových bodů</span><span class="sxs-lookup"><span data-stu-id="c7a6d-291">Endpoint routing</span></span>

<span data-ttu-id="c7a6d-292">Směrování koncového bodu, které umožňuje architekturám (například MVC) pracovat dobře s middlewarem, je vylepšeno:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-292">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="c7a6d-293">Pořadí middlewaru a koncových bodů je možné nakonfigurovat v kanálu zpracování požadavků `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-293">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="c7a6d-294">Koncové body a middleware se dobře zakládají s dalšími ASP.NET Core technologiemi, jako jsou například kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-294">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="c7a6d-295">Koncové body můžou implementovat zásadu, třeba CORS nebo autorizaci, a to v middlewaru i MVC.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-295">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="c7a6d-296">Filtry a atributy lze umístit na metody v řadičích.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-296">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="c7a6d-297">Další informace najdete v tématu <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-297">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="c7a6d-298">Kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="c7a6d-298">Health Checks</span></span>

<span data-ttu-id="c7a6d-299">Kontroly stavu používají směrování koncových bodů u obecného hostitele.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-299">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="c7a6d-300">V `Startup.Configure` volejte `MapHealthChecks` na Tvůrci koncových bodů s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-300">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="c7a6d-301">Koncové body kontrol stavu můžou:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-301">Health Checks endpoints can:</span></span>

* <span data-ttu-id="c7a6d-302">Zadejte minimálně jednoho povoleného hostitele nebo portů.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-302">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="c7a6d-303">Vyžadovat autorizaci.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-303">Require authorization.</span></span>
* <span data-ttu-id="c7a6d-304">Vyžadovat CORS</span><span class="sxs-lookup"><span data-stu-id="c7a6d-304">Require CORS.</span></span>

<span data-ttu-id="c7a6d-305">Další informace najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-305">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="c7a6d-306">Kanály na HttpContext</span><span class="sxs-lookup"><span data-stu-id="c7a6d-306">Pipes on HttpContext</span></span>

<span data-ttu-id="c7a6d-307">Je teď možné číst text žádosti a zapsat tělo odpovědi pomocí rozhraní <xref:System.IO.Pipelines> API.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-307">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="c7a6d-308">Rozhraní</span><span class="sxs-lookup"><span data-stu-id="c7a6d-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="c7a6d-309">vlastnost `HttpRequest.BodyReader` poskytuje <xref:System.IO.Pipelines.PipeReader>, která se dá použít ke čtení textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-309">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="c7a6d-310">Rozhraní</span><span class="sxs-lookup"><span data-stu-id="c7a6d-310">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="c7a6d-311">vlastnost `HttpResponse.BodyWriter` poskytuje <xref:System.IO.Pipelines.PipeWriter>, která se dá použít k zápisu textu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-311">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="c7a6d-312">`HttpRequest.BodyReader` je analogkou datového proudu `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-312">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="c7a6d-313">`HttpResponse.BodyWriter` je analogkou datového proudu `HttpResponse.Body`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-313">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="c7a6d-314">Vylepšené zasílání zpráv o chybách ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="c7a6d-314">Improved error reporting in IIS</span></span>

<span data-ttu-id="c7a6d-315">Chyby při spuštění při hostování ASP.NET Corech aplikací ve službě IIS teď vytváří rozsáhlejší diagnostická data.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-315">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="c7a6d-316">Tyto chyby jsou hlášeny do protokolu událostí systému Windows s trasováním zásobníku, kdykoli je to možné.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-316">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="c7a6d-317">Kromě toho jsou všechna upozornění, chyby a neošetřené výjimky protokolovány do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-317">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="c7a6d-318">Služba pracovních procesů a sada SDK pracovních procesů</span><span class="sxs-lookup"><span data-stu-id="c7a6d-318">Worker Service and Worker SDK</span></span>

<span data-ttu-id="c7a6d-319">.NET Core 3,0 zavádí novou šablonu aplikace služby pracovního procesu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-319">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="c7a6d-320">Tato šablona poskytuje výchozí bod pro psaní dlouhých služeb, které běží v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-320">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="c7a6d-321">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-321">For more information, see:</span></span>

* [<span data-ttu-id="c7a6d-322">Pracovní procesy .NET Core jako služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="c7a6d-322">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="c7a6d-323">Vylepšení middlewaru u předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="c7a6d-323">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="c7a6d-324">V předchozích verzích ASP.NET Core bylo volání <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> problematické při nasazení do systému Azure Linux nebo za jakékoli reverzní proxy jiné než IIS.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-324">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="c7a6d-325">Oprava pro předchozí verze je popsána v [části dopředného schématu pro reverzní proxy servery se systémy Linux a non-IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-325">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="c7a6d-326">Tento scénář je opravený v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-326">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="c7a6d-327">Hostitel povolí [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) , když je proměnná prostředí `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavená na `true`.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-327">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="c7a6d-328">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` je nastavené na `true` v našich obrázcích kontejneru.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-328">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="c7a6d-329">Vylepšení výkonu</span><span class="sxs-lookup"><span data-stu-id="c7a6d-329">Performance improvements</span></span>

<span data-ttu-id="c7a6d-330">ASP.NET Core 3,0 obsahuje mnoho vylepšení, která omezují využití paměti a zvyšují propustnost:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-330">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="c7a6d-331">Snížení využití paměti při použití integrovaného kontejneru vkládání závislostí pro vymezené služby.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-331">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="c7a6d-332">Snížení počtu přidělení napříč rámec, včetně scénářů middlewaru a směrování.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-332">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="c7a6d-333">Snížení využití paměti pro připojení protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-333">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="c7a6d-334">Snižování paměti a vylepšení propustnosti pro připojení HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-334">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="c7a6d-335">Nový optimalizovaný a plně asynchronní serializátor JSON.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-335">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="c7a6d-336">Snížení využití paměti a vylepšení propustnosti při analýze formuláře.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-336">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="c7a6d-337">ASP.NET Core 3,0 běží pouze na .NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="c7a6d-337">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="c7a6d-338">Od ASP.NET Core 3,0 .NET Framework již není podporovanou cílovou architekturou.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-338">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="c7a6d-339">Projekty cílené na .NET Framework můžou v plně podporovaném způsobem dál používat [LTS verzi pro .NET Core 2,1](https://www.microsoft.com/net/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-339">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://www.microsoft.com/net/download/dotnet-core/2.1).</span></span> <span data-ttu-id="c7a6d-340">Většina balíčků s podporou ASP.NET Core 2.1. x bude po dobu tří let LTS pro .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-340">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="c7a6d-341">Informace o migraci najdete v tématu [portování kódu z .NET Framework do .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-341">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="c7a6d-342">Použití sdíleného rozhraní ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7a6d-342">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="c7a6d-343">Sdílené rozhraní ASP.NET Core 3,0, obsažené v souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), již nevyžaduje explicitní `<PackageReference />` prvek v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-343">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="c7a6d-344">Na sdílené rozhraní se automaticky odkazuje při použití sady `Microsoft.NET.Sdk.Web` SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-344">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="c7a6d-345">Sestavení odebraná z ASP.NET Core sdílené rozhraní</span><span class="sxs-lookup"><span data-stu-id="c7a6d-345">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="c7a6d-346">Nejvýznamnější sestavení odebraný ze sdílené architektury ASP.NET Core 3,0 jsou:</span><span class="sxs-lookup"><span data-stu-id="c7a6d-346">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="c7a6d-347">[Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.NET).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-347">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="c7a6d-348">Pokud chcete přidat Json.NET do ASP.NET Core 3,0, přečtěte si téma [Přidání podpory formátu JSON založeného na Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-348">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="c7a6d-349">ASP.NET Core 3,0 zavádí `System.Text.Json` pro čtení a zápis JSON.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-349">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="c7a6d-350">Další informace najdete v tématu [Nová serializace JSON](#new-json-serialization) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="c7a6d-350">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="c7a6d-351">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c7a6d-351">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="c7a6d-352">Úplný seznam sestavení odebraných ze sdíleného rozhraní najdete v tématu sestavení, [která jsou odebírána z Microsoft. AspNetCore. App 3,0](https://github.com/aspnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-352">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/aspnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="c7a6d-353">Další informace o motivaci této změny najdete v tématu přemístění [změn do Microsoft. AspNetCore. app v 3,0](https://github.com/aspnet/Announcements/issues/325) a [první pohled na změny, které přicházejí v ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="c7a6d-353">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
