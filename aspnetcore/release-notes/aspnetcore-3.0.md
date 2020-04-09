---
title: Co je nového v ASP.NET Core 3.0
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976973"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="3b93f-103">Co je nového v ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="3b93f-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="3b93f-104">Tento článek upozorňuje na nejvýznamnější změny v ASP.NET Jádrem 3.0 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="3b93f-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="3b93f-105">je nový framework v ASP.NET Core pro vytváření interaktivního webového uživatelského rozhraní na straně klienta s rozhraním .NET:</span><span class="sxs-lookup"><span data-stu-id="3b93f-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="3b93f-106">Vytvořte bohaté interaktivní umělá použití jazyka C# místo JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="3b93f-107">Sdílejte logiku aplikace na straně serveru a klienta napsanou v rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="3b93f-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="3b93f-108">Vykreslete ui jako HTML a CSS pro širokou podporu prohlížeče, včetně mobilních prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="3b93f-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="3b93f-109">rámcových scénářů:</span><span class="sxs-lookup"><span data-stu-id="3b93f-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="3b93f-110">Opakovaně použitelné součásti ui (komponenty Razor)</span><span class="sxs-lookup"><span data-stu-id="3b93f-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="3b93f-111">Směrování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="3b93f-111">Client-side routing</span></span>
* <span data-ttu-id="3b93f-112">Rozložení komponent</span><span class="sxs-lookup"><span data-stu-id="3b93f-112">Component layouts</span></span>
* <span data-ttu-id="3b93f-113">Podpora vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="3b93f-113">Support for dependency injection</span></span>
* <span data-ttu-id="3b93f-114">Formuláře a ověřování</span><span class="sxs-lookup"><span data-stu-id="3b93f-114">Forms and validation</span></span>
* <span data-ttu-id="3b93f-115">Vytváření knihoven komponent pomocí knihoven tříd Razor</span><span class="sxs-lookup"><span data-stu-id="3b93f-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="3b93f-116">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="3b93f-116">JavaScript interop</span></span>

<span data-ttu-id="3b93f-117">Další informace naleznete v tématu <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="3b93f-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="3b93f-118">Server</span><span class="sxs-lookup"><span data-stu-id="3b93f-118"> Server</span></span>

Blazor<span data-ttu-id="3b93f-119">odděluje logiku vykreslování komponent od způsobu použití aktualizací ui.</span><span class="sxs-lookup"><span data-stu-id="3b93f-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="3b93f-120">Server poskytuje podporu pro hostování komponent Razor na serveru v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b93f-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="3b93f-121">Aktualizace ui jsou SignalR zpracovávány přes připojení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="3b93f-122">Server je podporován v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b93f-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="opno-locblazor-webassembly-preview"></a>Blazor<span data-ttu-id="3b93f-123">Webassembly (náhled)</span><span class="sxs-lookup"><span data-stu-id="3b93f-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="3b93f-124">aplikace lze také spouštět přímo v prohlížeči pomocí runtime rozhraní .NET založeného na webové sestavě.</span><span class="sxs-lookup"><span data-stu-id="3b93f-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="3b93f-125">WebAssembly je ve verzi Preview a *není* podporován a není podporován v ASP.NET Jádrem 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b93f-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="3b93f-126">WebAssembly bude podporovánv budoucí verzi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b93f-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="3b93f-127">Komponenty pro břitvy</span><span class="sxs-lookup"><span data-stu-id="3b93f-127">Razor components</span></span>

Blazor<span data-ttu-id="3b93f-128">aplikace jsou sestaveny z komponent.</span><span class="sxs-lookup"><span data-stu-id="3b93f-128"> apps are built from components.</span></span> <span data-ttu-id="3b93f-129">Součásti jsou samostatné bloky uživatelského rozhraní ( UI), například stránka, dialog nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="3b93f-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="3b93f-130">Součásti jsou normální třídy .NET, které definují logiku vykreslování rozhraní a obslužné rutiny událostí na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="3b93f-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="3b93f-131">Bohaté interaktivní webové aplikace můžete vytvářet bez JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="3b93f-132">Komponenty Blazor v jsou obvykle vytvářeny pomocí syntaxe Razor, přirozené směsi HTML a C#.</span><span class="sxs-lookup"><span data-stu-id="3b93f-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="3b93f-133">Razor komponenty jsou podobné Razor Stránky a MVC zobrazení v tom, že oba používají Razor.</span><span class="sxs-lookup"><span data-stu-id="3b93f-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="3b93f-134">Na rozdíl od stránek a zobrazení, které jsou založeny na modelu požadavku odpověď, komponenty se používají speciálně pro zpracování složení uživatelského panelu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="3b93f-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="3b93f-135">gRPC</span></span>

<span data-ttu-id="3b93f-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="3b93f-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="3b93f-137">Je populární, vysoce výkonné RPC (vzdálené volání procedur) framework.</span><span class="sxs-lookup"><span data-stu-id="3b93f-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="3b93f-138">Nabízí umíněný přístup k vývoji rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b93f-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="3b93f-139">Využívá moderní technologie, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="3b93f-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="3b93f-140">HTTP/2 pro přepravu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="3b93f-141">Vyrovnávací paměti protokolu jako jazyk popisu rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3b93f-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="3b93f-142">Binární formát serializace.</span><span class="sxs-lookup"><span data-stu-id="3b93f-142">Binary serialization format.</span></span>
* <span data-ttu-id="3b93f-143">Poskytuje funkce, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="3b93f-143">Provides features such as:</span></span>

  * <span data-ttu-id="3b93f-144">Authentication</span><span class="sxs-lookup"><span data-stu-id="3b93f-144">Authentication</span></span>
  * <span data-ttu-id="3b93f-145">Obousměrné streamování a řízení toku.</span><span class="sxs-lookup"><span data-stu-id="3b93f-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="3b93f-146">Zrušení a časové lhůty.</span><span class="sxs-lookup"><span data-stu-id="3b93f-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="3b93f-147">funkce gRPC v ASP.NET Core 3.0 obsahuje:</span><span class="sxs-lookup"><span data-stu-id="3b93f-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="3b93f-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; ASP.NET Core framework pro hostování služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b93f-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="3b93f-149">gRPC na ASP.NET Core se integruje se standardními funkcemi ASP.NET Core, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="3b93f-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="3b93f-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Klient gRPC pro .NET Core, `HttpClient`který staví na známém .</span><span class="sxs-lookup"><span data-stu-id="3b93f-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="3b93f-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC integrace `HttpClientFactory`klienta s .</span><span class="sxs-lookup"><span data-stu-id="3b93f-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="3b93f-152">Další informace naleznete v tématu <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="3b93f-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="3b93f-153">Pokyny [ SignalR k migraci najdete v tématu Update code.](xref:migration/22-to-30#signalr)</span><span class="sxs-lookup"><span data-stu-id="3b93f-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="3b93f-154">Nyní `System.Text.Json` používá serializovat/rekonstruovat zprávy JSON.</span><span class="sxs-lookup"><span data-stu-id="3b93f-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="3b93f-155">Pokyny [k](xref:migration/22-to-30#switch-to-newtonsoftjson) obnovení serializátoru `Newtonsoft.Json`na bázi č.</span><span class="sxs-lookup"><span data-stu-id="3b93f-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="3b93f-156">V jazyce JavaScript a SignalRklienti .NET pro byla přidána podpora pro automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="3b93f-157">Ve výchozím nastavení se klient pokusí okamžitě znovu připojit a v případě potřeby to zkusit po 2, 10 a 30 sekundách.</span><span class="sxs-lookup"><span data-stu-id="3b93f-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="3b93f-158">Pokud se klient úspěšně znovu připojí, obdrží nové ID připojení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="3b93f-159">Automatické opětovné připojení je opt-in:</span><span class="sxs-lookup"><span data-stu-id="3b93f-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="3b93f-160">Intervaly opětovného připojení lze zadat předáním pole milisekundových dob trvání:</span><span class="sxs-lookup"><span data-stu-id="3b93f-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="3b93f-161">Vlastní implementace může být předána pro úplnou kontrolu intervalů opětovného připojení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="3b93f-162">Pokud se opětovné připojení nezdaří po posledním intervalu opětovného připojení:</span><span class="sxs-lookup"><span data-stu-id="3b93f-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="3b93f-163">Klient se domnívá, že připojení je offline.</span><span class="sxs-lookup"><span data-stu-id="3b93f-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="3b93f-164">Klient se přestane pokoušet o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="3b93f-165">Během pokusů o opětovné připojení aktualizujte uživatelské rozhraní aplikace a upozorněte uživatele, že se pokouší o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="3b93f-166">Chcete-li poskytnout zpětnou vazbu SignalR uživatelského rozhraní při přerušení připojení, klientské rozhraní API bylo rozšířeno tak, aby zahrnovalo následující obslužné rutiny událostí:</span><span class="sxs-lookup"><span data-stu-id="3b93f-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="3b93f-167">`onreconnecting`: Poskytuje vývojářům možnost zakázat uživatelské rozhraní nebo dát uživatelům vědět, že aplikace je offline.</span><span class="sxs-lookup"><span data-stu-id="3b93f-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="3b93f-168">`onreconnected`: Poskytuje vývojářům možnost aktualizovat ui po obnovení připojení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="3b93f-169">Následující kód `onreconnecting` používá k aktualizaci ui při pokusu o připojení:</span><span class="sxs-lookup"><span data-stu-id="3b93f-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="3b93f-170">Následující kód `onreconnected` používá k aktualizaci uI na připojení:</span><span class="sxs-lookup"><span data-stu-id="3b93f-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="3b93f-171">3.0 a novější poskytuje vlastní prostředek pro obslužné rutiny autorizace, když metoda rozbočovače vyžaduje autorizaci.</span><span class="sxs-lookup"><span data-stu-id="3b93f-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="3b93f-172">Prostředek je instancí třídy `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="3b93f-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="3b93f-173">Zahrnuje: `HubInvocationContext`</span><span class="sxs-lookup"><span data-stu-id="3b93f-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="3b93f-174">Název volané metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="3b93f-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="3b93f-175">Argumenty metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="3b93f-175">Arguments to the hub method.</span></span>

<span data-ttu-id="3b93f-176">Vezměme si následující příklad aplikace chatovací místnosti, která umožňuje více organizací přihlášení prostřednictvím služby Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="3b93f-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="3b93f-177">Kdokoli s účtem Microsoft se může přihlásit k chatu, ale pouze členové vlastnící organizace mohou zakázat uživatelům nebo zobrazit historii chatu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="3b93f-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="3b93f-178">Aplikace může omezit určité funkce od konkrétních uživatelů.</span><span class="sxs-lookup"><span data-stu-id="3b93f-178">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="3b93f-179">V předchozím kódu `DomainRestrictedRequirement` slouží jako `IAuthorizationRequirement`vlastní .</span><span class="sxs-lookup"><span data-stu-id="3b93f-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="3b93f-180">Vzhledem `HubInvocationContext` k tomu, že parametr prostředku je předáván, vnitřní logika může:</span><span class="sxs-lookup"><span data-stu-id="3b93f-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="3b93f-181">Zkontrolujte kontext, ve kterém je volána hub.</span><span class="sxs-lookup"><span data-stu-id="3b93f-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="3b93f-182">Rozhoduj se o povolení uživatele provádět jednotlivé metody hubu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="3b93f-183">Jednotlivé metody rozbočovače mohou být označeny názvem zásady, kterou kód kontroluje za běhu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="3b93f-184">Jako klienti se pokoušejí volat `DomainRestrictedRequirement` jednotlivé metody Hub, obslužná rutina běží a řídí přístup k metodám.</span><span class="sxs-lookup"><span data-stu-id="3b93f-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="3b93f-185">Na základě způsobu `DomainRestrictedRequirement` přístupu k ovládacím prvkům:</span><span class="sxs-lookup"><span data-stu-id="3b93f-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="3b93f-186">Metodu `SendMessage` mohou volat všichni přihlášení uživatelé.</span><span class="sxs-lookup"><span data-stu-id="3b93f-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="3b93f-187">Historii uživatelů mohou zobrazit pouze `@jabbr.net` uživatelé, kteří se přihlásili pomocí e-mailové adresy.</span><span class="sxs-lookup"><span data-stu-id="3b93f-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="3b93f-188">Pouze `bob42@jabbr.net` může zakázat uživatelům z chatovací místnosti.</span><span class="sxs-lookup"><span data-stu-id="3b93f-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="3b93f-189">Vytvoření `DomainRestricted` zásady může zahrnovat:</span><span class="sxs-lookup"><span data-stu-id="3b93f-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="3b93f-190">V *Startup.cs*přidání nové zásady.</span><span class="sxs-lookup"><span data-stu-id="3b93f-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="3b93f-191">Zadejte `DomainRestrictedRequirement` vlastní požadavek jako parametr.</span><span class="sxs-lookup"><span data-stu-id="3b93f-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="3b93f-192">Registrace `DomainRestricted` pomocí autorizačního middlewaru.</span><span class="sxs-lookup"><span data-stu-id="3b93f-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

SignalR<span data-ttu-id="3b93f-193">rozbočovače používají [směrování koncových bodů](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="3b93f-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="3b93f-194">připojení k rozbočovači bylo dříve provedeno explicitně:</span><span class="sxs-lookup"><span data-stu-id="3b93f-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="3b93f-195">V předchozí verzi vývojáři potřebovali drátovat řadiče, stránky Razor a rozbočovače na různých místech.</span><span class="sxs-lookup"><span data-stu-id="3b93f-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="3b93f-196">Explicitní připojení má za následek řadu téměř identických segmentů směrování:</span><span class="sxs-lookup"><span data-stu-id="3b93f-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

SignalR<span data-ttu-id="3b93f-197">Rozbočovače 3.0 lze směrovat prostřednictvím směrování koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="3b93f-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="3b93f-198">Při směrování koncových bodů lze obvykle `UseRouting`nakonfigurovat všechny směrování v :</span><span class="sxs-lookup"><span data-stu-id="3b93f-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="3b93f-199">ASP.NET Core 3.0 SignalR přidáno:</span><span class="sxs-lookup"><span data-stu-id="3b93f-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="3b93f-200">Streamování klient-server.</span><span class="sxs-lookup"><span data-stu-id="3b93f-200">Client-to-server streaming.</span></span> <span data-ttu-id="3b93f-201">Při streamování klient-server mohou metody na straně serveru trvat instance buď nebo `IAsyncEnumerable<T>` . `ChannelReader<T>`</span><span class="sxs-lookup"><span data-stu-id="3b93f-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="3b93f-202">V následující ukázce `UploadStream` jazyka C# metoda v centru obdrží datový proud řetězců z klienta:</span><span class="sxs-lookup"><span data-stu-id="3b93f-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="3b93f-203">Klientské aplikace .NET `IAsyncEnumerable<T>` mohou `ChannelReader<T>` předat `stream` buď nebo `UploadStream` instanci jako argument výše uvedené metody Hub.</span><span class="sxs-lookup"><span data-stu-id="3b93f-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="3b93f-204">Po `for` dokončení smyčky a ukončení místní funkce je odesláno dokončení datového proudu:</span><span class="sxs-lookup"><span data-stu-id="3b93f-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="3b93f-205">Klientské aplikace JavaScriptu SignalR `Subject` používají (nebo [Předmět RxJS)](https://rxjs.dev/api/index/class/Subject)pro `stream` argument výše uvedené metody `UploadStream` Hub.</span><span class="sxs-lookup"><span data-stu-id="3b93f-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="3b93f-206">Kód JavaScriptu může `subject.next` použít metodu pro zpracování řetězců, jak jsou zachyceny a připraveny k odeslání na server.</span><span class="sxs-lookup"><span data-stu-id="3b93f-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="3b93f-207">Pomocí kódu, jako jsou dva předchozí úryvky, lze vytvořit prostředí streamování v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="3b93f-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="3b93f-208">Nová serializace JSON</span><span class="sxs-lookup"><span data-stu-id="3b93f-208">New JSON serialization</span></span>

<span data-ttu-id="3b93f-209">ASP.NET Core 3.0 <xref:System.Text.Json> nyní používá ve výchozím nastavení pro serializaci JSON:</span><span class="sxs-lookup"><span data-stu-id="3b93f-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="3b93f-210">Čte a zapisuje JSON asynchronně.</span><span class="sxs-lookup"><span data-stu-id="3b93f-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="3b93f-211">Je optimalizován pro Text UTF-8.</span><span class="sxs-lookup"><span data-stu-id="3b93f-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="3b93f-212">Obvykle vyšší výkon `Newtonsoft.Json`než .</span><span class="sxs-lookup"><span data-stu-id="3b93f-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="3b93f-213">Chcete-li přidat Json.NET do ASP.NET Core 3.0, viz [Přidání podpory formátu JSON založeného na newtonsoftu.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="3b93f-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="3b93f-214">Nové direktivy břitvy</span><span class="sxs-lookup"><span data-stu-id="3b93f-214">New Razor directives</span></span>

<span data-ttu-id="3b93f-215">Následující seznam obsahuje nové direktivy Razor:</span><span class="sxs-lookup"><span data-stu-id="3b93f-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="3b93f-216">[`@attribute`](xref:mvc/views/razor#attribute)&ndash; Směrnice `@attribute` použije daný atribut pro třídu generované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="3b93f-217">Například, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="3b93f-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="3b93f-218">[`@implements`](xref:mvc/views/razor#implements)&ndash; Směrnice `@implements` implementuje rozhraní pro generované třídy.</span><span class="sxs-lookup"><span data-stu-id="3b93f-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="3b93f-219">Například, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="3b93f-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="3b93f-220">IdentityServer4 podporuje ověřování a autorizaci pro webová api a spa</span><span class="sxs-lookup"><span data-stu-id="3b93f-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="3b93f-221">ASP.NET Core 3.0 nabízí ověřování v jednostránkových aplikacích (SPA) pomocí podpory autorizace webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b93f-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="3b93f-222">ASP.NET základní identity pro ověřování a ukládání uživatelů je v kombinaci s [IdentityServer4](https://identityserver.io/) pro implementaci Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="3b93f-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="3b93f-223">IdentityServer4 je openid připojit a OAuth 2.0 framework pro ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b93f-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="3b93f-224">Umožňuje následující funkce zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="3b93f-224">It enables the following security features:</span></span>

* <span data-ttu-id="3b93f-225">Ověřování jako služba (AaaS)</span><span class="sxs-lookup"><span data-stu-id="3b93f-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="3b93f-226">Jednotné přihlašování/vypínání (SSO) přes více typů aplikací</span><span class="sxs-lookup"><span data-stu-id="3b93f-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="3b93f-227">Řízení přístupu pro api</span><span class="sxs-lookup"><span data-stu-id="3b93f-227">Access control for APIs</span></span>
* <span data-ttu-id="3b93f-228">Federační brána</span><span class="sxs-lookup"><span data-stu-id="3b93f-228">Federation Gateway</span></span>

<span data-ttu-id="3b93f-229">Další informace naleznete [v dokumentaci identityServer4](http://docs.identityserver.io/en/latest/index.html) nebo [ověřování a autorizace pro spa](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="3b93f-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="3b93f-230">Ověřování certifikátu a protokolu Kerberos</span><span class="sxs-lookup"><span data-stu-id="3b93f-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="3b93f-231">Ověření certifikátu vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="3b93f-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="3b93f-232">Konfigurace serveru pro přijímání certifikátů.</span><span class="sxs-lookup"><span data-stu-id="3b93f-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="3b93f-233">Přidání ověřovacího middlewaru do souboru `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3b93f-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="3b93f-234">Přidání služby ověřování `Startup.ConfigureServices`certifikátů do souboru .</span><span class="sxs-lookup"><span data-stu-id="3b93f-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="3b93f-235">Možnosti ověřování certifikátů zahrnují možnost:</span><span class="sxs-lookup"><span data-stu-id="3b93f-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="3b93f-236">Přijímejte certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="3b93f-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="3b93f-237">Zkontrolujte odvolání certifikátu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="3b93f-238">Zkontrolujte, zda je v nabízeném certifikátu správné příznaky použití.</span><span class="sxs-lookup"><span data-stu-id="3b93f-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="3b93f-239">Výchozí uživatelský objekt zabezpečení je vytvořen z vlastností certifikátu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="3b93f-240">Uživatelský objekt zabezpečení obsahuje událost, která umožňuje doplnění nebo nahrazení objektu zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="3b93f-241">Další informace naleznete v tématu <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="3b93f-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="3b93f-242">[Ověřování systému Windows](/windows-server/security/windows-authentication/windows-authentication-overview) bylo rozšířeno na Linux a macOS.</span><span class="sxs-lookup"><span data-stu-id="3b93f-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="3b93f-243">V předchozích verzích bylo ověřování systému Windows omezeno na [služby IIS](xref:host-and-deploy/iis/index) a [httpsys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="3b93f-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="3b93f-244">V ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) má možnost používat Vyjednat, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)a [NTLM na Windows](/windows-server/security/kerberos/ntlm-overview), Linux a macOS pro windows domény-připojen hostitelé.</span><span class="sxs-lookup"><span data-stu-id="3b93f-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="3b93f-245">Podpora kestrelu těchto schémat ověřování je poskytována balíčkem [Microsoft.AspNetCore.Authentication.Negotiate NuGet.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)</span><span class="sxs-lookup"><span data-stu-id="3b93f-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="3b93f-246">Stejně jako u ostatních ověřovacích služeb nakonfigurujte ověřovací aplikaci pro celou a pak nakonfigurujte službu:</span><span class="sxs-lookup"><span data-stu-id="3b93f-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="3b93f-247">Požadavky hostitele:</span><span class="sxs-lookup"><span data-stu-id="3b93f-247">Host requirements:</span></span>

* <span data-ttu-id="3b93f-248">Hostitelé systému Windows musí mít k uživatelskému účtu hostujícímu aplikaci [přidaná názvy hlavních](/windows/win32/ad/service-principal-names) názvů služeb (SPN).</span><span class="sxs-lookup"><span data-stu-id="3b93f-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="3b93f-249">Počítače s Linuxem a macOS musí být připojeny k doméně.</span><span class="sxs-lookup"><span data-stu-id="3b93f-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="3b93f-250">Hlavní aktualizace služeb při obslužné síti musí být vytvořeny pro webový proces.</span><span class="sxs-lookup"><span data-stu-id="3b93f-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="3b93f-251">[Soubory záložek klíčů](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) musí být generovány a nakonfigurovány v hostitelském počítači.</span><span class="sxs-lookup"><span data-stu-id="3b93f-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="3b93f-252">Další informace naleznete v tématu <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="3b93f-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="3b93f-253">Změny šablony</span><span class="sxs-lookup"><span data-stu-id="3b93f-253">Template changes</span></span>

<span data-ttu-id="3b93f-254">Webové šablony uživatelského uživatelského nastavení (Razor Pages, MVC s ovladačem a zobrazení) byly odstraněny následující:</span><span class="sxs-lookup"><span data-stu-id="3b93f-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="3b93f-255">UI souhlasu se soubory cookie již není zahrnuto.</span><span class="sxs-lookup"><span data-stu-id="3b93f-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="3b93f-256">Informace o povolení funkce souhlasu se soubory cookie v aplikaci ASP.NET aplikaci generovanou šablonou Core 3.0 naleznete v tématu <xref:security/gdpr>.</span><span class="sxs-lookup"><span data-stu-id="3b93f-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="3b93f-257">Skripty a související statické datové zdroje jsou nyní odkazovány jako místní soubory namísto použití souborů CDN.</span><span class="sxs-lookup"><span data-stu-id="3b93f-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="3b93f-258">Další informace naleznete v [tématu Skripty a související statické datové zdroje jsou nyní odkazovány jako místní soubory namísto použití sítí CDN na základě aktuálního prostředí (aspnet/AspNetCore.Docs #14350).](https://github.com/dotnet/AspNetCore.Docs/issues/14350)</span><span class="sxs-lookup"><span data-stu-id="3b93f-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="3b93f-259">Úhlová šablona byla aktualizována tak, aby používala úhlové 8.</span><span class="sxs-lookup"><span data-stu-id="3b93f-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="3b93f-260">Šablona třídy Razor (RCL) ve výchozím nastavení ve výchozím nastavení na vývoj komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="3b93f-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="3b93f-261">Nová možnost šablony v sadě Visual Studio poskytuje podporu šablon pro stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3b93f-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="3b93f-262">Při vytváření RCL ze šablony v příkazovém prostředí předavěte `--support-pages-and-views` možnost (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="3b93f-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="3b93f-263">Obecný hostitel</span><span class="sxs-lookup"><span data-stu-id="3b93f-263">Generic Host</span></span>

<span data-ttu-id="3b93f-264">Používají šablony ASP.NET Core 3.0 <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="3b93f-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="3b93f-265">Předchozí použité <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>verze .</span><span class="sxs-lookup"><span data-stu-id="3b93f-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="3b93f-266">Použití obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) poskytuje lepší integraci aplikací ASP.NET Core s jinými serverovými scénáři, které nejsou specifické pro web.</span><span class="sxs-lookup"><span data-stu-id="3b93f-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="3b93f-267">Další informace naleznete v tématu [HostBuilder nahrazuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="3b93f-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="3b93f-268">Konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="3b93f-268">Host configuration</span></span>

<span data-ttu-id="3b93f-269">Před vydáním ASP.NET Core 3.0 byly proměnné `ASPNETCORE_` prostředí s předponou načteny pro konfiguraci hostitele webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="3b93f-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="3b93f-270">V 3.0 `AddEnvironmentVariables` se používá k načtení `DOTNET_` proměnných prostředí `CreateDefaultBuilder`s předponou pro konfiguraci hostitele s .</span><span class="sxs-lookup"><span data-stu-id="3b93f-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="3b93f-271">Změny vstřikování konstruktoru startupu</span><span class="sxs-lookup"><span data-stu-id="3b93f-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="3b93f-272">Obecný hostitel podporuje pouze následující `Startup` typy pro vkládání konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="3b93f-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="3b93f-273">Všechny služby mohou být stále vloženy `Startup.Configure` přímo jako argumenty metody.</span><span class="sxs-lookup"><span data-stu-id="3b93f-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="3b93f-274">Další informace naleznete [v tématu Obecný hostitel omezuje vstřikování konstruktoru spuštění (aspnet/Oznámení #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="3b93f-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="3b93f-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="3b93f-275">Kestrel</span></span>

* <span data-ttu-id="3b93f-276">Konfigurace kestrelu byla aktualizována pro migraci na obecný hostitel.</span><span class="sxs-lookup"><span data-stu-id="3b93f-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="3b93f-277">V 3.0, Kestrel je konfigurován na tvůrce `ConfigureWebHostDefaults`webhost poskytované .</span><span class="sxs-lookup"><span data-stu-id="3b93f-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="3b93f-278">Adaptéry připojení byly odebrány z Kestrel a nahrazeny připojení middleware, který je podobný HTTP Middleware v kanálu ASP.NET Core, ale pro připojení nižší úrovně.</span><span class="sxs-lookup"><span data-stu-id="3b93f-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="3b93f-279">Transportní vrstva Kestrel byla vystavena `Connections.Abstractions`jako veřejné rozhraní v .</span><span class="sxs-lookup"><span data-stu-id="3b93f-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="3b93f-280">Nejednoznačnost mezi záhlavími a přívěsy byla vyřešena přesunutím koncových záhlaví do nové kolekce.</span><span class="sxs-lookup"><span data-stu-id="3b93f-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="3b93f-281">Synchronní vstupně-v., jako `HttpRequest.Body.Read`je například , jsou běžným zdrojem nedostatku podprocesů, což vede k selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b93f-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="3b93f-282">V 3.0 `AllowSynchronousIO` je ve výchozím nastavení zakázán.</span><span class="sxs-lookup"><span data-stu-id="3b93f-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="3b93f-283">Další informace naleznete v tématu <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="3b93f-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="3b93f-284">HTTP/2 ve výchozím nastavení povolena</span><span class="sxs-lookup"><span data-stu-id="3b93f-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="3b93f-285">HTTP/2 je ve výchozím nastavení povolen v Kestrel u koncových bodů HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3b93f-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="3b93f-286">Podpora protokolu HTTP/2 pro službu IIS nebo soubor HTTP.sys je povolena, pokud ji podporuje operační systém.</span><span class="sxs-lookup"><span data-stu-id="3b93f-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="3b93f-287">EventCounters na vyžádání</span><span class="sxs-lookup"><span data-stu-id="3b93f-287">EventCounters on request</span></span>

<span data-ttu-id="3b93f-288">Hosting EventSource, `Microsoft.AspNetCore.Hosting`, vydává následující <xref:System.Diagnostics.Tracing.EventCounter> nové typy související s příchozí požadavky:</span><span class="sxs-lookup"><span data-stu-id="3b93f-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="3b93f-289">Směrování koncového bodu</span><span class="sxs-lookup"><span data-stu-id="3b93f-289">Endpoint routing</span></span>

<span data-ttu-id="3b93f-290">Směrování koncových bodů, které umožňuje, aby rozhraní (například MVC) dobře fungovaly s middlewarem, je vylepšeno:</span><span class="sxs-lookup"><span data-stu-id="3b93f-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="3b93f-291">Pořadí middleware a koncové body je konfigurovatelné v `Startup.Configure`kanálu zpracování požadavků .</span><span class="sxs-lookup"><span data-stu-id="3b93f-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="3b93f-292">Koncové body a middleware dobře tvoří s dalšími ASP.NET technologiemi založenými na jádru, jako jsou kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="3b93f-293">Koncové body můžete implementovat zásady, jako je například CORS nebo autorizace, v middleware a MVC.</span><span class="sxs-lookup"><span data-stu-id="3b93f-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="3b93f-294">Filtry a atributy lze umístit na metody v řadičích.</span><span class="sxs-lookup"><span data-stu-id="3b93f-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="3b93f-295">Další informace naleznete v tématu <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="3b93f-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="3b93f-296">Kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="3b93f-296">Health Checks</span></span>

<span data-ttu-id="3b93f-297">Kontroly stavu používají směrování koncových bodů s obecným hostitelem.</span><span class="sxs-lookup"><span data-stu-id="3b93f-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="3b93f-298">V `Startup.Configure`aplikace `MapHealthChecks` volejte tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="3b93f-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="3b93f-299">Koncové body kontroly stavu mohou:</span><span class="sxs-lookup"><span data-stu-id="3b93f-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="3b93f-300">Zadejte jeden nebo více povolených hostitelů/portů.</span><span class="sxs-lookup"><span data-stu-id="3b93f-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="3b93f-301">Vyžadovat autorizaci.</span><span class="sxs-lookup"><span data-stu-id="3b93f-301">Require authorization.</span></span>
* <span data-ttu-id="3b93f-302">Vyžadovat CORS.</span><span class="sxs-lookup"><span data-stu-id="3b93f-302">Require CORS.</span></span>

<span data-ttu-id="3b93f-303">Další informace najdete v těchto článcích:</span><span class="sxs-lookup"><span data-stu-id="3b93f-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="3b93f-304">Kanály na httpcontext</span><span class="sxs-lookup"><span data-stu-id="3b93f-304">Pipes on HttpContext</span></span>

<span data-ttu-id="3b93f-305">Nyní je možné číst tělo požadavku a zapsat <xref:System.IO.Pipelines> tělo odpovědi pomocí rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3b93f-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="3b93f-306">Prostředek</span><span class="sxs-lookup"><span data-stu-id="3b93f-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="3b93f-307">`HttpRequest.BodyReader`vlastnost poskytuje, <xref:System.IO.Pipelines.PipeReader> které lze použít ke čtení těla požadavku.</span><span class="sxs-lookup"><span data-stu-id="3b93f-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="3b93f-308">Prostředek</span><span class="sxs-lookup"><span data-stu-id="3b93f-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="3b93f-309">`HttpResponse.BodyWriter`vlastnost poskytuje, <xref:System.IO.Pipelines.PipeWriter> které lze použít k zápisu těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3b93f-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="3b93f-310">`HttpRequest.BodyReader`je analogový `HttpRequest.Body` proud.</span><span class="sxs-lookup"><span data-stu-id="3b93f-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="3b93f-311">`HttpResponse.BodyWriter`je analogový `HttpResponse.Body` proud.</span><span class="sxs-lookup"><span data-stu-id="3b93f-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="3b93f-312">Vylepšené zasílání zpráv o chybách ve správě IIS</span><span class="sxs-lookup"><span data-stu-id="3b93f-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="3b93f-313">Chyby při spuštění při hostování aplikací ASP.NET Core ve službě IIS nyní vytvářejí bohatší diagnostická data.</span><span class="sxs-lookup"><span data-stu-id="3b93f-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="3b93f-314">Tyto chyby jsou hlášeny do protokolu událostí systému Windows s trasování zásobníku, pokud je to možné.</span><span class="sxs-lookup"><span data-stu-id="3b93f-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="3b93f-315">Kromě toho jsou všechna upozornění, chyby a neošetřené výjimky zaznamenány do protokolu událostí systému Windows.</span><span class="sxs-lookup"><span data-stu-id="3b93f-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="3b93f-316">Pracovní služba a sada SDK pracovníka</span><span class="sxs-lookup"><span data-stu-id="3b93f-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="3b93f-317">.NET Core 3.0 zavádí novou šablonu aplikace pracovní služby.</span><span class="sxs-lookup"><span data-stu-id="3b93f-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="3b93f-318">Tato šablona poskytuje výchozí bod pro zápis dlouhotrvajících služeb v rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b93f-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="3b93f-319">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3b93f-319">For more information, see:</span></span>

* [<span data-ttu-id="3b93f-320">Základní pracovníci rozhraní .NET jako služby systému Windows</span><span class="sxs-lookup"><span data-stu-id="3b93f-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="3b93f-321">Vylepšení middlewaru předávaných záhlaví</span><span class="sxs-lookup"><span data-stu-id="3b93f-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="3b93f-322">V předchozích verzích ASP.NET <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> Core, volání a byly problematické při nasazení na Azure Linux nebo za reverzní proxy než IIS.</span><span class="sxs-lookup"><span data-stu-id="3b93f-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="3b93f-323">Oprava předchozích verzí je popsána v části [Předat schéma pro linuxové a neisis reverzní proxy servery](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="3b93f-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="3b93f-324">Tento scénář je opraven v ASP.NET jádrem 3.0.</span><span class="sxs-lookup"><span data-stu-id="3b93f-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="3b93f-325">Hostitel povolí [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` pokud `true`je proměnná prostředí nastavena na .</span><span class="sxs-lookup"><span data-stu-id="3b93f-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="3b93f-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`je nastavena na `true` v našich nánosů.</span><span class="sxs-lookup"><span data-stu-id="3b93f-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="3b93f-327">Zlepšení výkonu</span><span class="sxs-lookup"><span data-stu-id="3b93f-327">Performance improvements</span></span>

<span data-ttu-id="3b93f-328">ASP.NET Core 3.0 obsahuje mnoho vylepšení, která snižují využití paměti a zlepšují propustnost:</span><span class="sxs-lookup"><span data-stu-id="3b93f-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="3b93f-329">Snížení využití paměti při použití integrovaného kontejneru vkládání závislostí pro služby s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="3b93f-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="3b93f-330">Snížení přidělení v rámci, včetně middleware scénáře a směrování.</span><span class="sxs-lookup"><span data-stu-id="3b93f-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="3b93f-331">Snížení využití paměti pro připojení WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3b93f-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="3b93f-332">Vylepšení snížení paměti a propustnost pro připojení HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3b93f-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="3b93f-333">Nový optimalizovaný a plně asynchronní serializátor JSON.</span><span class="sxs-lookup"><span data-stu-id="3b93f-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="3b93f-334">Snížení využití paměti a zlepšení propustnosti v parsování formulářů.</span><span class="sxs-lookup"><span data-stu-id="3b93f-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="3b93f-335">ASP.NET Core 3.0 běží pouze na rozhraní .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="3b93f-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="3b93f-336">Od ASP.NET jádra 3.0 rozhraní .NET Framework již není podporovanou cílovou architekturou.</span><span class="sxs-lookup"><span data-stu-id="3b93f-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="3b93f-337">Projekty zaměřené na rozhraní .NET Framework mohou pokračovat plně podporovaným způsobem pomocí [verze .NET Core 2.1 LTS](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="3b93f-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="3b93f-338">Většina ASP.NET balíčky související s jádrem 2.1.x budou podporovány neomezeně dlouho, po dobu tří LTS pro .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="3b93f-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="3b93f-339">Informace o migraci naleznete [v tématu Port kódu z rozhraní .NET Framework do jádra .NET](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="3b93f-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="3b93f-340">Použití sdíleného rámce ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b93f-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="3b93f-341">Sdílené rozhraní ASP.NET Core 3.0 obsažené v [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app)již `<PackageReference />` nevyžaduje explicitní prvek v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="3b93f-342">Sdílená architektura je automaticky `Microsoft.NET.Sdk.Web` odkazována při použití sady SDK v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="3b93f-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="3b93f-343">Sestavení odebraná ze sdíleného rozhraní ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b93f-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="3b93f-344">Nejvýznamnější sestavení odebraná ze sdíleného rámce ASP.NET Core 3.0 jsou:</span><span class="sxs-lookup"><span data-stu-id="3b93f-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="3b93f-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="3b93f-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="3b93f-346">Chcete-li přidat Json.NET do ASP.NET Core 3.0, viz [Přidání podpory formátu JSON založeného na newtonsoftu.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="3b93f-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="3b93f-347">ASP.NET Core 3.0 `System.Text.Json` zavádí pro čtení a psaní JSON.</span><span class="sxs-lookup"><span data-stu-id="3b93f-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="3b93f-348">Další informace naleznete v [tématu Nová serializace JSON](#new-json-serialization) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="3b93f-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="3b93f-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="3b93f-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="3b93f-350">Úplný seznam sestavení odebraných ze sdílené ho rozhraní naleznete v [tématu Sestavení odebírá z Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="3b93f-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="3b93f-351">Další informace o motivaci pro tuto změnu naleznete [v tématu Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="3b93f-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 